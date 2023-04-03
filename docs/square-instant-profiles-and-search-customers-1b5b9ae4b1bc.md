# Square Connect SDKs:即时个人资料和搜索客户

> 原文：<https://medium.com/square-corner-blog/square-instant-profiles-and-search-customers-1b5b9ae4b1bc?source=collection_archive---------6----------------------->

![](img/31b01f9b45156dd099b6d5e69df92148.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

Square SDK 2 . 9 . 0 版本(现在是 2.20180712.1，发布了 API 版本)的发布带来了一些非常酷的功能。

你现在可以通过[客户 API](https://docs.connect.squareup.com/growth/customers/overview) 访问*所有*你的客户档案。以前，您只能检索使用 API 创建的客户资料，但现在您也可以查看[即时资料](https://docs.connect.squareup.com/growth/customers/overview#instant-profiles)。

每当客户在销售点使用信用卡时，就会创建一个即时档案。我们会自动在您的 Square 帐户上为该客户创建个人资料。这些旨在让您更轻松地为自己建立客户档案，并在客户选择将[卡保存在](https://squareup.com/pos/card-on-file)文件中时为他们提供便利。

因此，对于那些只看过通过 API 创建的客户资料的人来说，添加即时资料可以获得比以前多 10 倍的访问权限。那就多了很多数据！

还有更多的配置文件要返回——所以我们还发布了一个搜索客户端点，让您可以更轻松地找到和过滤您获得的更多结果。

下面是在搜索客户端点上检索 100 个 Instant Profile 客户的查询:

```
POST https://connect.squareup.com/v2/customers/search
{
  "query": {
   "filter": {
    "creation_source": {
     "values": [
      "INSTANT_PROFILE"
     ],
     "rule": "INCLUDE"
    }
   },
    "sort": {
      "field": "CREATED_AT",
      "order": "ASC"
    }
  },
  "limit": 100
}
```

无论您是将 Square 用于自己的业务，还是为 Square 卖家开发应用程序，您现在都可以访问比以往更多的数据，从而更好地了解您或您的卖家的客户。

如果你正在开发一些使用客户 API(或我们任何其他优秀的 API)的东西，或者想和我们聊得更多，请发推特到 [@SquareDev](https://twitter.com/@SquareDev) 或者加入我们在[squ.re/slack](https://squ.re/slack)的 Slack 社区来告诉我们吧！