# 发布我们客户端库的 2.3.0 版本

> 原文：<https://medium.com/square-corner-blog/announcing-version-2-3-0-of-our-client-libraries-f98c7ab0e5fb?source=collection_archive---------9----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

如果你熟悉[Square 是如何制作 SDK](/square-corner-blog/how-square-makes-its-sdks-6a0fd7ea4b2d)的，那么你就会知道，要了解我们最新 SDK 版本的新特性，最好的方法之一就是查看我们在 github 上的 API 规范的差异([链接](https://github.com/square/connect-api-specification/compare/0a2c7c18685bbfd30c83ef6c6d6052fe619dfa6b...master))。

让我们深入了解每一项变化:

## 订单支持

此版本中最大的更新是对订单端点的支持。新的订单端点允许您在线创建明细交易，是在线销售报告的一大改进。你可以在官方[公告](/square-corner-blog/building-for-an-omni-channel-business-with-squares-apis-has-never-been-easier-3b5e0977741a)中阅读更多内容，或者直接进入[文档](https://docs.connect.squareup.com/api/connect/v2#navsection-orders)。

## 错误修复

在任何新版本中，总会有一些早期的错误被修复。这一项包括以下内容:

*   修正了 [Python SDK](https://github.com/square/connect-python-sdk) readme 中的一个错别字。
*   改进了几个模型属性的描述，比如`BatchUpsertCatalogObjectsRequest`的断开链接。
*   为一些属性添加了一个`maxLength`描述。

看看你喜欢使用的 SDK 的最新和最好的版本，并且花点时间升级。如果您注意到任何问题，请在适当的 GitHub 资源库中提出问题，我们会尽快解决。