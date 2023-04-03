# 在 Oracle Commerce Cloud Search 中执行地理位置搜索和过滤

> 原文：<https://medium.com/oracledevs/performing-geolocation-searches-and-filtering-in-oracle-commerce-cloud-search-646646e11fd8?source=collection_archive---------0----------------------->

许多零售商在其网站上提供“查找最近的商店”功能。随着作为 Oracle Commerce Cloud 19A 版本的一部分而添加的特性，搜索可以通过使用 API 来提供这种功能。

本文将介绍准备数据、更新配置、索引和查询的必要步骤。

我还[创建了一个 Postman 工作区](https://www.getpostman.com/collections/082136bb6616caa92380),其中包含了本教程中使用的 REST 查询，我强烈建议您使用 Postman 来完成本教程。

在我最近帮助运行的关于索引额外内容的网上研讨会中，涵盖了很多这种材料。[该网络研讨会可在甲骨文合作伙伴网络上观看](https://videohub.oracle.com/media/Technical+Dives+with+Commerce+CloudA+Extending+Search+%26+Dynamic+Curation/0_yfkfwgdo/74880262)。地理定位的工作流程与我们在网上研讨会中索引的买家指南几乎相同，因此如果您有详细的问题，请观看。

# 使用邮递员工作空间

我计划将来为我的文章共享邮递员工作空间，因为它可以让你确切地看到我在做什么。为了使用这个工作区，您需要设置几个环境变量:

*   ADMIN_URL:管理服务器的完整 URL。这应该包括 https://等。
*   SF_URL:同上，但指向您的店面服务器
*   app_key:在 OCC 管理中，您必须创建一个 REST API 应用程序密钥

一旦设置了这些环境变量，您就应该能够执行“登录管理员”请求。当你这样做的时候，另一个环境变量被神奇地创建了，叫做“token”。这允许对工作区中的所有后续查询进行身份验证。

提醒:登录计时器只能持续一小会儿，因此在此过程中您可能需要执行几次“登录管理员”操作。

要在 OCC 创建“app_key ”,请执行以下操作:

1.  登录 OCC 管理
2.  转到设置-> Web API
3.  点击注册的应用程序
4.  +注册的应用程序
5.  给它起个名字(我称我的为“邮差”)
6.  单击您的应用程序名称，然后“单击显示”应用程序密钥
7.  完整复制该密钥(它相当长)。作为参考，我的应用程序密钥有 222 个字符长。我相信它们总会以 a =结尾

# 创建和搜索地理位置数据的步骤摘要

总体步骤将是:

*   按照下面描述的格式将数据准备为 JSON 文档
*   将商店位置数据发布到/gsdata/v1/cloud/data/stores
*   在/GS admin/v1/cloud/attributes/stores 配置自定义属性
*   在/GS admin/v1/cloud/pages/Default/storeSearch 创建一个定制的搜索服务来处理对这些数据的查询
*   启动索引
*   查询数据

# 准备您的数据

下面是一个包含商店信息的示例记录。

```
{
    "items": [   
          {  
               "content.type": "store",  
               "record.id" : "store-1",
               "store.name": "ACME NJ",  
               "store.city": "Newark",  
               "store.zip": "07076",  
               "store.address": "123 Main St",  
               "store.state": "New Jersey",  
               "store.phone": "908-123-4567",  
               "store.geocode" : "40.735657,-74.172363"

          },
....
}
```

需要指出的关键信息是地理编码格式。这是纬度逗号经度。不要加多余的空格什么的。

我们将在下面的查询中只使用“store.geocode”字段。其他值表示您可能希望在屏幕上呈现的示例数据。

# 上传您的数据

关于上面的 JSON，每个商店的商品数组中有一个商品。

当您的数据准备好了，您应该将它发布到/gsdata/v1/cloud/data/stores。请注意，该 URL 中的“商店”是您自己选择的，就像“购买指南”是如何在 OPN 网络研讨会中创建的一样。

您还可以获取该 URL 来查看上传的文档。有关这方面的更多信息，请参考网络研讨会和其他 OPN 内容(如[https://community . Oracle . com/groups/Oracle-commerce-cloud-group/blog/2019/03/11/enhancing-your-search-with-the-type-ahead-framework](https://community.oracle.com/groups/oracle-commerce-cloud-group/blog/2019/03/11/enhancing-your-search-with-the-type-ahead-framework))

# 定义属性

在上面的 JSON 中，我们创建了 store.geocode、store.phone 等属性。这些是您自己创建的属性名，这意味着我们必须在它们出现在索引中之前定义它们。

我共享的 Postman 工作空间有完整的属性列表和它们的模式，所以我不会粘贴到这里。相反，下面是“store.geocode”的属性定义。

```
"store.geocode": {
    "ecr:lastModifiedBy": "admin",
    "propertyDataType": "GEOCODE",
    "ecr:type": "property"

  },
```

请注意，它是 ecr:type = property，并且 propertyDataType=GEOCODE。

将这些自定义属性发布到/GS admin/v1/cloud/attributes/stores。像上面的商店记录一样，URL 中的“商店”是您自己选择的。正如我在其他一些文章中提到的，我不再推荐向/attributes/system 添加新的定制属性。相反，根据记录类型创建您自己的文件夹。单词“stores”并不重要，因为所有属性都合并在一起了。

如果需要更新属性，那么获取/GS admin/v1/cloud/attributes/stores，修改 JSON，然后放回去。放置替换，发布创建。

# 创建商店搜索服务

至此，我们已经上传了数据并创建了属性配置。现在我们需要准备一个搜索服务来查询这些数据。

这是一个基本的商店搜索服务。将此内容发布到/GS admin/v1/cloud/pages/Default/store search

```
{
  "ecr:lastModifiedBy": "admin",
  "contentType": "Page",
  "ecr:type": "page",
  "contentItem": {
    "[@name](http://twitter.com/name)": "Store Search Service",
    "[@type](http://twitter.com/type)": "TypeaheadService",
    "[@appFilterState](http://twitter.com/appFilterState)": {
      "[@type](http://twitter.com/type)": "FilterState",
      "recordFilters": [
        "content.type:store"
      ]
    },

    "resultsList": {
      "[@type](http://twitter.com/type)": "ResultsList",
      "relRankStrategy": "maxfield,glom,nterms,static(guides.description)",
      "recordsPerPage": "20",
       "fieldNames": [
        "store.name", "store.address", "store.zip", "store.state", "store.phone", "store.city"
      ]
    }
  }
}
```

需要指出的重要一点是:

*   我们在“content.type:store”上有一个记录过滤器。这将服务限制为只搜索我们新的商店位置记录(在我的示例数据中有值“content . type”:“store”)
*   我们配置“字段名称”来列出我们创建的要返回的新属性。在我的上一篇文章中，我描述了我们如何使用“字段名称”和“子记录字段名称”来限制返回的数据。

说到地理位置数据，我们并不是真的用关键字搜索来搜索它。所以 relrank 策略并不重要。

相反，我们执行排序和过滤，正如我们将在下面看到的。

# 启动索引

在这一点上，我们准备开始索引。为此，POST /ccadmin/v1/search/index？op =基线

它将获取数据、属性和搜索服务，并用这些信息更新索引。请注意，我们使用了“op=baseline”，因为当模式发生变化时(特别是当我们添加到/attributes 中时)，基线是必需的。

(有关索引的更多背景信息，请参阅我的前一篇文章“[了解 Oracle Commerce Cloud 中的搜索索引](/oracledevs/understanding-indexing-for-search-in-oracle-cloud-commerce-e4829109c03a)”)

# 查询地理位置数据

通常使用地理位置数据执行两种查询。这些将是:

*   “按距离显示离我最近的商店列表”
*   "显示此位置 X 公里范围内的商店列表"

通过搜索，两者都是可能的。

在我的样本数据中，我创建了 4 个商店:新泽西州纽瓦克、纽约市、科罗拉多州丹佛和华盛顿州西雅图。

首先，这里有一个示例查询，它只是根据离新泽西州特伦顿的距离对商店进行排序。

```
/ccstoreui/v1/assembler/pages/Default/storeSearch?N=0&Ns=store.geocode(40.217052,-74.742935)
```

因此，让我们深入这个查询:

*   /assembler/pages/Default/storeSearch 指向我们创建的定制搜索服务
*   store.geocode 是我们在数据中创建并在/attributes/stores 下定义的字段名称
*   Ns=表示排序。
*   Ns=store.geocode(40.217052，-74.742935)基本上指示索引基于该位置进行排序(这是新泽西州特伦顿的位置，离新泽西州纽瓦克更近，纽约市紧随其后)

在下一个查询中，我们将根据距离进行过滤:

```
/ccstoreui/v1/assembler/pages/Default/storeSearch?N=0&Ns=store.geocode(40.217052,-74.742935)&Nfg=store.geocode|40.217052|-74.742935|150
```

好的，这是和上面一样的查询，但是现在我们增加了&Nfg=参数。把这个想象成“导航过滤地理编码”。

对于此字符串，格式为:

```
fieldName|latitude|longitude|radius(kilometers)
```

因此，在我们的示例中，我们筛选新泽西州特伦顿 150 公里以内的记录。然后，对于该组结果，我们根据离该位置的距离进行排序。(您也可以在不排序的情况下进行过滤。他们没有绑在一起)

通过这两种类型的查询，您可以实现典型的商店位置搜索。

请注意，管道字符需要在您的 web 应用程序中进行 URL 编码。在 Postman 中是没问题的，但是你将需要在你的前端代码中编码它。

# 关于地理定位距离测量的一点注记

关于索引如何计算距离的快速补充说明。

1.  距离公式是基于检测球体上的距离
2.  距离公式使用“直线距离”作为值。意思是，它没有考虑街道、高速公路等。谷歌地图或其他一些应用编程接口必须提供这种详细程度

# 结束语…

这在“显示浏览器报告的位置附近的商店”的上下文中最有用。Javascript 有一个 api，你可以在那里访问位置(详细一点[这里](https://stackoverflow.com/questions/11667838/how-do-i-get-an-addresses-latitude-longitude-using-html5-geolocation-or-google-a))。

然而，还有其他方法来实现这一点。例如，如果您向购物者询问邮政编码，可能需要这样的内容:

*   询问邮政编码
*   使用 web 服务将邮政编码翻译成经度/纬度
*   执行上述查询

希望你觉得这很有用。请记住，您必须在 19A 或更高版本上才能访问/gsdata 端点来加载数据。

如果您对未来文章的主题有任何建议，请告诉我！