# 重复生命周期 API 设计故事

> 原文：<https://medium.com/androiddevelopers/repeatonlifecycle-api-design-story-8670d1a7d333?source=collection_archive---------0----------------------->

![](img/67652710350970bedb352a287891992d.png)

在这篇博文中，你将了解到`Lifecycle.repeatOnLifecycle` API 背后的设计决策，以及*为什么*我们删除了在 2.4.0 `[lifecycle-runtime-ktx](https://developer.android.com/jetpack/androidx/releases/lifecycle)`库的第一个 alpha 版本中添加的一些助手函数。

在这个过程中，您将看到在某些场景中使用某些协同程序 API 是多么危险，命名是多么困难，以及为什么我们决定只在库中保留低级的 suspend APIs。

此外，您将意识到所有的 API 决策都需要在复杂性、可读性以及 API 的易错性方面进行一些权衡。

特别感谢 [Adam Powell](https://twitter.com/adamwp) 、 [Wojtek Kaliciński](https://twitter.com/wkalic) 、 [Ian Lake](https://twitter.com/ianhlake) 和 [Yigit Boyar](https://twitter.com/yigitboyar) 提供反馈并讨论这些 API 的形式。

> **注:**如果你正在寻找`repeatOnLifecycle`的指导，请查看[一个从 Android 用户界面博客文章](/androiddevelopers/a-safer-way-to-collect-flows-from-android-uis-23080b1f8bda)收集流量的更安全的方法。

# 重复生命周期

`Lifecycle.repeatOnLifecycle` API 的诞生主要是为了允许从 Android 的 UI 层进行更安全的流量收集。它的可重启行为考虑到了 UI 的生命周期，使得它成为完美的缺省 API，只有当 UI 在屏幕上可见时才处理项目。

> **注:** `LifecycleOwner.repeatOnLifecycle`也可用。它将功能委托给它的生命周期。这样，任何已经是`LifecycleOwner`作用域一部分的代码都可以省略显式接收器。

`**repeatOnLifecycle**` **是暂停功能**。因此，它需要在协程内*执行。`repeatOnLifecycle`挂起调用的协程，然后运行给定的挂起`block`，每次给定的生命周期达到目标状态或更高状态时，将它作为参数传递给新的协程。如果生命周期状态低于目标，为`block`启动的协程被取消。最后，`repeatOnLifecycle`函数本身不会恢复调用协程，直到生命周期到达`DESTROYED`。*

让我们看看这个 API 的运行情况。如果你读过我之前的 [*一个更安全的从 Android 用户界面收集流量的方法*博客文章](/androiddevelopers/a-safer-way-to-collect-flows-from-android-uis-23080b1f8bda)，这些都不会让你感到惊讶。

> **注意:**如果你对`repeatOnLifecycle`是如何实现的感兴趣，这里有一个[到其源代码](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:lifecycle/lifecycle-runtime-ktx/src/main/java/androidx/lifecycle/RepeatOnLifecycle.kt;l=63)的链接。

# 为什么它是一个挂起函数

**暂停功能是重启行为的最佳选择**，因为它保留了调用上下文。它*尊重*调用协程的`Job`树。由于`repeatOnLifecycle`的实现在幕后使用了`suspendCancellableCoroutine`，所以它配合了取消:取消调用的协程也取消了`repeatOnLifecycle`及其重启`block`。

同样，我们可以在`repeatOnLifecycle`之上添加更多的 API，比如`[Flow.flowWithLifecycle](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:lifecycle/lifecycle-runtime-ktx/src/main/java/androidx/lifecycle/FlowExt.kt;l=87)`流操作符。更重要的是，如果您的项目需要的话，它还允许您在这个 API 之上创建助手函数。这就是我们试图用`LifecycleOwner.addRepeatingJob` API 做的事情，我们在`lifecycle-runtime-ktx:2.4.0-alpha01`中添加了这个 API，事实上，在`alpha02`中删除了这个 API。

# 删除 addRepeatingJob API

第一个 alpha 版本的库中添加了具有此功能的`LifecycleOwner.addRepeatingJob` API，现在已从库中移除，实现如下:

给定一个`LifecycleOwner`，您可以运行一个 suspend 块，每当它的生命周期移入和移出目标状态时，它就会重新启动。这个 API 使用`LifecycleOwner`的`lifecycleScope`来触发一个新的协程，并在其中调用`repeatOnLifecycle`。

使用`addRepeatingJob` API 时，上面的代码如下所示:

乍一看，您可能认为这段代码更干净，需要的代码更少。然而，如果你不密切注意，有一些隐藏的陷阱会让你搬起石头砸自己的脚:

*   尽管`addRepeatingJob`使用了一个挂起块，但是`addRepeatingJob`不是而是**一个挂起函数。因此，您不应该在协程内部调用它！！！**
*   代码少？您只节省了一行代码，代价是拥有一个更容易出错的 API。

第一点可能看起来很明显，但它总是困扰着开发者。具有讽刺意味的是，它实际上是基于协程中最核心的概念之一: [**结构化并发**](https://elizarov.medium.com/structured-concurrency-722d765aa952) 。

`addRepeatingJob`不是一个挂起函数，因此，默认情况下不支持结构化并发(注意，您可以通过使用它作为参数的`coroutineContext`手动使它支持它)。因为`block`参数是一个 suspend lambda，所以您将这个 API 与协程相关联，并且您可以很容易地编写像这样的危险代码:

这个代码有什么问题？`addRepeatingJob`协程可以填充，我可以在协程中调用它，对吗？

由于`addRepeatingJob`使用`lifecycleScope`创建新的协同程序来运行重复块，这在实现细节中是隐含的，新的协同程序不考虑结构化并发性，也不保留调用协同程序的上下文。因此，当您调用`job.cancel()`时不会被取消。**这会导致你的应用程序出现非常细微的错误，很难调试。**

# 重复生命周期 FTW

在`addRepeatingJob`中使用的隐式`CoroutineScope`使得 API 在某些情况下不安全。这是一个隐藏的问题，需要格外注意才能写出正确的代码。这一点是反复出现的论点，以避免在库中的`repeatOnLifecycle`之上增加额外的包装 API。

suspend `repeatOnLifecycle` API 的主要好处是它默认与结构化并发协作，而`addRepeatingJob`没有。它还有助于您考虑希望重复作业在哪个范围内发生。该 API 不言自明，符合开发人员的期望:

*   像任何其他挂起函数一样，它将挂起协程的执行，直到有事情发生。在这种情况下，直到生命周期被破坏。
*   没有惊喜！它可以与其他协程代码结合使用，并且会像您预期的那样运行。
*   围绕`repeatOnLifecycle`的代码对于新手来说是可读且有意义的:*“首先，我启动了一个遵循 UI 生命周期的新协程。然后，我调用 repeatOnLifecycle，每当 UI 到达这个生命周期状态时，它就启动这个块"*。

# Flow.flowWithLifecycle

`Flow.flowWithLifecycle`操作符(这里的[实现](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:lifecycle/lifecycle-runtime-ktx/src/main/java/androidx/lifecycle/FlowExt.kt;l=87))构建在`repeatOnLifecycle`之上，并且仅在生命周期至少达到`minActiveState`时发出上游流发送的项目。

尽管这个 API 也有一些需要注意的问题，但我们决定保留它，因为它作为流操作符很有用。例如，可以在 Jetpack Compose 中轻松使用[。尽管您可以通过使用](https://manuelvivo.dev/coroutines-addrepeatingjob#safe-flow-collection-in-jetpack-compose)`[produceState](https://developer.android.com/jetpack/compose/side-effects#producestate)`和`repeatOnLifecycle` API 在 Compose 中实现相同的功能，但是我们将这个 API 留在了库中，作为一种更具反应性的方法的替代方案。

正如 KDoc 中所记录的，添加`flowWithLifecycle`操作符的顺序很重要。在之前*应用的操作员，当生命周期低于`minActiveState`时`flowWithLifecycle`操作员将被取消。但是，在*之后应用*的操作员即使没有发送物品也不会被取消。*

对于最好奇的人来说，这个 API 名称将`Flow.flowOn(CoroutineContext)`操作符作为一个先例，因为`Flow.flowWithLifecycle`改变了用于收集上游流量的`CoroutineContext`，同时使下游不受影响。

# 我们应该增加一个额外的 API 吗？

假设我们已经有了`Lifecycle.repeatOnLifecycle`、`LifecycleOwner.repeatOnLifecycle`和`Flow.flowWithLifecycle`API。我们应该添加任何其他 API 吗？

新的 API 带来的混乱和它们解决的问题一样多。有多种方式支持不同的用例，最短路径很大程度上取决于周围的代码。可能适用于您的项目，可能不适用于其他人。

这就是为什么我们不想为所有可能的情况提供 API，可用的 API 越多，开发者知道时*用什么*用什么*就越混乱。因此，我们决定只保留最底层的 API。有时候，少即是多。*

# 命名很重要(也很困难)

不仅仅是关于我们支持哪些用例，还有，如何命名它们！名称应该符合开发人员的期望，并遵循 Kotlin 协程惯例。例如:

*   如果 API 使用一个隐式的`CoroutineScope`(例如在`addRepeatingJob`中隐式使用的`lifecycleScope`)启动一个新的协同程序，这必须反映在名字中以避免错误的期望！在这种情况下，`launch`应该以某种方式包含在名称中。
*   `collect`是暂停功能。如果不是一个挂起函数，不要在 API 名称前加前缀`collect`。

> **注意:** Compose 的`[collectAsState](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#(kotlinx.coroutines.flow.StateFlow).collectAsState(kotlin.coroutines.CoroutineContext))` API 是一个特例，我们可以接受它的名字。不能将它与挂起函数混淆，因为在 Compose 中没有`@Composable suspend fun`这样的东西。

甚至连`LifecycleOwner.addRepeatingJob` API 都很难命名。因为它用`lifecycleScope`创建了新的协程，所以应该用`launch`作为前缀。然而，我们希望将协程在幕后使用的事实分开，因为它添加了一个新的生命周期观察者，这个名称与其他的`LifecycleOwner`API 更加一致。

这个名字也多少受到了现有的`[LifecycleCoroutineScope.launchWhenX](https://developer.android.com/reference/kotlin/androidx/lifecycle/LifecycleCoroutineScope)`暂停 API 的影响。由于`launchWhenStarted`和`repeatOnLifecycle(STARTED)`提供完全不同的功能(`launchWhenStarted`暂停协程的执行，而`repeatOnLifecycle`取消并重启一个新的协程)，如果新 API 的名称相似(例如，使用`launchWhenever`来重启 API)，开发人员可能会混淆，甚至会在没有注意到的情况下互换使用它们。

# 单线流集合

`LiveData`的`observe`功能具有生命周期意识，仅在生命周期开始时处理排放。如果您要从 LiveData 迁移到 Kotlin 流，您可能会认为用一行代码替换是个好主意！您可以删除样板代码，移植是简单的。

因此，你可以像伊恩·莱克第一次尝试 API 时所做的那样去做。他创建了一个名为`collectIn`的便利包装器，如下所示(为了遵循上面讨论的命名约定，我将其重命名为`launchAndCollectIn`):

所以你可以像这样从用户界面调用它:

这个包装器，在这个例子中看起来很好很简单，但是也有我们之前提到的关于`LifecycleOwner.addRepeatingJob`的问题。它不尊重调用上下文，在其他协程中使用可能很危险。再者，原来的名字真的有误导性:`collectIn`不是 suspend 函数！如前所述，开发者希望`collect`功能暂停。也许，这个包装器更好的名字是`Flow.launchAndCollectIn`，以防止不良使用。

# 包装丢失

`repeatOnLifecycle`必须与片段中的`viewLifecycleOwner`一起使用。在开源 Google I/O 应用程序中， [iosched](https://github.com/google/iosched) 项目，团队决定创建一个包装器，以避免片段中 API 的错误使用，该片段具有非常明确的 API 名称:`[Fragment.launchAndRepeatWithViewLifecycle](https://github.com/google/iosched/blob/main/mobile/src/main/java/com/google/samples/apps/iosched/util/UiUtils.kt#L60)`。

> **注意:**实现非常类似于`addRepeatingJob` API。当使用库的`alpha01`版本编写时，在`alpha02`中添加的`repeatOnLifecycle` API lint 检查还没有到位。

# 你需要包装纸吗？

如果您需要在`repeatOnLifecycle` API 之上创建包装器来适应您的应用程序中最常见的用例，问问自己是否真的需要它，以及为什么需要它。如果您确信并且想要继续，我建议您选择一个非常明确的 API 名称来清楚地定义包装器的行为，以避免误用。此外，非常清楚地记录它，以便新来者可以完全理解使用它的含义。

我希望这篇博客文章能让你了解团队在决定如何处理`repeatOnLifecycle`API 和我们可以在其上添加的潜在助手方法时的考虑。

再次感谢[亚当·鲍威尔](https://twitter.com/adamwp)、[沃伊泰克·卡利斯基](https://twitter.com/wkalic)、[伊恩·莱克](https://twitter.com/ianhlake)和[伊吉特·博雅](https://twitter.com/yigitboyar)给出反馈并讨论这些 API 的形状。