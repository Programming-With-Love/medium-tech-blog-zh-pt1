# 我们的客户端 SDK 版本 2.5.1

> 原文：<https://medium.com/square-corner-blog/version-2-5-1-of-our-client-sdks-a1560b0f9b66?source=collection_archive---------8----------------------->

## 假期还没到，但是随着这些发行，感觉就像圣诞节一样！

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们一直在努力扩展我们的 API 和客户端库，为开发人员提供他们需要的工具来构建与 Square 业务的集成。以下是我们最新版本(2.5.1)中的新增功能:

*   一个新字段(`ordinal`)被添加到`CatalogItemVariation`模型中。序号是 Square Point of Sale 应用程序显示项目的顺序。
*   `location`模型包括一个用于`website_url`的字段，这是一个商家在制作他们的 Square 帐户时输入的网站。
*   `Tender` objects 获得了一个新字段`time_money`来表示交易中的小费金额。
*   `V1PageCell`现在更准确地匹配底层数据模型。

GitHub 中已经提供了所有最新的 SDK，您可以通过您喜欢的包管理器进行更新。

*   [Java](https://github.com/square/connect-java-sdk/)
*   [Python](https://github.com/square/connect-python-sdk/)
*   [红宝石](https://github.com/square/connect-ruby-sdk/)
*   [C#](https://github.com/square/connect-csharp-sdk/)
*   [PHP](https://github.com/square/connect-php-sdk/)

如果你对我们最新的 SDK 版本有任何问题，请在评论中告诉我们，或者在 twitter 上联系 [@SquareDev](https://twitter.com/squaredev) 。你也可以在⬇️.下面看到官方的发布说明

 [## Square SDK 2.5 发布(2017-11-10)

### 接受来自 iPhone、Android 或 iPad - Square 的信用卡

docs.connect.squareup.com](https://docs.connect.squareup.com/articles/square-sdk-release-notes-2017-11-10)