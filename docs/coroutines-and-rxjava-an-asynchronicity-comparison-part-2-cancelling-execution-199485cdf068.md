# 协程和 rx Java——异步比较(第 2 部分):取消执行

> 原文：<https://medium.com/capital-one-tech/coroutines-and-rxjava-an-asynchronicity-comparison-part-2-cancelling-execution-199485cdf068?source=collection_archive---------0----------------------->

![](img/ba36fb284f19247ffafe5837b4dc1a19.png)

# 介绍

在这个博客系列中，我将比较 [Kotlin 协同程序](https://kotlinlang.org/docs/reference/coroutines.html)和 [RxJava](https://github.com/ReactiveX/RxJava) ，因为它们都试图解决 Android 开发中的一个常见问题:异步编程。

在第 1 部分中，我们学习了如何在后台执行繁重的计算任务。如果我们想中断计算呢？

第二部分是关于**取消执行**。

# 取消执行是什么意思？

我们希望能够取消用 RxJava 或协程创建的计算的执行。这个计算可以是异步的，也可以不是。

这在 Android 开发的不同用例中是很重要的——最常见的可能是视图将要被销毁的时候。如果发生这种情况，我们可能希望取消正在进行的执行，比如网络请求、重对象初始化等。

# RxJava

和第 1 部分一样，我们将省略传输元素流的能力。如何用 RxJava 取消执行？

让我们想象我们用[间隔操作符](http://reactivex.io/documentation/operators/interval.html)创建一个**计时器**。

```
Observable.interval(1, TimeUnit.SECONDS)
```

当您订阅这个可观察对象时，计时器将开始计时，它会在订阅后每秒钟向订阅者发送一个事件。

## 你怎么能取消那个计时器？

当你订阅定时器(调用`.subscribe()`)时，它返回一个`[**Disposable**](http://reactivex.io/RxJava/javadoc/io/reactivex/disposables/Disposable.html)` **对象**。

```
**val disposable: Disposable** = 
    Observable.interval(1, TimeUnit.SECONDS).**subscribe()**
```

**您可以调用** `**dispose()**` **对一次性对象取消执行**。被观察者将完成发射物品。

```
**disposable.dispose()**
```

就是这样！我们已经取消了可观测数据所产生的异步计算。

## 警告

如果您不使用任何 create 操作符(比如 interval)手动创建自己的可观察对象，您不需要自己处理计算的取消。

这种可观察性是不会被取消的。如果我们想让它发生，**我们需要在调用它之前检查发射器是否仍然被订阅。**

```
Observable.create<Int> **{** emitter **->** for (i in 1..5) {
       **if (!emitter.isDisposed) {** emitter.onNext(i)
       } else {
           **break**
       }
   }emitter.onComplete()
}
```

如果订户不在那里，我们可以跳过发送其余的项目。如果我们不这样做，代码将继续运行，并根据`[Observable.create](https://github.com/ReactiveX/RxJava/blob/v2.1.12/src/main/java/io/reactivex/internal/operators/observable/ObservableCreate.java)` [源代码](https://github.com/ReactiveX/RxJava/blob/v2.1.12/src/main/java/io/reactivex/internal/operators/observable/ObservableCreate.java)忽略`emitter.onNext(i)`调用。

# 协同程序

协程是计算本身的一个实例。取消协程意味着停止执行它的暂停 lambda。

> 我们可以用[协程作业](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-job/)取消执行，它是[协程上下文](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines.experimental/-coroutine-context/)的一部分。

**协程作业公开了取消协程执行的方法**。正如我们所料，这种方法被称为`cancel()`。

例如，[协程生成器 ***launch***](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/launch.html) 返回它创建的协程的作业。

```
**val job** = ***launch***(CommonPool) {
    // my suspending block
}
```

我们可以给它赋值并调用 cancel。

```
**job.cancel()**
```

这是一个从协程获取工作并取消它的例子。你能用不同的方法做它吗？是的，你也可以**指定协程**的工作。你可以通过多种方式做到这一点。

一些协程构建器(例如 launch 和 async)采用一个名为 `**parent**`的**命名参数，您可以用它为将要创建的协程设置作业。**

```
val parentJob = *Job*()*async*(CommonPool, **parent = parentJob**) {// my suspending block
}parentJob.cancel()
```

这种方法的一个好处是，你可以与多个协程共享那个 `**parentJob**` **实例，所以当你调用`parentJob.cancel()`时，你将取消那些以`parentJob`为工作的协程的执行。**

这种方法**类似于 rx Java**[**composited disposable**](http://reactivex.io/RxJava/javadoc/io/reactivex/disposables/CompositeDisposable.html)，使用它可以一次处理多个订阅。

```
val parentJob = *Job*()val deferred1 *= async*(CommonPool, **parent = parentJob**) {// my suspending block
}val deferred2 = *async*(CommonPool, **parent = parentJob**) {// my suspending block
}parentJob.cancel() **// Cancels both Coroutines**
```

注意:在不同的协程之间共享作业时，您应该小心。**取消作业时，需要重新分配。您不能用作业**启动另一个协程，您必须创建一个新的协程。

> 当您取消作业时，您需要重新分配它。

另一种方法是结合协程上下文。您可以使用加号运算符来完成此操作。

```
val parentJob = *Job*()*launch*(**parentJob + CommonPool**) {// my suspending block
}parentJob.cancel()
```

在这种情况下，**该协程的结果协程上下文是** `**parentJob**` **和** `**CommonPool**`的组合。线程策略将由`CommonPool`定义，作业值由`parentJob`定义。

如果你想了解更多关于组合上下文的知识，你可以阅读 Kotlin 协同程序文档的这一部分。

## 警告

就像 RxJava 一样，你必须考虑协程中的取消。

```
val job = *launch*(CommonPool) {for (i in 1..5) {
        heavyComputation()
    }
}job.cancel()
```

如果我们尝试执行这个代码，它将重复繁重的计算 5 次，因为**代码还没有准备好被取消**。

我们该如何改进它？

与我们检查订阅者是否在 RxJava 中一样，我们需要检查协程是否是活动的。

```
val job = *launch*(CommonPool) {for (i in 1..5) {
 **if (!isActive) { break }**        heavyComputation()
    }
}job.cancel()
```

`[**isActive**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-job/is-active.html)`是一个可以在协程内部访问的内部变量(`[coroutineContext](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-scope/coroutine-context.html)`是另一个变量)。

标准协程库中的一些挂起函数为我们处理取消。我们来看看`delay`。

```
val job = *launch*(CommonPool) {doSomething()
 **delay(300) // It’s going to cancel at this point**
    doSomething()
}job.cancel()
```

[Delay](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-delay/) 是一个暂停函数，可以为我们处理取消。但是，如果你使用`Thread.sleep`而不是`delay`，因为它阻塞了线程，没有挂起协程，所以它不会取消它。

```
val job = *launch*(CommonPool) {doSomething()
 **Thread.sleep(300) // It’s NOT going to cancel execution**    doSomething()
}job.cancel()
```

`Thread.sleep`并没有取消对我们的处决。它甚至不是一个暂停功能！即使我们调用了`job.cancel()`，那个协程也不会被取消。

在这种情况下，不应该使用`Thread.sleep`。如果你真的需要，取消协程的一个方法是检查它是否在线程阻塞之前和之后都是活动的。

```
val job = *launch*(CommonPool) {doSomething()
 **if (!isActive) return**
    Thread.sleep(300) // It’s NOT going to cancel execution **if (!isActive) return**
    doSomething()
}job.cancel()
```

# 接下来会发生什么？

本系列的第三部分[将是关于**传输元素流**。](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-3-transferring-stream-of-values-e858f4233791)

**可观察和通道有什么区别？题材和播出渠道？**下周不要错过第三部。

# 更多教育

您是否错过了**协程和 RxJava 比较第 1 部分**？

[](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-1-asynchronous-programming-e726a925342a) [## 协程和 rx Java——异步比较(第 1 部分):异步编程

### 第 1 部分—异步编程

medium.com](/@manuelvicnt/coroutines-and-rxjava-an-asynchronicity-comparison-part-1-asynchronous-programming-e726a925342a) 

你想了解更多关于科特林的事吗？看看这篇文章！

[](https://proandroiddev.com/kotlin-education-d0b958740d6a) [## 科特林教育

### 超越基础

proandroiddev.com](https://proandroiddev.com/kotlin-education-d0b958740d6a) 

感谢阅读，

曼努埃尔·维森特 Vivo