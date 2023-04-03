# 我是如何从零开始重建一个 JavaScript 承诺的

> 原文：<https://medium.com/globant/how-i-recreated-a-javascript-promise-from-scratch-f649931dadfe?source=collection_archive---------3----------------------->

![](img/383f7ad813b08ea9951b379744cc266e.png)

Photo by [Miryam León](https://unsplash.com/@miryam_leon?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 什么是承诺？

JavaScript 中的承诺是一个表示异步操作的对象，可以有三种状态之一:

*   *:初始状态，既不履行也不拒绝。*
*   ****完成*** :表示操作成功完成。*
*   ****拒绝*** :表示操作失败。*

*A promise with a positive attitude*

# *关于承诺的困惑*

*承诺允许在外部 API 的帮助下异步执行 JavaScript 代码，但经常被混淆为异步本身。为了消除这个神话，为了向自己证明承诺没有什么不同步，我继续创造了我自己的小“假”承诺。为了简单起见，我实现了它的 resolve 部分。我鼓励你自己去推断被拒绝的部分。*

## *步骤 1:在机器周围创建外壳*

*我们像往常一样开始写承诺，只是这次我们把它命名为 *FakePromise* 。我们稍后将实现这个类，但是现在，我们的假承诺看起来与真承诺一样，包含以下部分:*

## *步骤 2:创建 FakePromise 函数构造函数*

*我们现在创建刚刚实例化的 FakePromise 类，记住以下几点:*

1.  *必须将名为“resolve”的方法传递给给 FakePromise 构造函数的回调*
2.  *当回调的异步任务结束时，必须运行该方法。*

*现在我们有了，*

1.  *接受回调的 FakePromise 类*
2.  *一个 resolve 方法，该方法向下传递给此回调，并在回调结束其异步任务时调用*

*让我们弄清楚这个 resolveCallback 是什么。*

## *第三步:那么“然后”呢？*

*想想看，‘解决’和‘然后’有什么关系？我们传递一个值，这个值代表了一个解决方法的承诺的实现，这个值*奇迹般地从传递给‘then’方法的回调中导出。**

*记住这种关系，让我们定义“然后”。*

*在这里，“then”方法接受一个最终为“this.resolveCallback”的回调。这是一种方法，通过这种方法，承诺的结果将被挖掘出来。*

*我们在哪里见过这个解决方案？没错，就是看到被 resolve 方法调用，被赋予了承诺的结果。让我们再看一下下面的代码:*

# *摘要*

*以下是夏洛克·福尔摩斯为您带来的一系列事件；)*

1.  *异步 setTimeout 是在浏览器 API 的帮助下完成的(没有任何承诺的帮助，谢谢)*
2.  *在与 setTimout 相同的函数中，我们调用 resolve 方法，并向其传递表示承诺实现的最终值。*
3.  *当 resolve 被调用时，它运行一个叫做 resolveCallback 的东西，并向它传递最终值*
4.  *惊喜！resolveCallback 作为参数提供给了“then”。现在它已经被 resolve 方法调用，并且有了最终值(在我们的例子中，它是一个字符串，表示“这是在 5 秒钟后异步完成的”)*
5.  *我们已经在正在运行的对“then”(resolve callback)的回调中实现了这个最终值的使用。在我们的例子中，我们只是将字符串记录到控制台。*

*正如我们所见，承诺只是对回调概念的巧妙抽象，而不是 ES6 中神奇的异步部分！*