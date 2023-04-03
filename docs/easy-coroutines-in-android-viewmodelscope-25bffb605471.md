# Android 中的简单协同程序:视图模型范围

> 原文：<https://medium.com/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471?source=collection_archive---------0----------------------->

![](img/1fd9f5c7b4ee89f90326151046f4afbe.png)

*Illustration by* [*Virginia Poltrack*](https://twitter.com/vpoltrack)

当不再需要协程时，取消它们是一项容易忘记的任务，这是一项单调的工作，并且增加了许多样板代码。`viewModelScope`通过向 ViewModel 类添加一个[扩展属性](https://kotlinlang.org/docs/reference/extensions.html#extension-properties)来为[结构化并发](https://kotlinlang.org/docs/reference/coroutines/basics.html#structured-concurrency)做出贡献，该扩展属性在 ViewModel 被销毁时自动取消其子协程。

# 视图模型中的范围

一个[协程作用域](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/)跟踪它创建的所有协程。因此，如果取消一个作用域，就会取消它创建的所有协程。如果您在[视图模型](https://developer.android.com/topic/libraries/architecture/viewmodel)中运行协程，这一点尤其重要。如果您的 ViewModel 被破坏，那么它可能正在做的所有异步工作都必须停止。否则，您将浪费资源并可能泄漏内存。如果您认为某些异步工作应该在 ViewModel 销毁后继续，那是因为它应该在应用程序架构的较低层完成。

通过使用您在`onCleared()`方法中取消的 [SupervisorJob](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-supervisor-job.html) 创建一个新的作用域，向您的 ViewModel 添加一个协程作用域。只要 ViewModel 还在使用，用这个作用域创建的协程就会一直存在。请参见以下代码:

如果视图模型被破坏，后台发生的繁重工作将被取消，因为协程是由那个特定的`uiScope`启动的。

但是每个视图模型中都包含大量代码，对吗？`**viewModelScope**`来简化这一切。

# viewModelScope 意味着更少的样板代码

[AndroidX life cycle v 2 . 1 . 0](https://developer.android.com/jetpack/androidx/releases/lifecycle)向 ViewModel 类引入了扩展属性`viewModelScope`。它管理协程的方式与我们在上一节中所做的一样。这段代码被简化为:

所有的协同作用域设置和取消都为我们完成了。要使用它，请在您的`build.gradle`文件中导入以下依赖项:

```
implementation "androidx.lifecycle.lifecycle-viewmodel-ktx$lifecycle_version"
```

让我们来看看引擎盖下发生了什么。

# 深入查看视图模型范围

代码是[公开可用的](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:lifecycle/lifecycle-viewmodel-ktx/src/main/java/androidx/lifecycle/ViewModel.kt)。`viewModelScope`实现如下:

ViewModel 类有一个`ConcurrentHashSet`属性，可以存储任何类型的对象。协程示波器存放在那里。如果我们看一下代码，方法`getTag(JOB_KEY)`试图从那里检索范围。如果它不存在，那么它会像我们之前一样创建一个新的协同作用域，并将标签添加到包中。

当 ViewModel 被清除时，它在调用`onCleared()`方法之前执行`clear()`方法，否则我们将不得不覆盖这个方法。在`clear()`方法中，ViewModel 取消了`viewModelScope.`的工作[完整的 ViewModel 代码也是可用的](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:lifecycle/lifecycle-viewmodel/src/main/java/androidx/lifecycle/ViewModel.java)，但是我们只关注我们感兴趣的部分:

该方法遍历包中的所有项目，并调用`closeWithRuntimeException`来检查对象是否属于类型`Closeable` ，如果是，则关闭它。为了让 ViewModel 关闭范围，它需要实现`Closeable`接口。这就是为什么`viewModelScope`是扩展`CoroutineScope`覆盖`coroutineContext`并实现`Closeable`接口的`**CloseableCoroutineScope**`类型。

# 调度员。默认为 Main

`Dispatchers.Main.immediate`被设置为`viewModelScope`的默认[协程调度器](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/index.html)。

```
val scope = CloseableCoroutineScope(SupervisorJob() + Dispatchers.Main.immediate)
```

`Dispatchers.Main`很适合这种情况，因为 ViewModel 是一个与 UI 相关的概念，经常涉及更新它，因此在另一个 dispatcher 上启动将引入至少 2 个额外的线程切换。考虑到 suspend 函数将正确地执行自己的线程限制，使用其他调度程序将不是一个选项，因为我们将假设 ViewModel 正在做什么。

`immediate`用于立即执行协程，而无需将工作重新`[dispatch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/dispatch.html)`到适当的线程。

# 单元测试视图模型范围

`Dispatchers.Main`使用 Android `Looper.getMainLooper()`方法在 UI 线程中运行代码。这种方法在 Android 测试中可用，但在单元测试中不可用。

通过用`org.jetbrains.kotlinx:kotlinx-coroutines-test:$coroutines_version`中可用的[TestCoroutineDispatcher](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/-test-coroutine-dispatcher/)调用`Dispatchers.setMain(dispatcher: CoroutineDispatcher)`，使用`org.jetbrains.kotlinx:kotlinx-coroutines-test:$coroutines_version`库来替换 Coroutines 主调度程序。请注意，只有当您在代码库中使用`viewModelScope`或者硬编码`Dispatchers.Main`时，才需要`Dispatchers.setMain`。

[**TestCoroutineDispatcher**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/-test-coroutine-dispatcher/)是一个调度程序，它让我们控制协同程序如何执行，能够暂停/恢复执行并控制其虚拟时钟。它是作为一个实验性的 API 被添加到 Kotlin Coroutines v1.2.1 中的。你可以在[文档](https://github.com/Kotlin/kotlinx.coroutines/tree/master/kotlinx-coroutines-test)中读到更多关于它的内容。

不要用`Dispatchers.Unconfined`代替`Dispatchers.Main`，这会破坏所有使用`Dispatchers.Main`的代码的假设和计时。由于单元测试应该在隔离状态下运行良好，并且没有任何副作用，所以您应该在测试结束运行时调用`Dispatchers.resetMain()`并清理执行器。

您可以使用这个带有逻辑的 JUnitRule 来简化您的代码:

现在，您可以在您的单元测试中使用它。

## 使用 Mockito 测试协程

你是否使用 Mockito 并希望`verify`与一个对象发生交互？注意，使用 Mockito 的`verify`方法并不是单元测试代码的首选方式。您应该检查特定于应用程序的逻辑，如元素是否存在，而不是验证与对象的交互是否发生。

在检查与对象的交互发生之前，我们需要确保所有启动的协程都已经完成。让我们看看下面的例子。

在测试中，我们在规则创建的`TestCoroutineDispatcher`中调用`runBlockingTest`方法。由于该调度程序覆盖了`Dispatchers.Main`，MainViewModel 也将在该调度程序上运行协程。调用`runBlockingTest`将使协程在测试中同步执行。因为我们的`verify` Mockito 调用在`runBlockingTest`块中，所以它将在协程完成后被调用，并且交互将在那时发生。

再举一个例子，看看我们是如何将这种单元测试添加到 Kotlin 协同程序代码实验室的:

[](https://github.com/googlecodelabs/kotlin-coroutines/pull/29) [## 通过 manuelvicnt Pull 请求#29 将单元测试添加到 MainViewModel

### GitHub 是超过 3600 万开发人员的家园，他们一起工作来托管和审查代码、管理项目和构建…

github.com](https://github.com/googlecodelabs/kotlin-coroutines/pull/29) 

如果您正在使用架构组件、视图模型和协程，使用`viewModelScope`让框架为您管理其生命周期。这是显而易见的！

[协程代码实验室](https://codelabs.developers.google.com/codelabs/kotlin-coroutines)已经被更新以使用它。请查看这篇文章，了解更多关于协程以及如何在 Android 应用程序中使用它们的信息。