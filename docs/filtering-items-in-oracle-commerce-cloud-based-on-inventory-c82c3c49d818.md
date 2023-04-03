# 基于库存筛选 Oracle Commerce Cloud 中的项目

> 原文：<https://medium.com/oracledevs/filtering-items-in-oracle-commerce-cloud-based-on-inventory-c82c3c49d818?source=collection_archive---------5----------------------->

默认情况下，在 Oracle Commerce Cloud 中，所有具有 SKU 和价格的活动产品都被索引并由搜索返回。

然而，很多时候，一个站点希望只显示有库存的商品(或者没有缺货)。这是一个可以在搜索中配置的静态记录过滤器的例子。这种类型的过滤将在幕后进行，并保持在 URL 之外(这样客户就不能篡改 URL 来显示这些产品)。

每个 SKU 都标有一个名为“sku.availabilityStatus”的字段。该字段的唯一值集(截至 19B)仅为:

*   库存内的
*   缺货

# 总体步骤

在我们的示例中，我们将配置一个过滤器，只显示 sku.availabilityStatus = INSTOCK 的产品。为此，我们将:

*   GET/GS admin/v1/cloud/pages/Default/services/guided search
*   修改@appFilterState 部分中“recordFilters”的 JSON
*   PUT/GS admin/v1/cloud/pages/Default/services/guides search
*   启动发布

对/GS admin/v1/cloud/pages/Default/services/type ahead 重复这些步骤

# 修改 appFilterState

在我们对/GS admin/v1/cloud/pages/Default/services/guided search 执行 GET 之后，我们可以看到一系列记录过滤器已经就绪。这是现成的过滤方式:

```
...
"@appFilterState": {
            "hiddenNavigationFilter": "${catalogDimensionValueId}",
            "@type": "FilterState",
            "recordFilters": [
                "product.active:1",
                "sku.active:1",
                "product.catalogId:${catalogId}"
            ],
            "rollupKey": "sku.listingId"
        },
...
```

可以修改上述过滤器，但我们强烈建议您不要这样做，因为不支持对上述过滤器的更改。

对于我们的库存过滤器，我们将更新如下:

```
"@appFilterState": {
            "hiddenNavigationFilter": "${catalogDimensionValueId}",
            "@type": "FilterState",
            "recordFilters": [
                "product.active:1",
                "sku.active:1",
                "product.catalogId:${catalogId}",
                **"sku.availabilityStatus:INSTOCK"** ],
            "rollupKey": "sku.listingId"
        },
```

# 发布更改

对于修改后的 JSON，使用 PUT 将其放回/GS admin/v1/cloud/pages/Default/services/guided search。更新/typeahead 也是有意义的，这样你的完整搜索结果和 typeahead 结果就不会不匹配。

内容放入后，我们首先要验证它们是否正常工作。登录 OCC 管理，并查看您的预览店面。当您执行关键字搜索和在搜索驱动的页面上导航时，过滤应该已经就绪。

当您对更改的效果感到满意时，最容易做的事情就是对产品做一个小的更改(比如在一个长描述中添加一个空格)并调用发布。在发布结束时，内容更改(例如对搜索配置(如/pages)的更改)将被提升到实时店面。

# 一些注释和警告

*   为什么这个滤镜不是开箱就开的？并非所有客户都使用 Oracle 商务云进行库存管理
*   如果您使用外部库存调用，则不能使用它进行过滤(因为外部值不会被搜索索引)
*   收藏页面不是纯粹由搜索驱动的，这种过滤不会影响它们。

如果您想添加其他静态过滤器，上面的模式会很有帮助。不过有几个关键点:

*   sku.availabilityStatus 字段已经存在。如果您对/GS admin/v1/cloud/attributes . merge . JSON 执行 GET，您会看到这个字段也为“isRecordFilterable = true”启用。
*   如果您定义了自定义属性并希望将它们用于过滤器，您还需要设置“isRecordFilterable”= true

# 过滤器和 URL

只要您有一个静态过滤器，如 sku.availabilityStatus，最好按照本指南中的描述进行配置。这个过滤器不会出现在 URL 中，因此购物者无法随意更改。

可以向 URL 添加相同类型的过滤器。例如，可以通过在 URL 后面追加& Nr = AND(SKU . availability status:in stock)来添加上述过滤器。缺点是它将是可见的，可以被篡改。