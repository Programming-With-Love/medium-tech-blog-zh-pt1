# 为什么后端 Javaer 应该尝试 Kotlin

> 原文：<https://medium.com/google-developer-experts/why-backend-javaer-should-try-kotlin-c4003b5edb74?source=collection_archive---------3----------------------->

![](img/238a335f40e6bb0c9cc401fe07b02305.png)

Photo by [Elisabeth Wales](https://unsplash.com/@elventhorncreations?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我以前是一名专注于后端的 Java 开发人员。Spring 是我最熟悉的框架。因为 Spring Framework 5 高度支持 Kotlin。是时候用 Kotlin 试试 Spring 框架了。

如果你用谷歌搜索为什么是科特林，为什么是科特林，为什么是 T2。&缺点。、**科特林 vs 爪哇**、等。有很多很棒的文章可以互相解释和比较。所以我就不再重复了。但即使我们知道 Kotlin 有许多令人敬畏的部分，为什么许多 Java 开发人员倾向于继续使用 Java 而不是 Kotlin？

嗯，说来话长。但这部分不是我今天要重点讲的重点。但是如果我们从商业角度考虑，继续使用 Java 是一个合理的选择。因为连写 Kotlin 代码都比 Java 舒服。我认为最有力的原因是 Java 已经有了一个大的生态系统来支持大多数的产品案例。

所以今天我想展示一个后端服务器端的场景来解释这个标题。我将使用**协程**并发发送或处理请求，并与我们过去使用的其他代码风格进行比较。

在前端，并发发送请求是常见的场景。但是在过去，在 Java 后端并发地发送或处理请求并不流行。我觉得有以下几点原因。

1.  Servlet(每个线程每个请求)和阻塞请求的模型非常容易学习和使用。如果您的服务器不需要处理巨大的流量，垂直扩展是一个简单而有用的想法。
2.  并发请求风格比顺序阻塞请求风格更复杂。
3.  生态系统最近增加了支持。例如，Spring 5 反对 RestTemplate(阻塞的),建议我们改用 WebClient(非阻塞的)

> 空谈不值钱。给我看看代码。

我为 2–1–2 个请求设计了一个案例，这在我们真实的项目场景中非常常见。这意味着我们可以同时发送前两个请求。第三个请求需要前两个请求的结果。最后两个请求也需要第三个请求的结果，我们可以同时发送它。

原始阻塞请求示例如下所示。

从代码中可以看出我们是否顺序调用了请求。花费了 100+200+300+400+500=1500ms。但是我们之前解释的 2–1–2 情况意味着一些请求可以并发发送。

让我们检查一下协程版本！

我们同时发送前两个请求，所以只需要 200 毫秒。第三个请求需要前两个的结果，我们发送它，花费 300 毫秒。最后两个请求也需要第三个请求的结果。我们同时发送这两个，所以要花 500 毫秒。

因此，与原始样本相比，成本为(200+300+500 = 1000 毫秒)< (100+200+300+400+500=1500ms).

This Coroutine sample shows a very important point that Java can not replace it. **协程可以以顺序阻塞代码的方式编写并发代码。**

为什么这个简单的想法是有价值的？如果我们考虑其他解决方案，如手动处理线程池、CompletableFuture、反应式编程等。这些技术和协程是基于后台线程池之上的。但是其他解决方案很难写得简单易读。我在下面展示了 CompletableFuture 和 Reactive 编程案例，如果你有更好的版本，请反馈给我。

Servlet 3 Async feature support to return a Future

Spring WebFlux support to return a Mono or Flux

显然，协同程序代码风格比其他代码风格更简单，可读性更好。但我认为最重要的一点是在正确的场景下用正确的库和编码风格编码。例如，如果你的团队熟悉反应式编程或 CompletableFuture。呆在上面完全没问题。但是如果我们只比较这些技术，显然协程更容易学习和编写。

现在值得尝试 Coroutine，来和我一起用 Kotlin 的 Spring 框架吧！