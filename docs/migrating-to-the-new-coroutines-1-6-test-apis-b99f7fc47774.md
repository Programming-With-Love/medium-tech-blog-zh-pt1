# 迁移到新的协程 1.6 测试 API

> 原文：<https://medium.com/androiddevelopers/migrating-to-the-new-coroutines-1-6-test-apis-b99f7fc47774?source=collection_archive---------5----------------------->

![](img/c280add9ed9ca9f364efa932861a872b.png)

`[kotlinx.coroutines](https://github.com/Kotlin/kotlinx.coroutines/releases/tag/1.6.0)` [1.6](https://github.com/Kotlin/kotlinx.coroutines/releases/tag/1.6.0) 引入了一组新的测试 API，之前的测试 API 现在已经被弃用。使用旧的 API 将很快产生弃用错误，它们计划在 2022 年底左右被完全移除。

我们最近发布了一个关于如何使用新的测试 API 的[指南，详细解释了它们是如何工作的。在这篇文章中，我们将通过查看我们如何迁移我们自己的一些示例来关注从旧 API 的迁移。你可以在这篇文章的末尾找到链接来查看完整的差异。](https://developer.android.com/kotlin/coroutines/test)

我们采取的迁移步骤应该涵盖了大多数 Android 项目的许多必要工作。如果您发现这些对您的项目来说还不够，您可以看看 JetBrains 的详细的[迁移指南](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-test/MIGRATION.md)，它也涵盖了测试 API 的高级用法。

## **从运行测试开始**

让我们从新测试 API 的入口点开始，`[runTest](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/run-test.html)`协程构建器。这取代了旧 API 中的`runBlockingTest`,它可以作为顶级函数被调用，但是它也经常在测试范围、测试调度程序或者测试规则中被调用。

我们已经用对顶级`runTest`函数的调用替换了所有这些:

如果你还没有使用表达式体(直接从函数返回`runTest`的结果),现在也是采用这个约定的好时机！这对于一致性来说很好，如果您在多平台项目中使用带有 KotlinJS 目标的协程测试 API，这是必需的。

在一些高级情况下，您可能仍然希望[创建自己的](https://developer.android.com/kotlin/coroutines/test#creating-your-own-testscope) `[TestScope](https://developer.android.com/kotlin/coroutines/test#creating-your-own-testscope)`，但是大多数测试只需要单独调用`runTest`。

## **处理主线程**

由于 Android UI 线程在单元测试中不可用，任何依赖于`Main` dispatcher 的测试都需要在测试期间用一个`[TestDispatcher](https://developer.android.com/kotlin/coroutines/test#testdispatchers)`实现来替换它。你可以像其他调度器一样[注入，或者用](https://developer.android.com/kotlin/coroutines/test#injecting-test-dispatchers)`[Dispatchers.setMain](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/set-main.html)`替换。使用`setMain`以静态的方式取代了调度程序，这意味着您可以在测试中使用依赖于硬编码`Main`调度程序的构造，比如`[viewModelScope](/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471)`。

一种常用的方法是将替换`Main`的代码放入一个可重用的 [JUnit 测试规则](https://junit.org/junit4/javadoc/4.12/org/junit/rules/TestRule.html)(或者对于 JUnit 5，一个测试扩展)。你可以在 iosched 项目中看到这样一个规则的例子。如果您使用旧的 API 有这样的规则，请按如下方式更新它:

然后，像这样使用规则，作为测试类的一个属性(与以前没有变化):

## **更加渴望:收集流量**

测试通常会启动新的协同程序从流中收集值。这些测试倾向于依赖这些急切启动的新协程，以便无论何时流发出一个值，收集器都已经准备好处理它。

当`runBlockingTest`急切地启动在测试中创建的新协程时，`runTest`却懒洋洋地启动它们，因为默认情况下它使用一个`[StandardTestDispatcher](https://developer.android.com/kotlin/coroutines/test#standardtestdispatcher)`用于测试协程。

为了让测试中的流收集协程再次急切地启动，创建一个新的`UnconfinedTestDispatcher`，并将其传递给创建收集协程的构建器:

注意，在这个代码片段中，创建了一个新的`TestDispatcher`,而没有显式地传入调度程序。只有当`[Main](https://developer.android.com/kotlin/coroutines/test#setting-main-dispatcher)` [调度程序已经被`TestDispatcher`取代](https://developer.android.com/kotlin/coroutines/test#setting-main-dispatcher)时，这样做才是安全的，这使得调度程序共享是自动的。否则，您必须将现有的调度程序传递给您创建的任何`TestDispatchers`:

同样值得记住的是，显式调用 collect 并不是收集流的唯一方法，其他方法如`[Flow.toList()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/to-list.html)`也在内部收集流。如果您正在使用这样的方法，您可能还想在以`UnconfinedTestDispatcher`开头的新协程中调用它们。

## **不要急于求成:主调度员执行**

正如您在上面的`MainDispatcherRule`实现中看到的，我们默认使用`UnconfinedTestDispatcher`让`Main`调度程序急切地启动协程。这在测试视图模型时很有用，当从主线程调用时，在生产代码中使用`Dispatchers.Main.immediate`会有类似的急切行为。

然而，[我们样本中的一些测试](https://github.com/android/architecture-samples/blob/f042c781a6cb959426c4606160cf9d2da50eb045/app/src/test/java/com/example/android/architecture/blueprints/todoapp/statistics/StatisticsViewModelTest.kt#L102-L118)需要`Main` dispatcher 协程的惰性调度。通常，这将用于需要断言 ViewModel 的中间加载状态的测试，其中急切地启动数据加载协程将意味着测试只能观察最终的加载状态。对于旧的 API，这些测试使用`pauseDispatcher`来防止新的协程过早执行，就像这样:

为了对新的 API 执行相同的测试，需要将`Main` dispatcher 设置为`StandardTestDispatcher`，因此我们需要一个不同于规则默认使用的`TestDispatcher`类型。由于用于`MainDispatcherRule`的`TestDispatcher`的类型影响测试类中的所有测试，我们有两个选择:

*   根据每个测试所需的`Main`调度程序的类型，将测试分成两个测试类，在两个测试类中使用不同类型调度程序的规则，或者
*   继续使用单个类，规则总是为`Main`设置一个`UnconfinedTestDispatcher`，然后在一些需要不同类型的测试中覆盖`Main` dispatcher 的类型。

我们选择了后一种解决方案，通过用新的`StandardTestDispatcher`替换`Main`中已经被替换的`TestDispatcher`来开始这些测试，从而在`Main`上延迟启动新的协程。然后，在稍后的测试代码中，当我们用旧的 API 调用`resumeDispatcher`时，我们可以通过使用`advanceUntilIdle`来改进这些协程。

这种方法将属于同一个测试类的测试保留在一起，使得代码库更容易导航，代价是一些测试必须包含额外的代码来用期望类型的`TestDispatcher`替换`Main`。

## 清理那个清理代码

最后，做一些快速整理。iosched 样本有一些[测试代码，这些代码在测试结束之前明确地等待](https://github.com/google/iosched/blob/69db6ea7772093fc286df5d1f317aff8f3b02c5d/mobile/src/test/java/com/google/samples/apps/iosched/ui/feed/FeedViewModelTest.kt#L104-L105)协程在`TestCoroutineDispatcher`上完成:

然而，`runTest`自动等待所有已知的协同程序，包括测试协同程序的子进程和任何运行在`TestDispatchers`上的协同程序。这意味着您可以删除任何等待一些松散协程完成的清理代码！

## **总结**

这些迁移步骤应该让您基本上能够使用新的协程测试 API。如需了解更多信息，您可以查看我们在样本中所做的所有更改:

*   [丢失公关](https://github.com/google/iosched/pull/404)(还有一个小[后续](https://github.com/google/iosched/pull/428))
*   [建筑-样品公关](https://github.com/android/architecture-samples/pull/825)
*   [跟踪公关](https://github.com/android/trackr/pull/45)

当然，Android 示例应用中的新[已经使用了新的测试 API 进行测试！](https://github.com/android/nowinandroid)

最后，如果您在迁移方面需要更多帮助，请查看 JetBrains 的官方[迁移指南](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-test/MIGRATION.md)，其中涵盖了复杂的协程测试 API。