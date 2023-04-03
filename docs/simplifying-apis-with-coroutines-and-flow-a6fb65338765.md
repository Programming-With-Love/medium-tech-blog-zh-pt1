# 用协程和流程简化 API

> 原文：<https://medium.com/androiddevelopers/simplifying-apis-with-coroutines-and-flow-a6fb65338765?source=collection_archive---------0----------------------->

![](img/f1f064469350b2336414178de85f7e03.png)

如果您是一个库作者，您可能希望使用协程和流使您的基于 Java 或基于回调的库更容易从 Kotlin 中使用。或者，如果你是一个 API 消费者，你可能愿意让第三方 API 表面适应协程，使它们对 Kotlin 更友好。

本文涵盖了如何使用协程和流程简化 API，以及如何使用`suspendCancellableCoroutine`和`callbackFlow`API 构建自己的适配器。对于最好奇的人来说，这些 API 将被剖析，您将看到它们是如何工作的。

如果你更喜欢看关于这个话题的视频。看看这个:

# 检查现有的协程适配器

在为现有 API 编写自己的包装器之前，检查适配器或[扩展函数](/androiddevelopers/extend-your-code-readability-with-kotlin-extensions-542bf702aa36)是否可用于您的用例。对于常见的类型，已经有了带有协程适配器的库。

## 未来类型

对于未来类型，有 Java 8 的 [CompletableFuture](https://github.com/Kotlin/kotlinx.coroutines/blob/master/integration/kotlinx-coroutines-jdk8/src/future/Future.kt) 和 Guava 的 [ListenableFuture](https://github.com/Kotlin/kotlinx.coroutines/blob/master/integration/kotlinx-coroutines-guava/src/ListenableFuture.kt) 的集成。这不是一个详尽的列表，如果您未来类型的适配器已经存在，请在线搜索。

```
// Awaits completion of CompletionStage without blocking a thread
suspend fun <T> **CompletionStage<T>.await(): T** // Awaits completion of ListenableFuture without blocking a thread
suspend fun <T> **ListenableFuture<T>.await(): T**
```

有了这些函数，您可以摆脱回调，只需挂起协程，直到将来的结果返回。

## 反应流

对于反应流库，有对 [RxJava](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-rx3) 、[Java 9 API](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-jdk9)和[反应流](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-reactive)库的集成。

```
// Transforms the given reactive Publisher into Flow.
fun <T : Any> **Publisher<T>.asFlow(): Flow<T>**
```

这些功能将反应流转化为流动。

## Android 特定 API

对于 Jetpack 库或 Android 平台 API，请看一下 [Jetpack KTX 库](https://developer.android.com/kotlin/ktx/extensions-list)列表。目前，超过 20 个库拥有 KTX 版本，创建了 Java APIs 的甜蜜惯用版本，从 SharedPreferences 到 ViewModels、SQLite 甚至 Play Core。

## 复试

回调是异步通信的一种非常常见的解决方案。事实上，我们在后台线程指南中的[运行任务中使用它们作为 Java 编程语言解决方案。然而，它们也有一些缺点:这种设计会导致嵌套的回调，最终导致不可理解的代码。此外，错误处理更加复杂，因为没有简单的方法来传播它们。](https://developer.android.com/guide/background/threading)

在 Kotlin 中，您可以使用协程来简化回调的调用，但是为此，您需要构建自己的适配器。

# 构建您自己的适配器

如果你没有为你的用例找到一个适配器，通常你可以自己写一个。**对于一次性异步调用，使用** `[**suspendCancellableCoroutine**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)` **API。对于流数据，使用** `[**callbackFlow**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/callback-flow.html)` **API。**

作为练习，下面的例子将使用来自 Google Play 服务的[融合位置提供者](https://developers.google.com/android/reference/com/google/android/gms/location/FusedLocationProviderClient.html) API 来获取位置数据。API 表面很简单，但是它使用回调来执行异步操作。有了协程，我们可以摆脱那些在逻辑变得复杂时会很快使我们的代码不可读的回调。

如果您想探索其他解决方案，可以从上面链接的所有函数的源代码中获得灵感。

## 一次性异步调用

[融合位置提供者](https://developers.google.com/android/reference/com/google/android/gms/location/FusedLocationProviderClient.html) API 提供了`[getLastLocation](https://developers.google.com/android/reference/com/google/android/gms/location/FusedLocationProviderClient#getLastLocation())`方法来获取[最后已知位置](https://developer.android.com/training/location/retrieve-current)。协程的理想 API 是一个 suspend 函数，它返回的正是这个函数。

> 注意，这个 API 返回一个[任务](https://developers.google.com/android/reference/com/google/android/gms/tasks/Task)，并且已经有一个[适配器](https://github.com/Kotlin/kotlinx.coroutines/blob/master/integration/kotlinx-coroutines-play-services/src/Tasks.kt)可供它使用。然而，出于学习的目的，我们将把它作为一个例子。

通过在`FusedLocationProviderClient`上创建一个扩展函数，我们可以有一个更好的 API:

```
suspend fun FusedLocationProviderClient.**awaitLastLocation(): Location**
```

因为这是一个一次性的异步操作，所以我们使用了`suspendCancellableCoroutine`函数:一个从协程库中创建挂起函数的底层构建块。

`suspendCancellableCoroutine`执行作为参数传递给它的代码块，然后暂停协程执行，同时等待信号继续。当在协程的[延续](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-continuation/)对象中调用`resume`或`resumeWithException`方法时，协程将恢复执行。关于延续的更多信息，请查看[文章](/androiddevelopers/the-suspend-modifier-under-the-hood-b7ce46af624f)下的*暂停修改器。*

我们使用可以添加到`getLastLocation`方法的回调来适当地恢复协程。请参见下面的实现:

注意:虽然您也可以在协程库(即`[suspendCoroutine](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/suspend-coroutine.html)`)中找到这个协程构建器的不可取消版本，但是最好总是选择`[suspendCancellableCoroutine](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)`来处理协程作用域的取消，或者从底层 API 传播取消。

## 引擎盖下的 suspendCancellableCoroutine

在内部，`[suspendCancellableCoroutine](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/CancellableContinuation.kt#L305)`使用`[suspendCoroutineUninterceptedOrReturn](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/coroutines/intrinsics/Intrinsics.kt#L41)`获取挂起函数中协程的`Continuation`。那个`Continuation`对象被一个`[CancellableContinuation](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/CancellableContinuation.kt)`拦截，它将从那一点开始控制那个协程的生命周期(它的[实现](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/CancellableContinuationImpl.kt)具有带一些限制的`Job`的功能)。

之后，传递给`suspendCancellableCoroutine`的 lambda 将被执行，如果 lambda 返回结果，协程将立即恢复，或者将被挂起，直到从 lambda 手动恢复`CancellableContinuation`。

参见下面代码片段中我自己的注释(遵循[原始实现](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/CancellableContinuation.kt#L305))来理解发生了什么:

要了解更多关于挂起功能如何工作的信息，请查看 [*挂起修改器*文章](/androiddevelopers/the-suspend-modifier-under-the-hood-b7ce46af624f)。

## 流式数据

相反，如果我们希望每当用户的设备在现实世界中移动时都接收定期的位置更新(使用`[requestLocationUpdates](https://developers.google.com/android/reference/com/google/android/gms/location/FusedLocationProviderClient#requestLocationUpdates(com.google.android.gms.location.LocationRequest,%20com.google.android.gms.location.LocationCallback,%20android.os.Looper))`函数)，我们需要使用 [Flow](https://kotlinlang.org/docs/reference/coroutines/flow.html) 创建一个数据流。理想的 API 应该是这样的:

```
fun FusedLocationProviderClient.**locationFlow(): Flow<Location>**
```

要将基于流回调的 API 转换为流，请使用创建新流的`[callbackFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/callback-flow.html)`流构建器。在`callbackFlow` lambda 中，我们在协程的上下文中，因此，可以调用暂停函数。与`[flow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/flow.html)`流构建器不同，`channelFlow`允许使用`[offer](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.channels/-send-channel/offer.html)`方法从不同的`CoroutineContext`或协程外部发出值。

通常，使用`callbackFlow`的流量适配器遵循以下三个通用步骤:

1.  使用`offer`创建将元素添加到流中的回调。
2.  注册回拨。
3.  等待使用者取消协程并注销回调。

将这个方法应用于这个用例，我们得到了下面的实现:

## 呼叫引擎盖下的回流

在内部，`callbackFlow`使用一个[通道](https://kotlinlang.org/docs/reference/coroutines/channels.html)，这在概念上与阻塞[队列](https://en.wikipedia.org/wiki/Queue_(abstract_data_type))非常相似。用`capacity`配置一个通道:可以缓冲的元素数量。在`callbackFlow`中创建的通道具有 64 个元件的默认容量。当向已满的通道添加新元素时，`send`将暂停生成器，直到通道中有空间容纳新元素，而`offer`不会向通道添加元素，并将立即返回`false`。

## 引擎盖下的一个陷阱

有趣的是，`awaitClose`在引擎盖下使用`suspendCancellableCoroutine`。参见下面代码片段中我自己的注释(遵循[原始实现](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/channels/Produce.kt#L49))来理解发生了什么:

## 重用流程

流动是冷的和懒惰的，除非有中间操作者如`[conflate](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/conflate.html)`另有说明。这意味着每次在流上调用终端操作符时，都会执行构建器块。在我们的例子中，这可能不是一个大问题，因为添加新的位置侦听器成本很低，但是，在其他实现中，这可能会有所不同。

使用`[shareIn](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/share-in.html)`中间操作器在多个收集器之间重复使用相同的流量，并使冷流量变热。

要了解更多关于在应用程序中添加`applicationScope`的最佳实践，请查看这篇[文章](/androiddevelopers/coroutines-patterns-for-work-that-shouldnt-be-cancelled-e26c40f142ad)。

考虑创建协程适配器，使您的 API 或现有的 API 简洁、易读且符合 Kotlin 习惯。首先检查适配器是否已经可用，如果不可用，使用`suspendCancellableCoroutine`创建自己的适配器进行一次性调用，使用`callbackFlow`创建流数据。

要获得这个主题的实践经验，请查看构建 Kotlin 扩展库的 [*代码实验室*](https://codelabs.developers.google.com/codelabs/building-kotlin-extensions-library)。