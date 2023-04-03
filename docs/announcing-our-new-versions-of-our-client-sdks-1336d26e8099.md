# 宣布我们客户端 SDK 的新版本

> 原文：<https://medium.com/square-corner-blog/announcing-our-new-versions-of-our-client-sdks-1336d26e8099?source=collection_archive---------1----------------------->

## 我们将对我们的第一方 SDK 进行重大更新，并为开发人员提供一个新的 Slack 社区！

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

**【更新:公测现已结束；您可以在此** **下载我们客户端 SDK**[**的最新版本。]**](https://docs.connect.squareup.com/articles/client-libraries)

在 Square，我们希望尽可能快速简单地构建与我们平台的集成。为了帮助解决这个问题，我们正在对我们的 SDK 进行重大更新，以扩展它们的功能，使它们更容易使用。我们还推出了一个 Slack 社区来收集关于我们即将发布的新 SDK 的反馈。如果您有兴趣讨论我们的新 SDK，或者只是想会见其他 Square 开发者，请点击下面的链接将您的名字加入邀请名单。

[**在松弛状态下用正方形构建**](https://docs.google.com/forms/d/e/1FAIpQLSfAZGIEZoNs-XryKqUoW3atFQHdQw5UqXLMOVPq3V4DEq-AJw/viewform?usp=sf_link)

## 关于我们如何制作 SDK 的简短说明

我们一直致力于为我们的 API 开发新功能，所以我们在 SDK 创建过程中自由使用了 [OpenAPI 规范](https://www.openapis.org/),以避免手工制作每一个。我们在 GitHub 上的[square/connect-API-specification](https://github.com/square/connect-api-specification)存储库中发布我们的规范文件。然后，我们使用 [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) 项目将我们的规范转化为 PHP、Python、Ruby、C#和 Java 的客户端库。我们花了很长时间投资与 [Travis CI](https://travis-ci.org/) 持续整合这一流程，让您全面了解整个流程。

## `System.out.println("Hello Java!");`

我们非常兴奋地宣布我们的第一个 Java SDK。Java 是使用我们的 API 的开发人员最流行的语言之一，他们已经被忽视太久了。这个版本是我们新的持续集成管道的直接结果，它应该允许我们目前不必快速跟进的其他语言。你可以在 [Github](https://github.com/square/connect-java-sdk) 上获取我们的 Java SDK 的源代码，或者用 Maven 或 Gradle 安装。

# …最后:五个新的 SDK！

今天，你会看到我们的每个 SDK 仓库都有一个名为`release/2.1.0`的分支。这个分支(以及我们未来的 SDK)有几个明显的不同之处:

*   完全支持我们所有的 v1 和 v2 端点，这意味着如果您使用流行的 v1 端点，如项目和库存管理，您将不必直接调用我们的端点。
*   新的授权格式。以前，我们的 SDK 要求开发人员在 PHP: `$api_instance->listLocations('sqatp-xxx...');`中的每个请求中都包含您的授权令牌。现在你只需要在你的配置中设置一次这个令牌`->setAccessToken(‘YOUR_ACCESS_TOKEN’);`，之后你做的每一个 API 调用都会变得更加简洁:`$api_instance->listLocations();`
*   我们重命名了一些 API。例如，现在有了`SquareConnect\Api\Location**s**Api();`，而不是`SquareConnect\Api\LocationApi();`

这些是一些大的变化，如果你试图在不改变任何逻辑的情况下升级你的 SDK，它会破坏你的应用。这就是为什么我们发布这些 SDK 的测试版，你今天可以在 github 上找到它。您可以立即使用`release/2.1.0`开始为升级准备您的应用程序，或者查看每种语言的[安装指南](/square-corner-blog/how-to-install-the-beta-sdks-b746503515d9)。

我们将主动联系开发者，确保你了解这些变化，我们的新社区，以及如何更新应用程序。在接下来的几个月里，我们将整合反馈，并对我们的 SDK 进行最后的润色。请关注这个博客、twitter 上的@SquareDev 和我们新的 [Slack 社区](http://squ.re/slack)以获取更多信息！