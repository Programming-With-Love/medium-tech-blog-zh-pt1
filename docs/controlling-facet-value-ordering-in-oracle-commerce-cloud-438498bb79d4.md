# 控制 Oracle 商务云中的方面排序

> 原文：<https://medium.com/oracledevs/controlling-facet-value-ordering-in-oracle-commerce-cloud-438498bb79d4?source=collection_archive---------0----------------------->

在呈现产品列表和搜索结果页面时，将调用/ccstore/v1/search 来检索搜索结果和各种其他数据，包括分面导航、面包屑、排序关键字。

在呈现方面列表时(有时也称为引导式导航，或分面导航)，有一些 API 可以控制这些方面的顺序。例如，您可能希望首先显示类别，然后是品牌，然后是价格，然后是其他方面。

有了这些 API，我们可以完成对分面导航的大量控制。我们可以重新排序方面，我们可以禁止方面出现，我们可以创建每个集合的排序等等。

本文试图采用许多收集到的最佳实践和技巧来提供有用的蓝图，以便更好地控制您的站点。

# 设置默认方面顺序的步骤概要

设置站点范围的默认方面排序的整个过程如下:

*   GET/GS admin/v1/cloud/content/facets/default
*   为我们想要特别订购的每个方面配置 JSON
*   配置我们是要显示所有其他方面(还是只显示特别列出的方面)
*   将更新后的 JSON 放到/GS admin/v1/cloud/content/facets/default 中
*   在预览店面中预览更改
*   一旦它看起来不错，运行部分索引，以便更改被提升到活动的店面

# 为方面排序配置 JSON

如果您以前没有这样配置，当您得到/GS admin/v1/cloud/content/facets/default 时，您的 JSON 将如下所示:

```
{
  "workflowState": "ACTIVE",
  "ecr:lastModifiedBy": "admin",
  "ecr:lastModified": "2018-11-13T14:14:26.726-05:00",
  "priority": 100,
  "ecr:createDate": "2018-11-13T14:14:26.726-05:00",
  "ecr:type": "content-item",
  "contentItem": {
    "navigation": [],
    "showAll": true,
    "@type": "GuidedNavigation"
  },
  "triggers": [
    {
      "exactLocation": false
    }
  ]
}
```

我们要做的是向 contentItem 部分添加更多的配置。下面是订购 3 个方面的 JSON 示例:

```
{
  "workflowState": "ACTIVE",
  "ecr:lastModifiedBy": "admin",
  "ecr:lastModified": "2018-11-13T14:14:26.726-05:00",
  "priority": 100,
  "ecr:createDate": "2018-11-13T14:14:26.726-05:00",
  "ecr:type": "content-item",
  "contentItem": {
    "navigation": [],
    "showAll": true,
    "@type": "GuidedNavigation",
    "navigation": [
            {
                "[@type](http://twitter.com/type)": "RefinementMenu",
                "dimensionName": "product.category"
            },
            {
                "[@type](http://twitter.com/type)": "RefinementMenu",
                "dimensionName": "product.brand"
            },
            {
                "[@type](http://twitter.com/type)": "RefinementMenu",
                "dimensionName": "product.priceRange"
            }
        ] },
  "triggers": [
    {
      "exactLocation": false
    }
  ]
}
```

在上面的 JSON 中，我们做了 4 件值得注意的事情:我们设置类别，然后是品牌，然后是价格。最后，我们有“showAll”:真实设置。配置该选项后，所有其他方面(适用的)将在价格范围后呈现。

如果“showAll”设置为 false，那么 JSON 响应中将只返回“navigation”数组中专门列出的方面。

# 预览和推广这些更改

一旦修改了 JSON，就执行一个 PUT 到/GS admin/v1/cloud/content/facets/default。

此时，您可以在预览店面中预览这些更改(通过登录 OCC 管理并单击右上角的预览按钮)。

一旦更改看起来不错，您将需要将它们推广到生产中。在之前的一篇文章([https://medium . com/Oracle devs/understanding-indexing-for-search-in-Oracle-cloud-commerce-e 4829109 c03a](/oracledevs/understanding-indexing-for-search-in-oracle-cloud-commerce-e4829109c03a))中，我们讨论了推广内容的方法。一个常见的技巧是对产品进行微小的更改(例如修改长描述以添加空格)并发布。发布完成后，将进行索引，在此期间，内容将被提升到实时店面。

# 修改特定位置的刻面排序

上述步骤详细说明了如何将方面排序修改为默认配置。如果你做了上面的改变，你将会成功地设置你的整个站点的面顺序。

但是，您可以基于每个位置修改面排序。例如，当浏览“男鞋”时，你可能想把尺码放在第一位，然后是品牌。也许对于“童鞋”来说，您已经发现价格是最重要的，并且希望首先呈现价格。

这可以通过修改上面的 JSON 来实现。这有点复杂，它涉及到以上面的 JSON 格式创建额外的“规则”。

首先，在为方面排序创建额外的规则之前，让我们讨论一下规则是如何工作的。

**理解规则**

上面的内容位于/GS admin/v1/cloud/content/facets/default，是一个名为“facets”的内容集合文件夹中的单个规则(标识为“default”)。

如果我们在/GS admin/v1/cloud/content/facets 上进行 GET，我们可以看到我们的一个规则“default”位于那里。这条规则是为您创建的现成规则。

我们可以通过 POST/GS admin/v1/cloud/content/facets/<insertrulename>定义更多的规则来控制刻面排序。规则名称应该是字母数字值，没有名称或特殊字符。(对于/gsadmin 端点，我们通常使用 POST 来创建内容，使用 PUT 来更新内容)。</insertrulename>

**了解哪个规则被激活**

在查询时，导航状态(包括细化、搜索词、日期/时间和其他信息)由规则引擎处理。在给定位置可以激活多个规则。例如，上面的“默认”规则将在任何地方激活，因为它没有定义触发器。

如果多个规则适用于给定的导航状态，那么它归结为哪个具有较低的**数值作为优先级。(上述默认规则的值为 100)。**

**理解规则优先级**

每个规则都有一个优先级数值。较低值优先于较高值。

因此，您希望您的通用规则(例如“default”)有一个较高的数值，这样您就可以在它下面放置更多的规则。

**理解触发**

对于何时可以激活规则，有几种类型的触发器。两种主要类型是关键字触发器和选择细化。现在，我们将只讨论精化触发器，因为这对管理方面最有意义。(为每个关键字创建一个方面顺序将是非常难以管理的)。

细分的触发器基于称为“维值 id”的数字 id。这些当前作为 N=参数出现在 URL 中。例如，在 Brand=Acme 上导航，我们可能会在 URL 中看到一个维度值 ID，如 N=439439。如果我们在 Price = $ 10-$ 20 上导航，并且 ID 为 23943，URL 可能看起来像&N=439439+23943。

我们将使用这些维度值 id 来定义触发器。

下面是一个定义了细化触发器的规则:

```
{
  "workflowState": "ACTIVE",
  "ecr:lastModifiedBy": "admin",
  "ecr:lastModified": "2018-11-13T14:14:26.726-05:00",
  "priority": 50,
  "ecr:createDate": "2018-11-13T14:14:26.726-05:00",
  "ecr:type": "content-item",
  "contentItem": {
    "navigation": [],
    "showAll": false,
    "[@type](http://twitter.com/type)": "GuidedNavigation",
    "navigation": [
            {
                "[@type](http://twitter.com/type)": "RefinementMenu",
                "dimensionName": "product.brand"
            },
            {
                "[@type](http://twitter.com/type)": "RefinementMenu",
                "dimensionName": "product.priceRange"
            },
            {
                "[@type](http://twitter.com/type)": "RefinementMenu",
                "dimensionName": "product.category"
            }
        ]
  },
  "triggers": [
    {
      "exactLocation": false,
      **"dvalIDs" : ["591400359"]**
    }
  ]
}
```

有一些有趣的配置需要注意:

1.  我们已经指定了维度值 ID 591400359，它在我的应用程序中是针对视频游戏的。
2.  我们设置了“showAll”= false。这意味着将显示的唯一改进是 3 个列表(品牌、价格、类别)
3.  我们将优先级设置为 50，低于默认规则的值 100。如果我们将优先级设置为 101 或更高，那么即使我们在类别=视频游戏上导航，也会触发默认规则
4.  我们将“exactLocation”设置为 false。这意味着，当我们在视频游戏类别上导航时，如果我们在视频游戏下的集合上导航，以及如果我们在第一次在视频游戏上导航后在其他细分上导航，如品牌或价格，该规则将被激活。

**了解触发器中的确切位置**

“exactLocation”选项可能很难理解，所以让我们通过例子来讨论它。假设一个购物者在类别=视频游戏和品牌=索尼上导航。如果您在 Category=Video Games 上定义了一个规则触发器，并且“exact location”= true，那么该规则不会为该购物者激活。购物者不在为其定义规则的确切位置。(他们在索尼+视频游戏，不完全是视频游戏)。

当“exact location”= false 时，该规则将被激活。

还有一个考虑。想象一下，购物者在类别=视频游戏上导航，然后在称为视频游戏控制台的子集合上进一步导航。当“exact location”= false 时，规则将被激活(因为我们不是在“视频游戏”中，而是在子集合中)。

“exactLocation”选项可以以一些有趣的方式使用。您可以在层次结构中的较高位置建立一个规则(如 Category 方面),并让它影响层次结构中较低的节点。或者您可以定义仅在特定位置激活的规则。

当与规则优先级结合使用时，exactLocation 可以非常强大地创建一系列通用规则，这些规则会被更具体的规则覆盖。您的一般规则将具有高数值优先级值，exactLocation=false。

**更容易检索维度值 id** 的技巧(注意:这些技巧适用于 18.5 及更高版本)

确定给定方面值的维度值 ID(比如 Category=Video Games)可能有点棘手，但是有一些技巧可以确定这一点。

首先，有一个维度值服务可以列出方面值及其 id。例如，要列出 Category 方面中的所有值，可以执行 GET/GS admin/v1/cloud/dim vals/product . Category/children？最大深度=-1

该服务还可以将 ID 转换成文本值。例如，如果您在 URL 中看到 3643890729，您可以执行 GET/GS admin/v1/cloud/dim vals/3643890729，它会告诉您方面的名称和方面值。

其次，在/ccstore/v1/search 端点返回的 JSON 中，有一个名为 searchEventSummary 的 JSON 块。中有一个“facetFilters”列表。例如，在我的应用程序中，在 Brand = Acme 上导航会带回以下内容:

```
...
"searchEventSummary": {
    "facetFilters": [
      {
        "id": "591400359",
        "dimensionName": "product.brand",
        "spec": "Acme"
      }
    ],
...
```

# 禁止面被返回

结合上面讨论的特性，我们现在可以利用方面排序来抑制方面被返回。

例如，我们可以配置一个默认规则，只返回一小部分方面(这样就不会让购物者不知所措)。例如，如果客户从顶部的大菜单中点击电子产品，您可能会在电子产品的所有子类别中看到 40 个不同的方面(屏幕大小、连接器类型、百万像素、电池大小、RAM 大小等)。(无论如何，对于这个应用程序来说)最好从一组核心方面开始，比如类别、品牌、价格、评级等，而不是一个 40 个的庞大列表。

首先，我们会给默认规则一个高优先级数值。接下来，我们将具体列出我们关心的方面，并设置“showAll”= false，并且不配置触发器。

然后，对于某些类别，我们定义规则，要么列出更多方面，要么将“showAll”= true。继续本节的电子产品示例，我们可能会为 Smartphones 创建一个带有维度值 ID 的规则，exactLocation=false，showAll=true。现在只显示与智能手机相关的方面。如果我们想把品牌推到顶端，我们可以把这个方面添加到“导航”列表中。

# 结论

本文展示了大量用于控制站点的配置选项和技巧，并帮助您理解规则、维度值 id、触发器等概念。