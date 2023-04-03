# 在视图上暂停

> 原文：<https://medium.com/androiddevelopers/suspending-over-views-19de9ebd7020?source=collection_archive---------1----------------------->

![](img/06b55745db5efc7d978190f3bb0d727c.png)

Illustration by [Virginia Poltrack](/@VPoltrack)

## 协程如何简化 UI 编程

Kotlin 协同程序允许我们像模拟同步代码一样模拟异步问题。这很好，但是大多数使用似乎集中在 I/O 任务和并发操作上。协同程序非常擅长建模跨线程工作的问题，但也可以在同一个*线程上建模异步问题。*

我认为有一个地方真正从中受益，那就是使用 Android view 系统。

# Android 视图💘复试

Android view 系统爱回调；喜欢 ***真的*** 爱回调。给你一个思路，目前在 Android 框架中的 view 和 widgets 类中有 80+个回调，然后在 Jetpack 中有另外 200+个(包括非 UI 库，但是你明白思路了)。

常用的例子包括:

*   知道一个动画师什么时候完成。
*   `[RecyclerView.OnScrollListener](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.OnScrollListener)`了解滚动状态何时改变。
*   `[View.OnLayoutChangeListener](https://developer.android.com/reference/android/view/View.OnLayoutChangeListener.html)`知道一个观点是什么时候提出来的。

然后是接受一个`[Runnable](https://developer.android.com/reference/java/lang/Runnable.html)`来执行异步动作的 API，比如`View.post()`或`View.postDelayed()`等。

之所以有这么多回调，是因为 Android 上的用户界面编程本来就是异步的。从测量和布局、绘图到插入调度，一切都是异步执行的。一般来说，某个东西(通常是一个视图)向系统请求遍历，然后一段时间后，系统调度该调用，然后触发任何侦听器。

# KTX 扩展功能

对于我们上面提到的许多 API，团队已经在 Jetpack 中添加了扩展功能，以改善开发人员的人机工程学。我最喜欢的一个是`[View.doOnPreDraw()](https://developer.android.com/reference/kotlin/androidx/core/view/package-summary#doonpredraw)`，它*大大简化了等待下一次抽奖的过程。还有很多其他的我每天都在用:举两个例子:`[View.doOnLayout()](https://developer.android.com/reference/kotlin/androidx/core/view/package-summary#doonlayout)`和`[Animator.doOnEnd()](https://developer.android.com/reference/kotlin/androidx/core/animation/package-summary#(android.animation.Animator).doOnEnd(kotlin.Function1))`。*

但是这些扩展函数只能做到这一步:它们把一个老派的回调 API 变成了一个 Kotlin 友好的基于 lambda 的 API。它们使用起来更好，但是我们仍然以不同的形式处理回调，这使得执行复杂的 UI 操作更加困难。既然我们在讨论异步操作，那么我们能从协程中获益吗？🤔

# 救援协管员

这篇博客文章假设了协程知识的工作水平。如果你对下面的内容感到陌生，我们今年早些时候发表了一系列博客文章来帮助你回顾:

[](/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb) [## Android 上的协同程序(第一部分):了解背景

### 协程解决什么问题？

medium.com](/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb) 

挂起函数是协程的基本单元之一，它允许我们以非阻塞的方式编写代码。这在我们处理 Android UI 时很重要，因为我们从不希望阻塞主线程，这可能导致类似 [*jank*](https://developer.android.com/topic/performance/vitals/render) 的性能问题。

# suspendCancellableCoroutine

在 Kotlin 协同程序库中，有许多协同程序构建器函数，它们支持用挂起函数包装基于回调的 API。主要的 API 是`[suspendCoroutine()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/suspend-coroutine.html)`，有一个可取消的版本叫做`[suspendCancellableCoroutine()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)`。

我们建议始终使用`[suspendCancellableCoroutine()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)`，因为它允许我们处理两个方向的取消:

**#1:当异步操作挂起时，协程可以被取消**。根据协程运行的范围，如果视图从视图层次结构中删除，协程可能会被取消。示例:从堆栈中弹出片段。处理这个方向允许我们取消任何异步操作，并清理任何正在进行的资源。

**#2:当协程被挂起时，异步 UI 操作被取消(或者抛出一个错误)。**不是所有的操作都有取消或错误状态，但是对于那些有取消或错误状态的操作，比如下面的`Animator`，我们应该将这些状态传播到协程，允许方法的调用者处理错误。

# 等待一个视图被布局

让我们来看一个例子，它完成了等待视图中下一次布局传递的任务(例如，您已经更改了一个`TextView`的文本，需要等待一次布局传递才能知道它的新大小):

该函数仅支持一个方向的取消，从协程到操作 *(#1)* ，因为布局没有我们可以观察到的错误状态。

我们可以这样使用它:

我们刚刚为视图的布局构建了一个 await 函数。相同的方法可以应用于许多常用的回调，例如`[doOnPreDraw()](https://developer.android.com/reference/kotlin/androidx/core/view/package-summary#doonpredraw)`知道绘制过程何时将要发生，`postOnAnimation()`知道下一个动画帧是什么时候，等等。

# 范围

你会注意到在上面的例子中，我们使用了一个`lifecycleScope`来启动我们的协程。那是什么？

当我们接触 UI 时，用来运行任何协程的范围尤其重要，以避免意外泄漏内存。幸运的是，对于我们的视图来说，有许多合适的范围。然后，我们可以使用`[lifecycleScope](https://developer.android.com/topic/libraries/architecture/coroutines#lifecyclescope)`扩展属性来获得一个适用于该生命周期的`[CoroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/)`。

> `*LifecycleScope*` *在 AndroidX* `*lifecycle-runtime-ktx*` *库中可用。你可以在这里* *找到更多信息* [*。*](https://developer.android.com/topic/libraries/architecture/coroutines)

一个常用的生命周期所有者是`Fragment`的`[viewLifecycleOwner](https://developer.android.com/reference/androidx/fragment/app/Fragment.html#getViewLifecycleOwner())`，只要附加了片段的视图，它就是活动的。一旦片段的视图被移除，附加的`[lifecycleScope](https://developer.android.com/topic/libraries/architecture/coroutines#lifecyclescope)`会自动取消。因为我们在挂起函数中增加了取消支持，所以如果发生这种情况，一切都会被自动清除。

# 等待动画师完成

让我们看另一个例子，这次等待动画师来完成:

该功能支持*和*两个方向的取消，因为`Animator`和协程都可以单独取消。

**#1:当动画师运行**时，协程被取消。我们可以使用`[invokeOnCancellation](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-cancellable-continuation/invoke-on-cancellation.html)`回调来知道协同程序何时被取消，从而使我们也能够取消动画制作程序。

**#2:当协程暂停时，动画程序被取消。**我们可以使用`[onAnimationCancel()](https://developer.android.com/reference/android/animation/Animator.AnimatorListener.html#onAnimationCancel(android.animation.Animator))`回调来知道动画师何时被取消，从而允许我们调用`cancel()`来取消暂停的协程。

我们刚刚学习了将回调 API 封装到挂起的 await 函数中的基础知识。🏅

# 指挥乐队

在这一点上，你可能会想“*太好了，但是这给了我什么*？”孤立地看，这些功能不会做很多事情，但是当你开始将它们结合在一起时，它们会变得非常强大。

下面是一个使用`Animator.awaitEnd()`依次运行 3 个动画的例子:

对于这个特殊的例子，你可以把它们都放到一个`[AnimatorSet](https://developer.android.com/reference/android/animation/AnimatorSet.html)`中，得到同样的效果。

但是这种技术适用于不同类型的异步操作；这里使用了一个`[ValueAnimator](https://developer.android.com/reference/android/animation/ValueAnimator.html)`、一个`[RecyclerView](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.html)`平滑滚动和一个`[Animator](https://developer.android.com/reference/android/animation/Animator.html)`:

尝试用`AnimatorSet`来做这件事🤯。要在没有协程的情况下实现这一点，意味着要向每个操作添加侦听器，这会启动下一个操作，依此类推。呸。

> 通过将不同的异步操作建模为`suspend`函数，我们获得了表达性和简洁性地编排它们的能力。

尽管如此，我们还可以更进一步…

**如果我们想让** `[ValueAnimator](https://developer.android.com/reference/android/animation/ValueAnimator)` **和平滑滚动同时启动，然后在两者都完成后再启动** `[ObjectAnimator](https://developer.android.com/reference/android/animation/ObjectAnimator)` **怎么办？**因为我们正在使用协程，我们可以使用`[async()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html)`并发运行它们:

但是如果你想让滚动延迟开始呢？(类似于`[Animator.startDelay](https://developer.android.com/reference/android/animation/Animator.html#setStartDelay(long))`)。好吧，协程也已经把你包括在内了，我们可以使用`[delay()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/delay.html)`函数:

**如果我们想要一个重复的过渡怎么办？**我们可以用`[repeat()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/repeat.html)`函数包装整个事情(或者使用`for`循环)。以下是一个视图淡入淡出 3 次的示例:

你甚至可以通过重复计数来完成一些简单的事情。假设您想要渐强渐弱在每次重复时逐渐变慢:

在我看来，这就是在 Android view 系统中使用协程真正发挥作用的地方。我们可以创建一个复杂的异步转换，结合不同的动画类型，而不必将不同类型的回调链接在一起。

通过使用我们在应用程序的数据层上使用的相同的协程原语，我们也使 UI 编程更容易访问。对于新接触代码的人来说，一个 *await* 函数比许多看似不相关的回调函数更具可读性。

# post.resume()

希望这篇文章能让你思考还有哪些 API 可以从协程中获益！

这篇文章的后续文章展示了如何使用协程来编排复杂的转换，并包括一些常见视图的实现，可以在下面找到:

[](/androiddevelopers/suspending-over-views-example-260ce3dc9100) [## 在视图上暂停-示例

### 让我们把我们在上一篇文章中学到的东西应用到一个真实的应用用例中。这里我们有电视节目…

medium.com](/androiddevelopers/suspending-over-views-example-260ce3dc9100)