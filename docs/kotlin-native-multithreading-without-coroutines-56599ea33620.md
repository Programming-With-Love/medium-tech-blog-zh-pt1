# 科特林本地人。无协程的多线程

> 原文：<https://medium.com/google-developer-experts/kotlin-native-multithreading-without-coroutines-56599ea33620?source=collection_archive---------1----------------------->

![](img/9982e95287268f37c04f2aa5884cbc32.png)

在之前的故事中，我们讨论了如何在 Kotlin Native 中使用协程进行简单的多线程处理。这次我们将讨论使用 Kotlin native 的 Native 机制来处理异步工作。

当我们在 Kotlin 多平台应用程序中使用 Ktor 库时，我们知道所有异步工作都是在 Kotlin 本机机制的帮助下实现的。我们来看看，试着在自己的网络客户端中应用一下。

首先，我们将为我们的 iOS 和 Android Http 客户端制作一个通用接口。我们将以不同方式实现它们，但没有预期/实际的机制:

我们可以使用 OkHttp 来提供 androidMain 中的所有逻辑。对于 iOS，我们将在通用模块的 iOSMain 部分使用 NSUrlSession。在 iOSMain 中，所有的原生框架(如网络)都是可用的和可访问的。

我们将使用标准的 NSUrlSession 方法和一些代表来接收响应:

因此，在这一点上，我们需要提供一些机制来从后台调用我们的任务。为此科特林本地有工人。Worker 是一个作业队列，您可以在其上调度作业在不同的线程上运行。每个工作线程都在自己的线程中执行工作:

有几个细节:
1。我们需要使用 TransferMode。对于线程安全的工作是安全的。

2.我们需要冻结一段代码，以便在不同的线程中执行它。在我的例子中，我使用 share()扩展来封装冻结。

作为执行的结果，我们得到了一些未来。为了处理它的完成和结果值，我们可以使用 consume:

我们也可以将完成发送到另一个线程中。例如，它可以是主线程和主队列:

这可以用我们以前创建自己的 MainDispatcher 的方法来完成。
让我们将它应用到我们的示例中。此外，我们需要冻结我们将在客户端中使用的所有参数和块:

当我们用委托方法实现自己的网络客户机时，我们可以将所有数据作为独立的部分接收。所以我们需要使用字节数组将所有的数据保存在一起:

而且还有一个很大的问题。当冻结我们客户的所有东西时，我们甚至冻结了 var 变量。现在，我们无法在不崩溃的情况下更改数据。
为了处理它，我们需要使用 AtomicReference API。AtomicReferences 保存冻结变量的链接，但允许更改它们的值:

使用 AtomicReferences 时存在潜在的泄漏风险。所以我们需要在工作结束后清除它们:

完成了。让我们从我们的 iOSMain 端调用:

并且不需要任何协程就可以工作:
[https://github . com/anioutkazharkova/kot Lin _ native _ network _ client](https://github.com/anioutkazharkova/kotlin_native_network_client)