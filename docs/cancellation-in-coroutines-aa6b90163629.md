# 协同程序中的取消

> 原文：<https://medium.com/androiddevelopers/cancellation-in-coroutines-aa6b90163629?source=collection_archive---------0----------------------->

![](img/05e0b7a4fbd9022bff79291d6e0673b7.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 协程中的取消和异常(下)

在开发中，就像在生活中一样，我们知道避免做多余的工作很重要，因为这会浪费内存和精力。这个原则也适用于协程。您需要确保控制协程的生命周期，并在不再需要时取消它——这就是结构化并发所代表的。继续阅读，了解协程取消的来龙去脉。

如果你喜欢看这个视频，可以看看我和 Manuel Vivo 在 KotlinConf'19 上关于协程取消和异常的演讲:

> ⚠️为了毫无问题地理解文章的其余部分，阅读和理解系列文章的第一部分是必需的。

[](/androiddevelopers/coroutines-first-things-first-e6187bf3bb21) [## 协程:先做最重要的事情

### 协同程序中的取消和异常(第一部分)

medium.com](/androiddevelopers/coroutines-first-things-first-e6187bf3bb21) 

# 呼叫取消

当启动多个协程时，跟踪它们或者单独取消每个协程会很痛苦。相反，我们可以依赖于取消启动协程的整个范围，因为这将取消创建的所有子协程:

```
// assume we have a scope defined for this layer of the appval job1 = scope.launch { … }
val job2 = scope.launch { … }**scope.cancel()**
```

> 取消范围会取消其子范围

有时，您可能只需要取消一个协程，这可能是对用户输入的一种反应。调用 job1.cancel 可以确保只有那个特定的协同例程被取消，而其他所有的兄弟例程不受影响:

```
// assume we have a scope defined for this layer of the appval job1 = scope.launch { … }
val job2 = scope.launch { … }// First coroutine will be cancelled and the other one won’t be affected
**job1.cancel()**
```

> 被取消的孩子不会影响其他兄弟姐妹

协程通过抛出一个特殊的异常来处理取消:`CancellationException`。如果你想提供更多关于取消原因的细节，你可以在调用`.cancel`时提供一个`CancellationException`的实例，因为这是完整的方法签名:

```
fun cancel(cause: CancellationException? = null)
```

如果您不提供自己的`CancellationException`实例，将会创建一个默认的`CancellationException`(完整代码[在这里](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/JobSupport.kt#L612)):

```
public override fun cancel(cause: CancellationException?) {
    cancelInternal(cause ?: defaultCancellationException())
}
```

因为抛出了`CancellationException`，所以您将能够使用这种机制来处理协程取消。在下面的*处理取消副作用*部分有更多关于如何做的信息。

在幕后，子作业通过异常通知其父作业取消。父进程使用取消的原因来确定它是否需要处理异常。如果子代由于`CancellationException`而被取消，则父代不需要任何其他操作。

> ⚠️Once 如果你取消了一个作用域，你将不能在被取消的作用域中启动新的协程。

如果你正在使用 androidx KTX 库，在大多数情况下你不会创建你自己的作用域，因此你不负责取消它们。如果你在一个`ViewModel`的范围内工作，使用`[viewModelScope](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#(androidx.lifecycle.ViewModel).viewModelScope:kotlinx.coroutines.CoroutineScope)`，或者，如果你想启动绑定到一个生命周期范围的协程，你可以使用`[lifecycleScope](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#lifecyclescope)`。`viewModelScope`和`lifecycleScope`都是在适当的时候被取消的`[CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html)`对象。例如，[当 ViewModel 被清除](/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471)时，它取消在其作用域中启动的协程。

# 为什么我的协程工作没有停止？

如果我们只是调用`cancel`，并不意味着协程工作就此停止。如果您正在执行一些相对较重的计算，比如从多个文件中读取，没有什么会自动阻止您的代码运行。

让我们举一个更简单的例子，看看会发生什么。假设我们需要使用协程每秒打印两次“Hello”。我们将让协程运行一会儿，然后取消它。实现的一个版本如下所示:

让我们一步一步来看看会发生什么。当调用`launch`时，我们在**活动**状态下创建一个新的协程。我们让协程运行 1000 毫秒。所以现在我们看到印刷的:

```
Hello 0
Hello 1
Hello 2
```

一旦`job.cancel`被调用，我们的协程就移动到*取消*状态。但是接下来，我们看到 Hello 3 和 Hello 4 被打印到终端。只有在工作完成后，协程才移动到*取消*状态。

调用 cancel 时，协程工作不会停止。相反，我们需要修改我们的代码，并定期检查协程是否处于活动状态。

> 取消协程代码需要合作！

# 使您的协同工作可取消

您需要确保您正在实现的所有协同工作都与取消相配合，因此您需要定期检查取消，或者在开始任何长时间运行的工作之前检查取消。例如，如果您正在从磁盘读取多个文件，在开始读取每个文件之前，请检查协程是否被取消。这样，当不再需要 CPU 密集型工作时，您可以避免这样做。

```
val job = launch {
    for(file in files) {
        // TODO check for cancellation
        readFile(file)
    }
}
```

从`kotlinx.coroutines`开始的所有暂停功能都可以取消:`withContext`、`delay`等。所以如果你正在使用它们中的任何一个，你不需要检查取消和停止执行或者抛出一个`CancellationException`。但是，如果您不使用它们，为了使您的协同程序代码具有协作性，我们有两种选择:

*   检查`job.isActive`或`ensureActive()`
*   使用`yield()`让其他工作发生

# 检查作业的活动状态

在我们的`while(i<5)`中，有一个选项是为协程状态添加另一个检查:

```
// Since we're in the launch block, we have access to job.isActive
while (i < 5 && **isActive**)
```

这意味着我们的工作应该只在协程活动时执行。这也意味着一旦我们离开了 while，如果我们想做一些其他的动作，比如记录作业是否被取消，我们可以为`!isActive`添加一个检查，并在那里执行我们的动作。

协程库提供了另一个有用的方法- `ensureActive()`。它的实现是:

```
fun Job.ensureActive(): Unit {
    if (!isActive) {
         throw getCancellationException()
    }
}
```

因为如果作业不是活动的，这个方法会立即抛出，所以我们可以将它作为 while 循环中要做的第一件事:

```
while (i < 5) {
    ensureActive()
    …
}
```

通过使用`ensureActive`，您避免了自己实现`isActive`所要求的 if 语句，减少了您需要编写的样板代码的数量，但是失去了执行任何其他动作(如日志记录)的灵活性。

# 使用 yield()让其他工作发生

如果您正在做的工作 1) CPU 繁重，2)可能耗尽线程池，3)您希望允许线程做其他工作，而不必向线程池添加更多线程，那么使用`[yield()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/yield.html)`。yield 完成的第一个操作是检查完成情况，如果任务已经完成，则抛出`CancellationException`退出协程。`yield`可以是周期检查中调用的第一个函数，就像上面提到的`ensureActive()`一样。

# 作业.加入与延期.等待取消

等待协程结果有两种方式:从`launch`返回的作业可以调用`join`，从`async`返回的`Deferred`(T3 的一种)可以调用`await`

挂起协同程序，直到工作完成。与`job.cancel`一起，它的行为如你所料:

*   如果你调用`job.cancel`然后`job.join`，协程将暂停直到任务完成。
*   在`job.join`之后调用`job.cancel`没有效果，因为作业已经完成。

当您对协程的结果感兴趣时，可以使用`[Deferred](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/index.html)`。当协程完成时，这个结果由`[Deferred.await](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/await.html)`返回。`Deferred`是`Job`的一种，也可以取消。

在被`cancel` led 的延迟上调用`await`会抛出一个`JobCancellationException`。

```
val deferred = async { … }deferred.cancel()
val result = deferred.await() // throws JobCancellationException!
```

下面是我们得到这个异常的原因:`await`的作用是挂起协程，直到计算出结果；由于协程被取消，因此无法计算结果。因此，取消后调用 await 导致`JobCancellationException: Job was cancelled`。

另一方面，如果你在`deferred.await`之后调用`deferred.cancel`，什么都不会发生，因为协程已经完成了。

# 处理取消副作用

比方说，当一个协程被取消时，您想要执行一个特定的动作:关闭您可能正在使用的任何资源，记录取消或者您想要执行的一些其他清理代码。有几种方法可以做到这一点:

## 检查！isActive

如果您定期检查`isActive`，那么一旦您脱离了 while 循环，您就可以清理资源。我们上面的代码可以更新为:

```
while (i < 5 && **isActive**) {
    // print a message twice a second
    if (…) {
        println(“Hello ${i++}”)
        nextPrintTime += 500L
    }
}**// the coroutine work is completed so we can cleanup
println(“Clean up!”)**
```

在这里看它的作用[。](https://pl.kotl.in/loI9DaIYj)

所以现在，当协程不再活动时，`while`将会中断，我们可以进行清理了。

## 最后试着接住

因为当一个协程被取消时会抛出一个`CancellationException`,那么我们可以在`try/catch`和`finally`块中包装我们暂停的工作，我们可以实现我们的清理工作。

```
val job = launch {
   try {
      work()
   } catch (e: CancellationException){
      println(“Work cancelled!”)
    **} finally {
      println(“Clean up!”)
    }**
}delay(1000L)
println(“Cancel!”)
job.cancel()
println(“Done!”)
```

但是，如果我们需要执行的清理工作被挂起，上面的代码将不再工作，因为一旦协程处于取消状态，它就不能再挂起了。完整代码见[此处](https://pl.kotl.in/wjPINnWfG)。

> 处于取消状态的协程不能挂起！

为了能够在一个协程被取消时调用`suspend`函数，我们将需要切换我们需要在`NonCancellable` `CoroutineContext`中完成的清理工作。这将允许代码暂停，并将使协程保持在*取消*状态，直到工作完成。

```
val job = launch {
   try {
      work()
   } catch (e: CancellationException){
      println(“Work cancelled!”)
    } finally { **withContext(NonCancellable)**{
         delay(1000L) // or some other suspend fun 
         println(“Cleanup done!”)
      }}
}delay(1000L)
println(“Cancel!”)
job.cancel()
println(“Done!”)
```

点击查看这在实践中是如何工作的[。](https://pl.kotl.in/ufZRQSa7o)

# suspendCancellableCoroutine 和 invokeOnCancellation

如果您使用`[suspendCoroutine](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines.experimental/suspend-coroutine.html)`方法将回调转换为协程，那么最好使用`[suspendCancellableCoroutine](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)`方法。取消时要做的工作可以使用`[continuation.invokeOnCancellation](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellable-continuation/invoke-on-cancellation.html)`实现:

```
suspend fun work() {
   return suspendCancellableCoroutine { continuation ->
       continuation.invokeOnCancellation { 
          // do cleanup
       }
   // rest of the implementation
}
```

为了实现结构化并发的好处，并确保我们不做不必要的工作，你需要确保你的代码也是可取消的。

使用 Jetpack 中定义的【T0:】或`lifecycleScope`，当它们的作用域完成时取消它们的工作。如果你正在创建你自己的`CoroutineScope`，确保你将它绑定到一个任务，并在需要时调用取消。

协程代码的取消需要协作，所以确保更新代码以检查取消是否懒惰，并避免做不必要的工作。

从这篇文章中找到更多关于不应该被取消的工作模式:

[](/androiddevelopers/coroutines-patterns-for-work-that-shouldnt-be-cancelled-e26c40f142ad) [## 不应该取消的工作的协程和模式

### 协同程序中的取消和异常(第四部分)

medium.com](/androiddevelopers/coroutines-patterns-for-work-that-shouldnt-be-cancelled-e26c40f142ad)