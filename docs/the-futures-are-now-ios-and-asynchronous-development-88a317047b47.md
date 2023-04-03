# 未来是现在——iOS 和异步开发

> 原文：<https://medium.com/capital-one-tech/the-futures-are-now-ios-and-asynchronous-development-88a317047b47?source=collection_archive---------1----------------------->

![](img/cc9092e9d0b3de774c1e52fcbf893fc3.png)

在编程中，我们经常遇到异步操作。这可能包括网络、文件系统、数据库、UI、长时间运行的任务或任何其他 I/O 事件。在 iOS 中，我们有许多机制来处理这个问题，包括 GCD、NSOperation、NSNotifications、delegates 和 callbacks。

一个在 iOS 上不太常见的构造——但在 JavaScript 和 Scala 等其他语言中非常成熟和常见——是期货。也称为承诺、延期、任务或异步；这种技术允许您以同步方式处理异步值，将连续传递样式的调用切换为直接样式。通过使用 futures，你的程序将更容易推理，因为不会有太多的嵌套调用。

但是未来到底是什么？未来是一个还不存在的价值的表示。当你处理未来的时候，你不会知道这个值当前是否可用。但是，您可以像使用该值一样使用它。一旦该值变得可用，未来将处理您的指令。

## 未来的框架

如果你想在你的 iOS 应用中开始使用期货，你有几个不同的第三方库可供选择，包括:

**-**[](https://github.com/Thomvis/BrightFutures)

****-**[**promise kit**](https://github.com/mxcl/PromiseKit)**

****-**[**future kit**](https://github.com/FutureKit/FutureKit)**

****——**——[——**——**——](https://github.com/bignerdranch/Deferred)**

****-**-[-**螺栓**-](https://github.com/BoltsFramework)**

**根据您选择的库，您将有不同的设计选择。例如，Big Nerd Ranch 的 Deferred library 的灵感来自 OCaml 的 Deferred。这意味着 future/deferred 值并没有说明异步操作是否已经失败。要表示失败，您需要通过返回结果类型或类似的关联枚举来将其表示为值的一部分。BrightFutures 遵循 Scala 的承诺和未来，因此有一个隐含的理解，即每个未来都可能失败。FutureKit 和 Bolts 还包含一个“取消”状态，表示用户取消了未来/任务，不再关心结果。**

**这些库可能有不同的方法和设计目标；然而，它们都解决了同一个问题，即提供一个接口来表示和处理异步结果。它们也都处理一些常见的任务，比如一起排序期货、并行运行期货以及在特定线程上处理结果。**

**所以让我们开始吧。**

## **只是最基本的**

**使用期货最基本的方法是设置一个完成处理程序。这只是允许您在值可用时执行一个操作。要设置处理程序，您可以通过调用*on success*(bright futures/FutureKit)、*on*(Deferred)、 *then* (PromiseKit)或*continue onSuccess with*(Bolts)来设置未来的延续函数。这些框架中的大多数还允许您通过调用 *onFailure* (BrightFutures)、 *onError* (FutureKit)或 *catch* (PromiseKit)来附加一个错误处理程序。**

**这应该感觉类似于只使用回调来处理异步任务。这里的主要区别是您仍然有一个 userFuture 对象，您可以对其执行进一步的处理，比如添加更多的完成处理程序或者传递给另一个函数或组件。**

## **用未来绘制地图**

**当处理期货时，我们可以传递未来，而不需要用回调来处理价值。假设我们有一种类型的未来(比如 *Future < JSON >* )，但是我们需要另一种类型的未来(比如 *Future < User* >)。如果我们有一个函数可以将第一个内部类型(JSON)转换成第二个内部类型(*用户*)，我们可以在*未来< JSON >* 上调用 map，传入映射函数得到一个*未来<用户>。***

## **测序未来**

**在处理多个未来的时候，有时候你会需要一个未来的结果来得到另一个未来。例如，如果您需要在获取用户地址之前查找用户，您可能需要像这样连接两个请求:**

**虽然这是正确的，但是请注意您的主逻辑将如何嵌套在两个完成块中。还要注意如果 getUser 调用失败，我们将无法运行 getAddress。这种链接或排序期货的行为非常常见，期货上有一个函数可以调用来完成这一操作。该函数或者被命名为*flat map*(bright futures/Deferred)， *then* (PromiseKit)，*continueWithTask*(Bolts)，或者 *onSuccess* (FutureKit)。**

## **并行的未来**

**另一种常见的情况是，当你有多个相互不依赖的期货时，你需要所有的值才能继续。大多数未来框架都有一个功能，将一组未来转换成一个包含一组价值的未来。如果所有的期货都成功，新的期货将返回一个包含所有期货值的数组。这个函数被称为*序列*(光明未来)*任务.当所有*(螺栓)*加入值*(延迟)或*当*(承诺)。**

## **多个异步值**

**虽然未来是伟大的，但它们并不适合每一种异步处理。例如，如果你想开发一个聊天应用，你需要处理来自其他用户的无限多的消息。在这种情况下，需要一个反应式结构(如*信号、可观测信号、*或*流*)。它们类似于期货，除了它们提供一系列的值，而不仅仅是一个值。期货实际上可以被认为是一种特殊的信号，一种只返回一个值的信号。有关这方面的更多信息，请查看 [RxSwift](https://github.com/ReactiveX/RxSwift/) 或 [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) 框架。**

## **句法糖**

**另外需要注意的是，通过为 Swift 编程语言提供额外的关键字，我们可以使我们的代码看起来更像同步编程，同时保留异步编程的优势。C#和 JavaScript 已经用 *async/await* 关键字做到了这一点。在 Swift 中可能是这样的:**

***retrieve*实际上会返回一个 *Future < Int？>* ，而 *retrieveUserFromDB* 会返回一个*未来的<用户吗？>T25。通过使用 await 关键字，代码的其余部分变成异步的，并将一直等到 retrieveAge 返回，有效地将代码的其余部分放入如下所示的 *onSuccess* 处理程序中:***

**请注意使用 *await* 是如何隐藏期货正在被使用的事实，并使代码看起来像同步命令式编程，这应该为许多人所熟悉。**

**截至 Swift 3，Swift 尚不具备该功能。然而，根据 swift-evolution 邮件列表上的[这条消息](https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20160725/025676.html)，克里斯·拉特纳表示这可能是 Swift 4 Stage 2 的成果。所以希望我们最终能看到这一点。**

## **结论**

**如果需要处理异步结果，期货是一个很好的构造。与简单的回调相比，它们有很多好处，因为您可以传递它们，并按顺序或并行地将它们组合在一起。目前，您必须使用第三方框架来获得这一功能，但是现在要了解未来，这样您的编程工具库中就有了这一工具。这样，您就可以为未来完全融入 Swift 编程语言做好准备。**

***最初发表于*[*【developer.capitalone.com】*](https://developer.capitalone.com/blog-post/the-futures-are-now-ios-and-asynchronous-development/)*。***

***欲了解更多关于 Capital One 的 API、开源、社区活动和开发人员文化的信息，请访问我们的一站式开发人员门户网站 DevExchange。*[*https://developer.capitalone.com/*](https://developer.capitalone.com/)**