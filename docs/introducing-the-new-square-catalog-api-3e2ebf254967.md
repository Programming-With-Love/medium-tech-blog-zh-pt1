# 介绍新的 Square 目录 API

> 原文：<https://medium.com/square-corner-blog/introducing-the-new-square-catalog-api-3e2ebf254967?source=collection_archive---------0----------------------->

## 新的 Square [Catalog API](https://docs.connect.squareup.com/articles/catalog-overview) 公开了我们所有的平台改进，同时还允许开发人员提高他们的项目库解决方案的效率。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

公开允许我们的合作伙伴为 Square 卖家构建有意义和令人愉快的体验的 API 是满足我们多样化卖家社区需求的基础。对于大多数卖家来说，他们的物品库对他们的成功至关重要，所以当我们首次推出我们的 API 平台时，我们展示的介绍性端点之一是[物品](https://docs.connect.squareup.com/api/connect/v1/#navsection-items)。

自那次发布以来，Square 继续投资于我们的项目平台(又名 Catalog)，支持规模更大、延迟更短的大型项目库，并引入了一流的多位置支持。我们对平台的持续投资是对我们卖家的投资，因此随着他们的成长，我们的平台将继续支持他们所有的物品库需求。

# 连接 V2

此次发布将我们用于访问项目的 API 端点转移到 Square 的最新 API 平台 Connect V2。这确保了 Catalog API 的性能和可靠性符合我们的销售人员和开发合作伙伴对 Square 的期望标准。

# 多位置目录

我们的目录 API 将我们的项目管理 API 从基于位置的解决方案转变为基于卖家的解决方案。如果您有一个或多个位置，我们的 API 允许您定义和管理单个项目库。在单个项目库中，项目可以跨位置共享，也可以仅在单个位置激活。这不仅更具可扩展性和效率，而且与企业概念化和管理产品的方式相一致。

# 批量操作

为了确保使用 Catalog API 构建的解决方案对拥有许多项目和地点的卖家保持高效，我们提供了批量 API 端点，用于更新一个或多个地点的项目变化的价格、折扣或税收等信息。

# 附加阅读

*   API 参考[页面](https://docs.connect.squareup.com/api/connect/v2/#navsection-catalog)
*   支持的 SDK:[Ruby](https://github.com/square/connect-ruby-sdk/)， [Python](https://github.com/square/connect-python-sdk/) ， [PHP](https://github.com/square/connect-php-sdk/) ， [C#](https://github.com/square/connect-csharp-sdk) ， [Java](https://github.com/square/connect-java-sdk/)