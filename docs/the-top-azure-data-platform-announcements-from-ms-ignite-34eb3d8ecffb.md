# 来自 MS Ignite 的顶级 Azure 数据平台公告

> 原文：<https://medium.com/version-1/the-top-azure-data-platform-announcements-from-ms-ignite-34eb3d8ecffb?source=collection_archive---------2----------------------->

微软 Ignite 今年举办了一系列在线网络研讨会，并在 3 月 2 日(星期二)至 3 月 5 日(星期四)期间召开了“询问专家”会议。

![](img/fb6482d5eb4db23cb94bef456886864f.png)

Photo by [Johny vino](https://unsplash.com/@johnyvino?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

虽然微软以一个令人印象深刻的新混合现实平台 Microsoft Mesh 拉开了会议的序幕，但第一天还宣布了一些 Azure 数据和分析服务的新特性和功能。

**天蓝色突触通路**

基于数据现代化的主题，微软宣布了一项名为“Azure Synapse Pathway”的新服务，旨在加速将现有数据仓库解决方案迁移到 Azure Synapse 的过程。

在过去，转换成千上万行 SQL 代码是数据仓库迁移中最大的工作量，这些代码是用数据仓库平台支持的任何 SQL 方言编写的。现在，Azure Synapse Pathway 可以连接并扫描您的数据库，生成一份评估报告，概述哪些对象可以迁移到 Azure Synapse Analytics。数据库中的 SQL 对象被自动转换为 T-SQL 并优化运行在 Azure Synapse 上。今天，Azure Synapse Pathway 涵盖了来自 IBM Netezza、SQL Server 和雪花的数据库模式和表对象，并且对 Teradata、AWS Redshift、BigQuery & Hive 的支持正在进行中。SQL 翻译功能也在开发中，同时预览正在进行，并将随着时间的推移而扩展。(还没有提到甲骨文，但我不得不假设它也在路上。)

**用权限查看更多内容**

说到 Oracle，Azure 的新数据治理平台，现在支持 Oracle 数据库的数据编目。这扩展了已经涵盖内部配置单元、SAP、SQL Server 和 Teradata、多云数据源和 Power BI 数据集的范围。without 是微软的新数据目录和数据治理解决方案，使组织能够创建其所有数据的整体视图，无论是内部数据还是云中数据。该工具还提供了根据敏感度对数据进行分类的功能，并提供了易于导航的数据沿袭信息。虽然它仍处于预览阶段，并解决了一些问题，但它比最初的 Azure 数据目录有了很大的改进。仍然存在一些明显的差距——还不支持数据块，例如，增量文件仍在处理中。我也希望看到将来的权限扩展到处理整个组织的数据质量管理。

**将客户洞察扩展到 Synapse**

Dynamics 365 客户洞察是从 Dynamics 365 中的客户数据中提取洞察的强大解决方案。这项服务已经支持客户数据清理和重复数据删除等功能，以及对跨渠道交互和客户流失的实时洞察。在 Ignite 上，微软推出了一些新功能，使这些见解能够与 Azure Synapse Analytics & Power BI 集成。这为您的 CRM 平台提供了可操作的见解，并在 Azure Synapse Analytics 中集成了来自整个企业的数据，所有这些都可以在 Power BI 中使用。

**电力 BI 每用户溢价一般可用**

秘鲁 Power BI Premium 的用户许可在去年年底的预览版中推出，现在已全面上市。微软还提供了这项功能的价格更新——每人每月 20 美元(按今天的价格计算，大约是€16)。与竞争对手每人每月 30 至 70 美元的价格相比，这是有利的。微软的 Power BI Premium 产品可以说比一些竞争对手的功能更丰富——提供完整的 now 代码数据辩论功能，像素完美分页报告，以及与一系列认知服务和机器学习功能的内置集成。

**边缘分析**

Azure Percept 是微软的新平台，用于将智能分析推向组织边缘的设备(相机、传感器、智能仪表和其他物联网设备)。基于特定硬件组件的组合，并使用多种 Azure 服务，Percept 提供了一个安全的平台来创建智能(er)设备，这些设备应用人工智能驱动的决策来捕获信息，而不是依赖于将数据穿梭回中央处理中心。

**Azure Analytics 无处不在**

Azure 机器学习最近也有一些重大升级。与 Azure Synapse Analytics 的更深入集成允许数据科学家从他们的 Azure ML 笔记本中利用 Synapse SQL 和 Spark 引擎的功能和规模。与 Azure Arc 的集成使 Azure ML 模型培训活动能够在本地或其他云上进行管理。Azure Arc 代理可以部署到任何现有的 Kubernetes 集群，允许您使用 Azure ML 来训练模型，而不必将数据移动到 Azure。

**按含义搜索**

微软今天也宣布了 Azure 认知搜索服务的一些新功能。这项服务正在扩展，以提供语义搜索—搜索引擎理解问题的含义，并能够智能地将其与被索引文档的上下文和含义进行匹配。这是基于 Bing 搜索引擎使用的一些自然语言处理模型，应该会提供更准确和相关的搜索结果。

我有兴趣在现实环境中尝试一下…其中一个新功能是 Sharepoint 连接器，它将允许 Sharepoint 365 上的文档库被索引。对于在 Sharepoint 库中拥有大量文档的组织来说，这肯定会提供比当前更好的搜索功能。

**托管 NoSQL 平台**

扩展 Azure 中已经非常优秀的关系数据库功能，微软已经宣布了 Apache Cassandra 的 Azure 托管实例。这为 NoSQL 数据库做了 Azure SQL 托管实例为 SQL Server 做的事情—提供完全托管的虚拟基础架构，为平台提供与本地版本完全等同的功能。

正如您对云解决方案的预期，Azure MI for Cassandra 允许轻松调整集群中的节点数量，以应对不断增长的需求。将 Cassandra 工作负载迁移到云的人会对两个出色的特性感兴趣:

1.  Azure MI for Cassandra nodes 可以配置为现有 Cassandra 数据库的扩展，数据将双向复制。(每个过渡都有一个简洁的特征)
2.  透明复制到 Azure Cosmos DB 的现有 Cassandra API——使从本地到托管实例再到完整 Saas 的过渡非常顺利。

当您考虑到复制到 Cosmos DB 的数据可以进一步与分析数据存储同步并使用 Azure Synapse Link 进行分析时，上面的第二点甚至更有用。

在微软的现代数据平台方法中，出现了一些好的趋势:

> **易于迁移&采用 Azure 技术。**
> 
> **减少构建复杂流程从源系统提取数据的需求。**
> 
> **提供标准化且易于理解的界面，与您的数据进行交互，并从中提取见解。**

如果您想了解更多关于如何利用上述技术实现数据平台现代化的信息，或者想观看这些工具的实际演示，请联系我们。

Rohan Kumar(此处[可用](https://myignite.microsoft.com/sessions/44be5449-eec6-4d63-8732-716147341d56))在一次会议中谈到了这一点，其中包括一些关于 Azure Redis Cache &云应用开发的其他公告。我的角色主要是关于分析的，所以我在这篇文章中主要关注与分析相关的新特性。

**关于作者**

*Paul Finn 是 BI & Analytics 第 1 版的负责人，该版本是我们英国数字数据&云实践的一部分。关注我们的媒体博客，了解更多来自 Paul 的与数据相关的博客。*