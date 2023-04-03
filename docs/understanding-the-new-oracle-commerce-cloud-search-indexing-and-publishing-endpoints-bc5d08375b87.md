# 了解新的 Oracle Commerce 云搜索索引和发布端点

> 原文：<https://medium.com/oracledevs/understanding-the-new-oracle-commerce-cloud-search-indexing-and-publishing-endpoints-bc5d08375b87?source=collection_archive---------0----------------------->

作为 19C 版本(也称为 19.4，从 2019 年 8 月左右开始提供)的一部分，发布了一系列新的 REST 端点以供搜索。

这些端点有助于某些索引和发布任务。但是，现有端点和这些新端点之间存在功能重叠。

本文旨在提供对这些新端点的理解，并将它们与现有端点进行比较。

# 什么是新的索引端点？

从 19.4 开始，/gsadmin 下有一个新的任务端点

首先，对于索引，您可以调用 POST/GS admin/v1/cloud/tasks { " type ":" baseline " }或者您可以 POST { "type" : "partial" }

这将纯粹在搜索端执行索引操作。我们稍后会详细讨论这个问题。

(要了解基线更新与部分更新，请阅读“[了解 Oracle 商务云搜索中的索引](/oracledevs/understanding-indexing-for-search-in-oracle-cloud-commerce-e4829109c03a)”)

提交索引请求后，在响应 JSON 中，您将拥有一个任务标识符。例如，我在本地启动了一个基线，我的响应看起来像这样:

```
{"o:rspDetails": "Accepted operation: baselineUpdate",
"callback": "http://server/gsadmin/v1/cloud/tasks/BaselineUpdateTask-k045aol0-329",
"title": "Operation request",
"type": "https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3",
"status": "202"}
```

那么创造了什么？它创建一个异步任务来执行基线更新。在这种情况下，我的任务称为“baseline updatetask-k 045 AOL 0–329”

这样，我们现在可以执行 GET/GS admin/v1/cloud/tasks/baseline updatetask-k 045 AOL 0–329 来获取它的状态。

对该任务 ID 执行 GET 会返回以下结果:

```
{"o:rspDetails": "Task '/apps/cloud/tasks/BaselineUpdateTask-k045aol0-329' has finished processing",
"title": "Operation request",
"type": "https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1",
"status": "200"}
```

有几个可能的状态代码需要考虑:

*   200:成功完成
*   202:还在走
*   500:失败
*   404:您试图获取一个错误 ID 的状态

# 新的发布/内容推广端点是什么？

许多人遇到的一个问题是，对搜索配置的更改(使用 OCC 管理中的搜索管理 UI 或使用 REST)不会出现在 OCC 管理中的发布更改列表中。

相反，有一个独立的机制(不管是好是坏)将搜索更改从你的预览带到真实的店面。本文对此进行了大量讨论:[https://medium . com/Oracle devs/understanding-indexing-for-search-in-Oracle-cloud-commerce-e 4829109 c03a](/oracledevs/understanding-indexing-for-search-in-oracle-cloud-commerce-e4829109c03a)

在 19C / 19.4 之前，为了将您的搜索配置从预览版更改为实时店面，您需要启动索引。您可以直接使用/ccadmin/v1/search/index，或者对产品做一些小的修改，然后使用 UI 发布。

现在，有了一个新的发布端点，它只是用来发布搜索配置更改。

完成搜索配置更改后，您可以调用 POST/GS admin/v1/cloud/tasks { " type ":" publish " }。很像“类型”:“基线”或“部分”，这启动了一个异步任务。然后，您可以让/tasks 端点检查它的状态

# 这些端点与现有的/ccadmin/v1/search/index 端点相比如何？

这一组新端点是对现有端点的补充。让我们讨论一下它们是如何工作的:

**/cc admin/v1/search/index:{ " op ":" baseline-full-export " }**:这将重新导出整个目录和配置。执行完全重建索引。发布搜索配置和内容更改

**/cc admin/v1/search/index:{ " op ":" baseline " }:**仅导出已更改的目录记录。执行完全重建索引。发布搜索配置和内容更改。

**/GS admin/v1/cloud/tasks:{ " type ":" baseline " }**:对已经在搜索中的数据执行完全重新索引。

**/cc admin/v1/search/index:{ " op ":" partial " }:**仅导出已更改的目录记录。执行部分索引。发布搜索配置和内容更改

**/GS admin/v1/cloud/tasks:{ " type ":" partial " }:**对已经在搜索中的数据执行部分索引

**/GS admin/v1/cloud/tasks:{ " type ":" publish " }**:仅发布搜索配置和内容更改

(以上大致按照从最慢到最快的顺序排列。基线-完整-导出将花费最长的时间，而“类型”:“发布”将非常快地执行)

需要指出的几点是:使用/ccadmin/v1/search/index 将目录中的内容导出到搜索中。但是，使用/gsadmin/v1/cloud/tasks，索引只使用以前导出的内容。(也就是说，所有的处理都发生在搜索端)

使用/ccadmin/v1/search/index(无论是“基线”、“基线-完整-导出”还是“部分”)将在最后开始发布搜索内容。现在，我们可以使用/GS admin/v1/cloud/tasks { " op ":" publish " }来代替。

这就把我们带到了下一部分。

# 我何时会使用这些新的端点？

好吧，所以你可能对这组新的端点以及你什么时候会真正使用它感到困惑。这可以理解。

首先，用{"type": "publish"}调用/tasks 端点可能是这些端点中最有用的。这允许您更改搜索内容并发布它，而不必进行任何类型的索引。这意味着它将非常非常迅速地生效。

我说的搜索内容变化是什么意思？

*   同义词库条目
*   动态固化
*   搜索界面的更改
*   助推/掩埋
*   刻面排序

您现在可以调用/tasks 端点来发布这些更改，这样它们就可以很快在您的实际店面上生效，并且不需要索引。这通常只需要 1 或 2 秒钟。

对于使用带有“类型”:“基线”或“部分”的/tasks，这仅在少数情况下有用。

1.  您正在使用/gsdata 终结点来索引非目录内容。本文对此进行了深入讨论:[https://medium . com/Oracle devs/performing-geolocation-searches-and-filtering-in-Oracle-commerce-cloud-search-646646 e 11 FD 8](/oracledevs/performing-geolocation-searches-and-filtering-in-oracle-commerce-cloud-search-646646e11fd8)
2.  您正在对搜索配置进行架构级别的更改。这可能包括使用/attributes 端点的更改(可能创建新的方面、更改搜索设置等)

这样做的好处是，您可以执行索引操作，而不必重新导出部分或全部目录。所以在上面列出的文章中，我们对一些地理位置数据进行了索引。我们现在可以只调用/tasks，而不必调用/ccadmin/v1/search/index，这样完成起来会快得多。

# 请帮助我理解当我调用/ccadmin/v1/search/index 时我看到了什么，以及它与这些新端点相比如何？

好的，当您对/ccadmin/v1/search/index 执行 GET 时，您将从 Oracle Commerce Cloud 管理端查看索引的状态。

这显示了许多与导出目录和配置以搜索索引相关的任务。这里发生了许多有趣的事情，值得一提。(请注意，名称和任务将来可能会更改):

*   **SchemaExporter** :从 OCC 目录中获取配置，并将其导出到/gsadmin/v1/cloud/attributes 和/GS admin/v1/cloud/search index config
*   **ProductCatalogOutputConfig**:这将导出目录(要么是整个目录，要么是已经更改的目录)以便在索引开始之前进行搜索。
*   这个任务代表开始搜索索引。这实际上为 type=baseline 或 type=partial 调用了新的/gsadmin/v1/cloud/tasks。
*   **promotecontendexable**:该任务表示发布搜索配置和内容变更(如动态监管、搜索界面等)。这实际上使用“type”:“publish”调用了新的/gsadmin/v1/cloud/tasks 端点

此前，在内部引导搜索产品中，有一个名为“promote_content.sh”的脚本。在云中，这个过程现在被重命名为发布。所以说清楚:
promote _ content . sh = = promotecontendexable = =/tasks { " type ":" publish " }

# 结论

希望您发现本文有助于理解新的端点以及何时使用它们。

以下是我们之前文章的链接:

[解释 Oracle 商务云搜索中的配置](/oracledevs/explaining-configuration-in-oracle-commerce-cloud-search-fe0f34fa705b)

[执行地理位置搜索](/oracledevs/performing-geolocation-searches-and-filtering-in-oracle-commerce-cloud-search-646646e11fd8)

[根据存货过滤搜索结果](/oracledevs/filtering-items-in-oracle-commerce-cloud-based-on-inventory-c82c3c49d818)

[限制搜索的 JSON 响应中返回的字段](/oracledevs/limiting-fields-returned-by-search-in-oracle-commerce-cloud-bcc6e86ec614)

[理解和控制搜索结果的顺序](/oracledevs/understanding-and-controlling-the-order-of-search-results-in-oracle-commerce-cloud-813359864c0d)

[控制搜索中的方面和方面值排序](/oracledevs/controlling-facet-value-ordering-in-oracle-commerce-cloud-438498bb79d4)

[了解 Oracle 商务云搜索中的索引](/oracledevs/understanding-indexing-for-search-in-oracle-cloud-commerce-e4829109c03a)

[产品列表页面的动态管理](/oracledevs/dynamic-curation-of-product-listings-with-oracle-commerce-cloud-3a6de6f01450)