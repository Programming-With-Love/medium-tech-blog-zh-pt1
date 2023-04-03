# 如何在 Android 上使用 Ktor 客户端

> 原文：<https://medium.com/google-developer-experts/how-to-use-ktor-client-on-android-dcdeddc066b9?source=collection_archive---------0----------------------->

## 使用 Ktor 客户端走向 Kotlin 多平台

![](img/bbfaf8d07ad395f1ed26d15032bd681c.png)

[https://github.com/ktorio/ktor](https://github.com/ktorio/ktor)

Ktor 是一个异步开源框架，用于创建微服务和 web 应用。它是由 Jetbrains 和 Kotlin 一起开发的。它易于安装和使用，如果您想在执行管道中添加一个步骤，它是可扩展的。由于[协程](https://developer.android.com/kotlin/coroutines)和[多平台](https://kotlinlang.org/docs/reference/multiplatform.html)，它的异步属性允许它接收多个请求。

Ktor 是多平台的，是的多平台！这意味着我们可以在任何地方部署 Ktor 应用程序，并且不仅仅是作为服务器，我们还可以使用 Ktor 客户端在 Android、iOS 或 JS 上消费微服务。Ktor 客户端允许你发出请求和处理响应，用[特性](https://ktor.io/docs/http-client-features.html)扩展它的功能，比如认证、JSON 序列化等等。

我们将学习如何配置，如何创建客户端实例，以及如何使用 REST API。

让我们从在构建梯度中包含以下依赖项开始。

第一个依赖项是处理网络请求的[引擎](https://ktor.io/docs/http-client-engines.html)，在这种情况下，我们将使用 android 客户端。第二个依赖项用于 JSON [序列化和反序列化](https://ktor.io/docs/json-feature.html)设置，建议用于多平台项目。第三个依赖项是 [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) ，用于实体序列化。最后，第四个用于[记录 HTTP 请求](https://ktor.io/docs/features-logging.html)。

现在，我们继续在项目 gradle 中包含 [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) 插件。

现在让我们配置引擎并创建客户机实例。

根据[引擎](https://ktor.io/docs/http-client-engines.html#dependencies) (Android、Apache、OKHttp 等)，你可以拥有不同的属性。我们在此配置中执行了以下操作:

*   首先，我们通过添加各种属性，用 kotlinx.serialization 配置 JSON 序列化程序/反序列化程序。我们还定义了一个超时周期。
*   其次，我们配置日志来创建 HTTP 请求日志。
*   第三，我们配置一个观察器，它将记录响应的所有状态。
*   最后，我们可以为每个 HTTP 请求设置默认值。在这种情况下，我们在标题中定义属性，这些属性将出现在所有请求中。

搞定了。Ktor 现在已经配置好了，实例可以使用了。保持冷静，这是一次性工作。让我们使用它！

我们开始定义实体:

我们继续配置用户 Api:

我们继续配置存储库:

最后，我们将使用扩展函数处理异常:

# 更新—测试客户端

如果您需要为使用 Ktor 客户端的方法创建单元测试，您应该使用框架提供的 [MockEngine](https://ktor.io/docs/http-client-testing.html#test-client) 类，该类允许您定义具有所需内容的响应。

首先创建一个 MockEngine 实例，然后作为参数传递给 HttpClient 类。

Testing a Ktor client

恭喜你。我们已经用 Ktor 配置、创建了客户机，并创建了对 API 的请求。它的优点是实现简单，是多平台的，在内部使用协程，不使用注释，并且有多种配置选项，这取决于使用案例和需求。一个缺点是，它的设置和使用比其他方式要繁琐一些。

希望你觉得有用，记得分享这篇文章。

随时欢迎任何评论！

# **资源**

*   【https://ktor.io/ 
*   [https://developer.android.com/kotlin/coroutines](https://developer.android.com/kotlin/coroutines)
*   [https://kotlinlang.org/docs/reference/multiplatform.html](https://kotlinlang.org/docs/reference/multiplatform.html)
*   [https://ktor.io/docs/http-client-engines.html](https://ktor.io/docs/http-client-engines.html)
*   [https://github.com/Kotlin/kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization)
*   [https://ktor.io/docs/http-client-testing.html](https://ktor.io/docs/http-client-testing.html)