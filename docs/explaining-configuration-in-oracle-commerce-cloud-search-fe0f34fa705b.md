# 解释 Oracle Commerce 云搜索中的配置

> 原文：<https://medium.com/oracledevs/explaining-configuration-in-oracle-commerce-cloud-search-fe0f34fa705b?source=collection_archive---------0----------------------->

借助 Oracle Commerce Cloud search，我们通过/gsadmin API 向用户公开了大量配置。然而，在所有这些配置中，有一些是 1)实施者不应该触及的，或者 2)在修改时应该非常小心。

本文将讨论您可以通过/gsadmin/v1/cloud 访问的不同配置区域，对它们进行一些解释，并告诉您哪些区域应该避免修改。我们还将讨论修改属性的最佳方法，以及应该避免什么。

# 升级和迁移的快速背景

我们不断地在每个版本中为 GS 添加新的特性。在这些新特性中，有一个新的配置，它可以搁置或更新现有的配置。

例如，当我们添加动态监管和 Boost&Bury 时，我们将配置添加到/GS admin/v1/cloud/pages/Default/services/guided search。18D 年也是如此，当时增加了对多目录和多站点的高级支持。

随着搜索的升级，将执行脚本将新配置合并到现有配置中。

需要指出几件事:

1.  随着我们添加功能，新的配置会出现
2.  您需要采取措施不删除该配置。

例如，您可以通过执行 GET /gsadmin/v1/cloud.zip 来备份您的配置，并使用 POST 来恢复它。但是，如果您创建了 18D 备份，然后根据 19B 恢复它，您将丢失一些迁移的配置。

# 了解属性和所有权

从 18D 开始，如果执行 GET/GS admin/v1/cloud/attributes，会看到三个文件夹:/system、/ATG 和/keywords。我们称这些属性中的每一个为“所有者”。

我将完全诚实地说，“系统”和“ATG”的名称并不理想，如果我们可以重来，我们肯定会改变它们。但是让我们讨论一下这些是什么:

1.  **/ATG** :你可能知道，甲骨文商务云起源于 ATG 商务平台。每次建立索引时，管理服务器都会重新导出所有 ATG 属性，删除之前存在的所有属性。因此，这些系统管理的属性，不应该被触及。
2.  **/系统**:这是一组开箱即用的属性，是索引发生所必需的。然而，尽管名称如此，它们本身并不由系统管理。您可以修改这些属性，创建新属性，更新/ATG 下的属性。这些属性在索引过程中不会被删除，而是保持不变。
3.  **/关键词:**这是 18D 中新增的一组属性，用于支持热门搜索功能。

好吧，让我们在这里给出明确的指导:

*   不要碰/ATG 下的属性。就是不行。它们会在索引过程中被删除并重新创建，因此您的更改不会持久。
*   可以碰/关键词，但是不要。让我们把这些留给将来的增强吧。
*   如果要创建全新的属性，请创建自己的文件夹。(或者，您可以将它们放在/system 下，但我们正在推动人们使用他们自己的文件夹)
*   如果要修改/ATG 下的属性，则这些修改应位于/system 下

所以，不要再碰/ATG 下面的任何东西。

如果希望看到用于索引的最后一组属性，可以执行/gsadmin/v1/attributes . merge . JSON

最后一点说明:对属性的更改是架构级别的更改，需要基准索引才能生效。我之前在“了解 Oracle Commerce 云搜索中的索引”中讨论过索引

# 修改属性

在涉及修改属性时，让我们先简单回顾一下。

在 Oracle Commerce Cloud 搜索中(正如我们上面提到的)，当目录导出到搜索时，系统将在/attributes/ATG 下重新创建属性，因此您不能在该位置修改这些属性。

相反，如果我们想调整这些属性，我们会将这些修改放在/attributes/system 下，并指定“merge action”:“UPDATE”。

例如，如果要获取/attributes/ATG 下的一个属性，并将其用于记录筛选，则/attributes/system 下的 JSON 可能如下所示:

```
"product.x_customAttribute" : {
  "propertyDataType" : "ALPHA",
  "ecr:type" : "property",
  "isRecordFilterable" : true,
  "mergeAction": "UPDATE" },
```

总结如下:

*   不要修改/attributes/ATG 下的任何内容。它要么不起作用，要么不会长期存在
*   如果要修改来自/attributes/ATG 的属性，请将其放在/attributes/system 下，并使用“merge action”:“UPDATE”作为您的更改
*   执行 GET/gsadmin/v1/cloud/attributes . merge . JSON 预览更改。如果出现此错误，请撤消更改。
*   /gsadmin/v1/cloud/attributes . merge . JSON 是一个考虑所有属性所有者并呈现最终合并视图的视图。这是“搜索”在建立索引时要查看的内容。
*   对属性的更改需要基准索引，因为这些是架构级别的更改

# 配置为不打扰

这是一个不要打扰的文件夹列表。REST 无法使用其中的一些工具，可以通过执行 GET /gsadmin/v1/cloud.zip 来访问这些工具。

*   / **属性上下文**:有些属性(如产品、类别、产品、显示名称、定价)是上下文相关的。也就是说，产品上的值可能因地区或价目表组而异。此配置持有不同的上下文。我们不支持添加上下文
*   **/工作区，/编辑，/模板，/工具，/配置**:正如很多人所知，Oracle Commerce Cloud Search 是基于 Oracle Commerce Guided Search(之前的 Endeca)构建的，用于内部部署。这种配置主要用于内部部署，但仍然适用于云。别管它。
*   **/优先规则/ATG 和/优先规则/分析**:优先规则用于控制面何时出现。例如，“在选择类别之前不要显示品牌”。在/ATG 下通常没有优先级规则，但即使如此，这也是由系统管理的。/Analytics 集用于防止一些内部方面出现在网站上。两种情况都不要碰。我们也不推荐使用 precedenceRules，因为它们需要一个基线索引，并且在涉及到动态监管等方面不够灵活。相反，有其他可用的[方面管理 APIs】应该被使用并且不需要索引。](/oracledevs/controlling-facet-value-ordering-in-oracle-commerce-cloud-438498bb79d4)

# 结论

希望这有助于消除围绕配置的混乱，尤其是围绕属性的混乱！

提醒一下，这里有我们写的其他文章的链接:

[执行地理定位搜索](/oracledevs/performing-geolocation-searches-and-filtering-in-oracle-commerce-cloud-search-646646e11fd8)

[根据存货过滤搜索结果](/oracledevs/filtering-items-in-oracle-commerce-cloud-based-on-inventory-c82c3c49d818)

[限制搜索的 JSON 响应中返回的字段](/oracledevs/limiting-fields-returned-by-search-in-oracle-commerce-cloud-bcc6e86ec614)

[了解和控制搜索结果的顺序](/oracledevs/understanding-and-controlling-the-order-of-search-results-in-oracle-commerce-cloud-813359864c0d)

[控制搜索中的方面和方面值排序](/oracledevs/controlling-facet-value-ordering-in-oracle-commerce-cloud-438498bb79d4)

[了解 Oracle 商务云搜索中的索引](/oracledevs/understanding-indexing-for-search-in-oracle-cloud-commerce-e4829109c03a)

[产品列表页面的动态管理](/oracledevs/dynamic-curation-of-product-listings-with-oracle-commerce-cloud-3a6de6f01450)