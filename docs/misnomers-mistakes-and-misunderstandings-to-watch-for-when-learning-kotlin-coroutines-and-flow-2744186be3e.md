# 学习 Kotlin 协程和流程时要注意的用词不当、错误和误解

> 原文：<https://medium.com/google-developer-experts/misnomers-mistakes-and-misunderstandings-to-watch-for-when-learning-kotlin-coroutines-and-flow-2744186be3e?source=collection_archive---------2----------------------->

## 当我在几个不同的项目(包括我自己的项目)中学习使用协程和流时，我注意到一些常见的反模式，这些反模式往往是在处理结构化并发和反应式流时出现的。在本文中，我将概述如何识别和重构四种最常见的情况。

![](img/f237f25e572f6ca53e9222fa7881c732.png)

**Keep your eyes open! A Tarsier I met near Bitung, Sulawesi**

# 1.使用流而不是挂起函数

当设计你的方法和函数时，从一个暂停函数开始，如果你卡住了，把它改成一个流程。

考虑调用 RESTful API 的典型函数签名。

这样的函数在从 RxJava 迁移过来的项目中尤其常见，在 RxJava 中我们有一个`Single`的概念

一个`Single`意味着你期望从你的流中接收一个*单个*事件，而不是*多个*。一个*单个*事件...听起来不像是“溪流”，对吧？更像一滴而不是一条小溪。这就是为什么`Single`在 Flow 中不是作为一个显式类型存在，而是作为一个操作符: [Flow.single()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/single.html) 。

但是不要使用运算符！只有在出于某种原因需要合并或组合流时，它才是有用的…没有必要把事情复杂化——如果您期待的是单个事件——只需返回它！

挂起函数应该是您开箱即用的第一个工具，这主要是因为它是制作可读代码的正确选择，还因为如果您以后还想试验一个流，它可以很容易地重构为一个流:

你要问自己的关键问题是——我期待的是一件事(暂停功能)还是许多事(流)？

# 2.用回调参数挂起函数

挂起函数是回调的直接替代——它是结构化并发的基本前提之一。回调使得代码难以理解(例如它们让你的代码*变得无结构*。这方面的经典例子是[末日金字塔](https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming))或[回调地狱](http://callbackhell.com/)。协程(在 Kotlin 中主要表示为 suspend 函数)旨在清理这类代码——不再需要回调:

下面是一个具有多个回调的更复杂的例子:

你如何把它转换成一个挂起函数？如果你仔细观察，你会发现只有一个回调会被调用，这取决于调用成功与否。在过去的 Java 时代，我们会创建一个`Listener`接口并发送一个内联实现。奇怪的是，Kotlin/Coroutine 解决方案与它有相似的感觉，尽管它在技术上非常不同:

但是，如果我们正在下载一个大文件，并希望取得进展呢？

现在我们期待*多个*调用，所以我们需要重构一个流程:

Notice that if we are returning a Flow, we no longer need the `suspend` attribute

如果您对上述函数的实现感兴趣，请查看我的关于将回调转换为流的文章。

# 3.使用全球范围

这个 [Kotlin 的协程文档](https://kotlinlang.org/docs/coroutines-basics.html#structured-concurrency)明确警告不要使用`GlobalScope`。

`Activity`、`Fragment`、`ViewModel`、`View`...它们都有相应的[生命周期扩展](https://developer.android.com/kotlin/ktx#lifecycle)，您应该使用它们来启动协程。使用这些扩展来自由启动并经常启动，因为协程不像线程一样[便宜且轻量级](https://developer.android.com/kotlin/coroutines#features)。您可以[启动数以千计的协程](https://kotlinlang.org/docs/coroutines-basics.html#structured-concurrency),开销可以忽略不计——重要的是将协程绑定到一个生命周期，以避免内存泄漏。

# 4.不必要的调度

如果你确实需要跳转到另一个线程，尽可能在最近的时候做。使用协程很容易在线程之间来回切换，但是除非万不得已，否则不要编写防御性代码。大多数支持协程的现代 IO 库会在内部处理这种编组，所以你甚至不需要思考，除非你自己去敲打金属。

除了清洁之外，好处还在于测试。一旦您开始与 Dispatchers 打交道，您就必须开始[注入 DispatcherProvider](https://craigrussell.io/2019/11/unit-testing-coroutine-suspend-functions-using-testcoroutinedispatcher/) 以便您可以正确地进行单元测试。这很聪明，但是仍然有不必要的额外开销。

习惯于在 RxJava 上处理线程的团队会纠结于这个概念，并且在从 RxJava 迁移到 Flow 时经常会编写非常防御性的代码:

RxJava 和普通回调一样，不能保证哪个线程被回调——所以你必须有所防备，明确地将回调闭包封送到主线程。这是*结构化并发*背后的另一个目标，在 Kotlin 中通过[上下文保持](https://elizarov.medium.com/execution-context-of-kotlin-flows-b8c151c9309b)来解决。

希望这些片段能帮助你避免常见的反模式，让你的代码保持干净。

ta[Enrique lópez-Maas](https://medium.com/u/f08187f6a023?source=post_page-----2744186be3e--------------------------------)和 [Panini](https://medium.com/u/f85aa4d26d12?source=post_page-----2744186be3e--------------------------------) 进行校对。热爱你的工作。