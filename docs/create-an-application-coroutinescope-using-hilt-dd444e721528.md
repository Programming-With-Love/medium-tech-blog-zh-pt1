# 使用 Hilt 创建一个应用程序协同作用域

> 原文：<https://medium.com/androiddevelopers/create-an-application-coroutinescope-using-hilt-dd444e721528?source=collection_archive---------1----------------------->

![](img/1f0df8d64c0e9ec9cdb2da020a7a7508.png)

遵循[协程的最佳实践](https://developer.android.com/kotlin/coroutines/coroutines-best-practices)，你可能需要在一些类中注入一个应用范围的`CoroutineScope`来[启动遵循应用生命周期的新协程，或者使某些工作超出调用者的范围](https://developer.android.com/kotlin/coroutines/coroutines-best-practices#create-coroutines-data-layer)。

在本文中，您将学习如何使用 Hilt 创建一个应用程序范围的`CoroutineScope`，以及如何将其作为一个依赖项注入。为了进一步改进我们使用协程的方式，我们将看看如何在测试中注入不同的`CoroutineDispatcher`并替换它们的实现。

# 手动依赖注入

要创建一个[应用程序范围的](/androiddevelopers/scoping-in-android-and-hilt-c2e5222317c0) `CoroutineScope`遵循依赖注入(DI)最佳实践[手动](https://developer.android.com/training/dependency-injection/manual)没有任何库，您通常会用一个`CoroutineScope`的实例向您的应用程序类添加一个新变量。创建其他对象时，将手动传递同一个实例。

由于在 Android 中没有可靠的方法知道`Application`何时被销毁，所以您不需要手动调用`applicationScope.cancel()`，因为当应用程序进程结束时，作用域和所有正在进行的工作都将被销毁。

手动完成这项工作的一个更好的选择是创建一个保存应用程序范围类型的`ApplicationContainer`类。这有助于分离关注点，因为这些*容器*类负责:

*   处理逻辑*如何*构建某些类型，
*   保存容器范围的类型实例，以及
*   返回限定范围和未限定范围类型的实例。

> **注意**:容器总是返回作用域类型的同一个实例，并且总是返回未作用域类型的*不同的*实例。将类型作用于容器的代价是很高的，因为作用域对象在组件被破坏之前一直留在内存中，所以只作用于真正需要的对象。

在上面的`ApplicationDiContainer`例子中，所有类型都被限定了范围。如果不需要将 MyRepository 限定在应用程序的范围内，我们应该:

# 在你的应用程序中使用 Hilt

希尔特生成你能在`ApplicationDiContainer`里看到的东西(还有更多！)在编译时使用注释。此外，Hilt 为大多数 Android 框架类提供容器，而不仅仅是 T2 类。

要在你的应用程序中设置 Hilt 并为`Application`类创建容器，用`@HiltAndroidApp`注释你的`Application`类。

这样，应用程序 DI 容器就可以使用了。我们只需要让 Hilt 知道如何提供不同类型的实例。

> **注**:在 Hilt 中，容器类被引用为*组件*。与`Application`类相关的容器被称为`SingletonComponent`。查看所有可用刀柄组件的[列表。](https://developer.android.com/training/dependency-injection/hilt-android#generated-components)

# 建筑注射

如果我们可以访问一个类的构造函数，构造注入是让 Hilt 知道如何提供一个类型的实例的最简单的方法，因为我们只需要用`@Inject`注释构造函数:

这让 Hilt 知道，为了提供一个`MyRepository`类的实例，需要将一个`CoroutineScope`的实例作为依赖项传递。Hilt 在编译时生成代码，以确保在创建一个类型的实例时满足并传递依赖关系，或者在没有足够信息的情况下给出错误。`@Singleton`用于将该类的范围扩大到`SingletonContainer`。

此时，Hilt 不知道如何满足`CoroutineScope`依赖性，因为我们还没有告诉 Hilt 如何去做。下面几节将解释我们如何让 Hilt 知道传递什么作为依赖项。

> **注**:对于不同的刀柄可用组件，刀柄提供了不同的作用域类型注释。查看所有可用组件范围的[列表](https://developer.android.com/training/dependency-injection/hilt-android#component-scopes)。

# 粘合剂

一个*绑定*是一个在 Hilt 中常用的术语，表示**信息** Hilt 知道如何提供一个类型的实例作为依赖。我们可以说，我们用上面代码片段的`@Inject`注释添加了一个到 Hilt 的绑定。

绑定流过[刀柄的组件层次](https://developer.android.com/training/dependency-injection/hilt-android#component-hierarchy)。在`SingletonComponent`中可用的绑定在`ActivityComponent`中也可用。

未分类类型的绑定(一个例子可能是上面的`MyRepository`代码，如果它没有用`@Singleton`注释的话)，在*所有*手柄组件中都可用。作用于组件的绑定，比如用`@Singleton`注释的`MyRepository`，对于作用域组件和层次结构中低于它的组件都是可用的。

# 为类型提供模块

如上所述，我们需要让 Hilt 知道如何满足`CoroutineScope`依赖。然而，`CoroutineScope`是一个来自外部库的接口类型，所以我们不能像以前使用`MyRepository`类那样使用构造函数注入。另一种方法是让 Hilt 知道在使用模块提供类型[的实例时运行什么代码:](https://developer.android.com/training/dependency-injection/hilt-android#hilt-modules)

`[@Provides](https://developer.android.com/training/dependency-injection/hilt-android#inject-provides)`[方法](https://developer.android.com/training/dependency-injection/hilt-android#inject-provides)用`@Singleton`注释，使句柄*总是*返回那个`CoroutineScope`的同一个实例。这是因为任何需要遵循应用程序生命周期的工作都应该使用遵循`Application`生命周期的`CoroutineScope`的同一个实例来创建。

句柄模块用`@InstallIn`标注，表示绑定安装在哪个句柄组件(以及层次结构中的下一个组件)中。在我们的例子中，由于`MyRepository`需要应用程序`CoroutineScope`，而应用程序的作用域是`SingletonComponent`，所以这个绑定也需要安装在`SingletonComponent`中。

用 Hilt 术语来说，我们可以说我们添加了一个`CoroutineScope`绑定，因为现在，Hilt 知道如何提供`CoroutineScope`的实例。

然而，上面的代码片段还可以改进。[硬编码调度程序在协程](https://developer.android.com/kotlin/coroutines/coroutines-best-practices#inject-dispatchers)中是一种不好的做法，我们应该将它们注入**中，使它们可配置并使测试更容易**。按照前面的代码，我们可以创建一个新的 Hilt 模块，让它知道为每种情况注入哪个调度程序:main、default 和 IO。

# 为 CoroutineDispatcher 提供实现

我们必须为同一类型提供不同的实现:`CoroutineDispatcher`。换句话说，我们需要相同类型的不同绑定。

我们使用 [*限定词*](https://developer.android.com/training/dependency-injection/hilt-android#multiple-bindings) 来让 Hilt 知道每次使用哪个绑定或实现。限定符只是您和 Hilt 用来标识特定绑定的注释。让我们为每个`CoroutineDispatcher`实现创建一个限定符:

然后，这些限定符对不同的`@Provides`方法进行注释，以识别 Hilt 模块中的特定绑定。`@DefaultDispatcher`限定符注释了返回默认调度程序的方法，依此类推。

注意，这些`CoroutineDispatchers`不需要限定在`SingletonComponent`的范围内。每次需要这些依赖时，Hilt 调用`@Provides`方法，返回对应的`CoroutineDispatcher`。

# 提供应用程序范围的协同作用域

为了从我们之前的应用程序范围的`CoroutineScope`代码中去掉硬编码的`CoroutineDispatcher`，我们需要注入 Hilt 提供的默认调度程序。为此，我们可以传递我们想要注入的类型`CoroutineDispatcher`，使用相应的限定符`@DefaultDispatcher`，作为提供应用程序`CoroutineScope`的方法中的依赖项。

由于 Hilt 对`CoroutineDispatcher`类型有多个绑定，当`CoroutineDispatcher`被用作依赖项时，我们使用`@DefaultDispatcher`注释来消除它的歧义。

# 应用范围的限定符

尽管目前我们不需要对`CoroutineScope`进行多重绑定(如果我们需要像`UserCoroutineScope`这样的东西，这种情况可能会在将来发生变化)，但是在将应用程序`CoroutineScope`作为依赖项注入时，向它添加一个限定符有助于提高可读性。

因为`MyRepository`依赖于这个作用域，所以使用哪个外部作用域作为实现是非常清楚的:

# 为仪器测试更换调度程序

我们之前说过，我们应该注入调度程序，以使测试更容易，并完全控制发生的事情。对于仪器测试，我们想让 Espresso 等待协程完成。

我们可以利用 [AsyncTask](https://developer.android.com/reference/android/os/AsyncTask) API，而不是用一些 [Espresso 空闲资源](https://developer.android.com/training/testing/espresso/idling-resource)创建一个定制`CoroutineDispatcher`来等待协程完成。即使 AsyncTask 在 Android API 30 中被弃用，Espresso 也会挂钩到它的线程池来检查空闲。因此，任何应该在后台线程中执行的协程都可以在 AsyncTask 的线程池中执行。

使用 Hilt 的`TestInstallIn` API 让 Hilt 在测试中提供一个类型的不同实现。类似于上面我们提供不同调度程序的方式，我们可以在`androidTest`包下创建一个新文件，为这些调度程序提供不同的实现。

通过上面的代码，我们在测试中让 Hilt“忘记”生产代码中使用的`CoroutinesDispatchersModule`。该模块将被替换为`TestCoroutinesDispatchersModule`，它使用异步任务的线程池来处理需要在后台发生的工作，而`Dispatchers.Main`则处理需要在主线程上发生的工作，Espresso 也在等待主线程。

> **警告**:这个实现显然是一个我们并不引以为豪的黑客。然而，协程程序目前并没有很好地与 Espresso 集成，因为没有办法知道`CoroutineDispatcher`此刻是否空闲(链接到 bug )。`AsyncTask.THREAD_POOL_EXECUTOR`是目前最好的选择，因为 Espresso 不使用空闲资源来检查这个执行器是否空闲，Espresso 使用不同的启发式算法来考虑消息队列中的内容。这使得它比类似于`[IdlingThreadPoolExecutor](https://developer.android.com/reference/androidx/test/espresso/idling/concurrent/IdlingThreadPoolExecutor)`的东西更好，不幸的是，由于协程如何被编译到状态机，当协程被挂起时，它认为线程池是空闲的。

有关测试的更多信息，请查看[刀柄的测试指南](https://developer.android.com/training/dependency-injection/hilt-testing)。

在本文中，您学习了如何使用 Hilt 创建一个应用程序范围的`CoroutineScope`，将其作为依赖注入，注入不同的`CoroutineDispatcher`实例，并在测试中替换它们的实现。