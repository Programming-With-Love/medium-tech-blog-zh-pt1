# 用于服务器端应用程序的 Kotlin 介绍—第 1 部分

> 原文：<https://medium.com/globant/an-introduction-to-kotlin-for-server-side-applications-part-1-d9cbcfba34a2?source=collection_archive---------6----------------------->

![](img/1b82fba188fa29c2737263cde4369a2a.png)

Photo by [Chris Ried](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在过去的几十年中，web 和移动应用程序开发发生了重大变化。Java 和 JVM 仍然是服务器端编程的重要平台，但是像 Ruby on rails 这样的流行框架已经被 Node.js 和 Go language 这样的其他工具集所加入。在网络上，像 React 和 Angular 这样的客户端 JavaScript 框架已经变得突出，而 iOS 和 Android 的移动客户端现在主要是使用 Swift 和 Kotlin 构建的。Node.js 特有的好处之一是，开发人员可以专注于使用单一语言 JavaScript 来开发系统的前端和后端部分。使用单一语言有助于开发团队专注于应用程序需求，花更少的时间掌握多种语言，并减少您需要使用的工具集数量。

Swift 和 Kotlin 在移动开发中的出现为开发人员提供了一个类似的机会，让他们在构建前端和后端系统的同时专注于单一语言。此外，这两种语言都是高性能和静态类型的，因此它们也使得开发更安全的后端应用程序成为可能，这些应用程序比 JavaScript 或 Python 等动态类型语言更快，错误更少。

Kotlin 比其他 SDK 有优势，因为它不仅涵盖了移动和 web 上的前端开发，还涵盖了服务器端开发。你不仅可以使用 Kotlin 创建很酷的移动和网络应用，还可以创建与之交互的网络服务！。在服务器端，有几个框架可以用来用 Kotlin 构建 web 应用程序和 web 服务。

在本文中，我们将看到如何使用 http4k 构建一个服务器。它完全是在 Kotlin 中构建的，是一组提供服务和消费 HTTP 服务的功能性工具包的库，专注于简单、一致和可测试的 API。因此，尽管它确实提供了对与服务和消费 HTTP 相关的各种 API*的支持，但它并没有提供所有的集成——仅仅是允许这些集成被挂接的简单点。*

基于来自 Twitter 的令人敬畏的[“你的服务器作为一种功能”](https://monkey.org/~marius/funsrv.pdf)的论文，http4k 应用程序通过组合两种简单、独立的功能来建模。

# 1.HttpHandler

通过将 HTTP 请求映射到响应来将 HTTP 请求处理成响应是一种抽象。

**例如**

这段代码展示了一个由单个 Kotlin function 应用程序组成的全功能应用程序，我们将它嵌入到一个 Jetty 服务器中，这是一个可供我们选择的服务器实现示例。注意这里的类型 HttpHandler，它代表了“作为功能的服务器”这一概念中的两个基本概念之一

# 2.过滤器

这是一种抽象，可以为 HttpHandler 添加预处理和后处理，如缓存、调试、身份验证处理等。过滤器是可组合/堆叠的。

**例如**

这是一个在进行任何操作之前捕捉所有错误的简单示例，同样，我们可以在使用过滤器进行任何 API 调用之前设置 content-type。

每个应用程序都可以由 HttpHandlers 和过滤器组合而成，这两者都是普通 Kotlin 函数类型的简单类型别名。

# 按指定路线发送

[http4k 中的路由](https://www.http4k.org/blog/meet_http4k/#routing)可以处理任意层次的嵌套，这种方式可以完美地工作，因为路由本身会产生一个新的 HttpHandler。

**例如**

# 带镜头的类型安全 HTTP

## 基本定义

镜头瞄准复杂对象的特定部分，以获取或设置值。基本上，它是一个函数——或者更准确地说是两个函数！

```
Extract: (HttpMessage) -> X
Inject: (X, HttpMessage) -> HttpMessage
```

把对象想象成*整体*，把场想象成*部分*。getter 取一个整体，返回镜头聚焦的对象部分。setter 接受一个整体，并接受一个值来设置 part，然后返回一个新的整体和更新的 part。记住，镜头既可以用来拍摄也可以用来拍摄整个物体的一部分。

# http4k 中的镜头

根据透镜的基本思想，http4k 透镜是双向实体，可用于从 http 消息中获取或设置特定值。

相应的描述镜头的 API 以 DSL 的形式出现，它也让我们定义我们安装镜头的 HTTP 部分的需求(可选的和强制的)。由于 HTTP 消息是一个相当复杂的容器，我们可以将镜头聚焦于消息的不同区域:查询、标题、路径、表单域、正文。

# 使用 Lens 从 HTTP 请求中检索值

对于上面的路由示例，我们可以使用查询镜头生成器，然后调用()消息上的镜头来提取目标值:

例如

我们创建一个双向镜头，聚焦于消息的查询部分，从中提取一个必需的非空名称。现在，如果客户端碰巧调用端点而没有提供名称查询参数，那么 lens 会自动返回一个错误，因为它被定义为“required”和“nonEmpty”。

## 使用 Lens 设置 HTTP 请求中的值

这个例子展示了我们如何创建一个 Request 实例，并通过一个或多个镜头注入一个值。我们可以使用 Lens::inject 函数来指定我们想要设置到 Request 的任意实例中的值。

最终代码将如下所示:

# 部署

Kotlin 应用可以部署到任何支持 Java Web 应用的主机上，包括亚马逊 Web 服务、谷歌云平台等等。

# **总结**

我个人在几周内学会了欣赏科特林。一旦您熟悉了基本概念，快速开发前端、服务器端应用程序就变得容易了，这也为移动开发人员熟悉 web 开发打开了大门。我们可以说 Kotlin 是真正的全栈解决方案，使用它我们可以开发多平台应用。我们已经看到了如何使用一个完全用 Kotlin 构建的框架来开发一个简单的服务器端应用程序。

在下一部分，我们将看到 JSON 处理、REST API 示例和一个小应用程序。