# 协同程序和 rx Java——异步比较(第 4 部分):互操作库

> 原文：<https://medium.com/capital-one-tech/coroutines-and-rxjava-an-asynchronicity-comparison-part-4-interop-library-4a2439a690f9?source=collection_archive---------0----------------------->

![](img/ee90136029c91d2644bfb2555dce4465.png)

# 介绍

在这个博客系列中，我将比较 [Kotlin 协同程序](https://kotlinlang.org/docs/reference/coroutines.html)和 [RxJava](https://github.com/ReactiveX/RxJava) ，因为它们都试图解决 Android 开发中的一个常见问题:**异步编程**。

在[第 3 部分](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-3-transferring-stream-of-values-e858f4233791)中，我们已经讨论完了***publish***——一个内置了通道的协程，它在每次打开订阅时运行协程内的暂停 lambda。

在第 3 部分中，我们说过 *publish* 是 interop 库的一部分。这就是本文要讲的: [**协程和 RxJava 互操作库**](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-rx2) 。

# 为什么需要互操作库？

最明显的答案是你正在从一个库迁移到另一个库。您不应该进行一次性迁移，因为这将增加出错的风险。一个更好的方法是一个功能一个功能地迁移。在这种情况下，你需要在你的项目中同时使用这两个库，并且能够使用其中一个库。

不太明显的答案是当你想从 Java 代码中使用协程的时候。由于 Kotlin 编译器为您创建的代码，您不能从 Java 调用协程。出于这个原因，您需要一个能够从 Java 使用协程的桥。 **RxJava 是一个选项**。

# 入门指南

如果要使用 Interop 库，需要将该库导入到项目中。

```
implementation "org.jetbrains.kotlinx:**kotlinx-coroutines-rx2**:$kotlin_coroutines_version"
```

这将导入协程和 RxJava 2，以防您还没有将它们导入到您的项目中。

# RxJava 之上的协程

让我们回到我们在[第二部分](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-2-cancelling-execution-199485cdf068)中使用的例子:一个每秒发出一个项目的计时器。

```
Observable.interval(1, TimeUnit.SECONDS)
```

这是 RxJava 代码。我们如何使用协程来消费它呢？

我们可以在可观察对象上使用 [**openSubscription** 扩展函数](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-rx2/kotlinx.coroutines.experimental.rx2/io.reactivex.-observable-source/open-subscription.html)。这将返回一个`SubscriptionReceiveChannel`,您可以用它来打开订阅，然后**消费这些元素，就好像它们是由通道**发出的一样。

```
Observable.interval(1, TimeUnit.SECONDS)
    .***openSubscription*()**.*use* **{** channel **->** for (value in channel) {
            consumeValue(value)
        }
    **}**
```

您还可以使用 **await** 方法族，只使用 RxJava 信息源发送的第一个元素。你有`.await()`单个可用，`.awaitFirst()`可观测，等等。

这就是如何使用`[**awaitFirstOrDefault(value)**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-rx2/kotlinx.coroutines.experimental.rx2/io.reactivex.-observable-source/await-first-or-default.html)`扩展功能。

```
**val value** = Observable.interval(1, TimeUnit.SECONDS)
                .***awaitFirstOrDefault****(-1)*
```

反过来呢？如何使用 RxJava 消费协程？

# 协程之上的 RxJava

我们可以使用 RxJava 使用一些扩展函数来消费通道和协程。

## 使用 RxJava 消费协程

您可以使用[**Job . as Completable**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-rx2/kotlinx.coroutines.experimental.rx2/kotlinx.coroutines.experimental.-job/as-completable.html)扩展功能将任何作业转换为 Completable。

```
val job = *launch* **{** heavyComputation()
**}**job.***asCompletable***(CommonPool).subscribe(**{** // Job completed
**}**)
```

让我们想象一下，我们有一个执行繁重计算的协程。我们可以将该作业转换为 Completable 并订阅它，就好像它最初是用 RxJava 创建的一样。

在那个例子中，我们将`CommonPool`作为一个参数传递给扩展函数:这是 CoroutineContext，结果 completable 将从它发出信号。

也可以用同样的方法使用[**deferred . as single**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-rx2/kotlinx.coroutines.experimental.rx2/kotlinx.coroutines.experimental.-deferred/as-single.html)。

```
val deferred = *async* **{** heavyComputation()
**}**deferred.***asSingle***(CommonPool).subscribe(**{** // Job completed
**}**, **{** // Error happened
**}**)
```

## 协同程序生成器

有一些 CoroutineBuilders 将返回 RxJava 信息源。当您订阅它时，就像任何其他协程构建器一样，它将创建一个新的协程，并在其中运行挂起的 lambda。

**可用方法** : rxCompletable、rxMaybe、rxSingle、rxObservable、rx flow。

例如如何使用使用 [rxCompletable](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-rx2/kotlinx.coroutines.experimental.rx2/rx-completable.html) ？

```
***rxCompletable*****{** // Suspending lambda
**}**.subscribe()
```

取消订阅将取消协程。

## 使用 RxJava 使用通道

您还可以使用[**receive channel . as observable**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-rx2/kotlinx.coroutines.experimental.rx2/kotlinx.coroutines.experimental.channels.-receive-channel/as-observable.html)扩展功能将任何通道转换为热反应可观察值。

# 接下来会发生什么？

我们系列的[第五部](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-5-operators-2603a8ecaa5f)将全部是关于**操作者**！

所有这些令人惊叹的 RxJava 操作符在协程中都可用吗？默认支持哪些？哪些是我必须实现的？

# 更多教育

链接到本系列的前几部分

[](/capital-one-developers/coroutines-and-rxjava-an-asynchronicity-comparison-part-1-asynchronous-programming-e726a925342a) [## 协程和 rx Java——异步比较(第 1 部分):异步编程

### 异步编程

异步 Programmingmedium.com](/capital-one-developers/coroutines-and-rxjava-an-asynchronicity-comparison-part-1-asynchronous-programming-e726a925342a) [](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-2-cancelling-execution-199485cdf068) [## 协程和 rx Java——异步比较(第 2 部分):取消执行

### 第 2 部分—取消执行

medium.com](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-2-cancelling-execution-199485cdf068) [](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-3-transferring-stream-of-values-e858f4233791) [## 协程和 rx Java——异步性比较(第 3 部分):传递价值流

### 第 3 部分—转移价值流

medium.com](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-3-transferring-stream-of-values-e858f4233791) 

感谢阅读，

曼努埃尔·维森特 Vivo