# 限制 Oracle Commerce Cloud 中搜索返回的字段

> 原文：<https://medium.com/oracledevs/limiting-fields-returned-by-search-in-oracle-commerce-cloud-bcc6e86ec614?source=collection_archive---------6----------------------->

在 Oracle Commerce Cloud 中，从/ccstoreui/v1/search 或/ccstoreui/v1/assembler 检索的结果通常会返回与该记录相关联的所有字段。

但是，如果您的应用程序有许多自定义产品类型属性，这可能会返回太多的数据，尤其是因为您通常只需要足够的数据来呈现页面。

也可能有您认为是私有数据的字段，如利润或销售量。

通过编辑搜索服务，您可以指定希望返回的字段的白名单(所有其他字段将在响应中忽略)。这也有利于减少有效负载(尽管使用压缩和缓存，典型的/search 或/assembler 响应低于 150kb)。

这对于使产品类型提前响应更小可能非常有用。

# 访问默认搜索配置

处理搜索结果配置的现成搜索服务位于/GS admin/v1/cloud/pages/Default/services/guided search。OOTB typeahead 的配置位于/GS admin/v1/cloud/pages/Default/services/type ahead。

我们将遵循的模式是:

*   GET/GS admin/v1/cloud/pages/Default/services/guided search
*   修改 JSON
*   PUT/GS admin/v1/cloud/pages/Default/services/guides search

# 指定字段列表

从/guidedsearch 端点返回的 JSON 将包含一个名为“resultsList”的部分。默认情况下，它应该是这样的:

```
"resultsList": {
            "@type": "ResultsList",
            "rankingRules": {
                "merchRulePaths": ["/content/rankingRules"],
                "systemRulePaths": ["/content/system/rankingRules"],
                "systemRuleLimit": 10
            }
 },
```

我们要做的是将新的配置添加到“resultsList”部分。

请注意，在 Oracle Commerce Cloud 站点中，我们在 SKU 级别对记录进行索引，然后搜索“汇总”到产品记录中。例如，您将在@appFilterState 部分看到以下配置:

```
"rollupKey": "sku.listingId"
```

当谈到限制返回的字段时，我们可以在两个地方进行配置:“attributes”和“childRecordAttributes”。最有可能的是，您将使用“childRecordAttributes”来限制字段。只有少数产品级别的字段由“属性”控制，如最低/最高价格。

在下面的 JSON 中，我展示了如何将响应配置为只包含名称和描述。

```
"resultsList": {
            "@type": "ResultsList",
            "rankingRules": {
                "merchRulePaths": ["/content/rankingRules"],
                "systemRulePaths": ["/content/system/rankingRules"],
                "systemRuleLimit": 10
            },
            "childRecordAttributes" : ["product.description", "product.displayName"] },
```

实际上，只返回这两个字段是不够的，但是这个简短的列表有利于演示。

注意:这是一个白名单，目前，没有办法指定一个黑名单。这意味着，如果您的应用程序返回 70 个字段，而您想省略 2 个，那么它需要您指定一个 68 个字段的列表。

# 将更新的配置

修改完 JSON 后，将从/guidedsearch 或/typeahead 检索到的整个 JSON 块放回/GS admin/v1/pages/Default/services/guided search(或/typeahead)。

此时，您的更改将在预览店面中可见。登录到 OCC 管理和更新的字段列表是什么将被返回。您也可以执行 GET

这可以让你看到减少字段是否破坏了你的店面。

# 调用发布来将更改推广到您的店面

一旦您在预览店面中验证了 JSON 有效负载，我们需要推广这些更改，以便它们影响您的实际店面。

POST /ccadmin/v1/search/index？op =部分

# 结论

我希望这篇短文对你有用。请务必在 Medium 上“关注”我，因为我计划写更多短小精悍的文章。