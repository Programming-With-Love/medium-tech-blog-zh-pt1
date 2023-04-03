# Azure 开发和测试许可选项

> 原文：<https://medium.com/version-1/azure-development-test-licensing-options-27b01f88e97e?source=collection_archive---------0----------------------->

![](img/790a6fa89e6843691d9a8467ea09f58d.png)

Photo by [Annie Spratt](https://unsplash.com/@anniespratt) on [Unsplash](https://unsplash.com/)

随着组织拥抱 Azure，他们为开发和测试目的开发许可策略是很重要的。由于 Azure 有各种开发和测试许可途径，组织了解可用的许可选项及其相关条款是非常重要的。不重视这些选项和条款可能会导致错过成本节约或无意中违反许可。这篇博客将研究 Azure 开发和测试的可用许可模型。

**Azure 免费账户**

Microsoft 为需要执行以下操作的个人或组织提供有限的免费服务:

*   评估或演示 Azure
*   短期内以较小的规模实施 Azure

微软将这些免费服务的大部分限制在 12 个月内，有些服务是持续免费提供的；所有这些免费服务及其相关容量的总结可以在[这里](https://azure.microsoft.com/en-us/free/)找到。

这一免费服务包括 200 美元的积分，可以应用于任何 Azure 服务，必须在注册后 30 天内使用。由于这是一个免费的提供，有一些约束限制了它的潜在用途。

**Visual Studio/MSDN Azure Credits**

在撰写本博客时，每个授权用户每月将获得以下 Azure 点数:

*   Visual Studio 企业版 150 美元
*   MSDN 平台 100 美元
*   Visual Studio 专业版 50 美元

这些学分最适合个人使用场景，如自定进度学习和在非生产环境中探索 Azure 功能。创建自己的 Azure 租户(使用这些配额)的用户在非生产环境中与同事协作时可能会面临挑战。组织还需要意识到，无论持有多少 Visual Studio / MSDN 用户许可证，用户之间都不能共享或汇集学分。信用可以应用于各种各样的 Azure 服务，还可以为应用服务和逻辑应用提供折扣价格。

如果将这些面向 Visual Studio 订阅者的每月 Azure 点数用于生产目的，将违反微软许可条款。事实上，微软声明他们保留暂停任何连续运行超过 120 小时的实例(虚拟机或云服务)的权利，或者如果他们确定该实例用于生产。

此外，Microsoft 不保证使用这些配额时的容量可用性，如果将配额用于重要工作负载，可能会产生运营风险。了解有关 Visual Studio / MSDN Azure 点数的更多信息，包括好处和对限制的进一步见解[单击此处](https://azure.microsoft.com/en-us/pricing/member-offers/credit-for-visual-studio-subscribers/)。

**Azure 开发/测试订阅**

这种开发/测试许可模型在员工需要访问同一套非生产 Azure 服务时实现了高度的灵活性。Azure 开发/测试订阅可以在组织通过现收现付(PAYG)或微软称之为“企业运动”的方式许可 Azure 的地方获得

当使用开发/测试订阅时，管理员可以创建独特的 Azure 订阅，其中包含各种 Azure 服务的默认折扣级别。在写这篇博客的时候，微软提供了以下折扣:

*   Windows 虚拟机—按 CentOS/Ubuntu Linux 虚拟机费率计费。
*   BizTalk 企业虚拟机和 BizTalk 标准虚拟机—按 CentOS/Ubuntu Linux 虚拟机费率计费。
*   SQL 数据库—节省高达 55%。
*   SQL Server 虚拟机(企业版、标准版和 Web 版)—按 CentOS/Ubuntu Linux 虚拟机费率计费。
*   逻辑应用企业连接器— 50%折扣。
*   应用服务(基本、标准、高级 v2、高级 v3) —折扣因实例大小/类型而异。
*   云服务实例—折扣因实例规模/类型而异。
*   HDInsight 实例—折扣因实例大小/类型而异。

一个重要的许可证合规性考虑因素是，每个人都必须拥有适用的 Visual Studio 许可证，用户可以在该许可证中管理、开发或测试开发/测试订阅中的服务。但是，当用户被归类为 UAT(用户验收测试)时，不需要 Visual Studio 许可证。拥有 Visual Studio 许可可以根据所拥有的 Visual Studio 许可证类型，允许您 BYOL 各种 Microsoft 解决方案，从而进一步节省成本。

与使用 Visual Studio Azure 信用相比，使用 Azure 开发/测试订阅提供了从非生产到生产的绝佳途径。生产途径包括在同一租赁内将资源从非生产订阅转移到生产订阅，或者简单地恢复到基于生产的收费级别。目前，CSP 不提供开发/测试订阅，这可能会增加许可的复杂性，因为需要混合 PAYG 和 CSP，或者选择使用 Enterprise motion 与 Microsoft 建立更直接的计费关系。

从微软文档页面[这里](https://docs.microsoft.com/en-us/azure/devtest/offer/overview-what-is-devtest-offer-visual-studio)了解更多关于 Azure 开发/测试产品的信息，同时可以在[这里](https://azure.microsoft.com/en-us/pricing/dev-test/#overview)找到更多信息、开发/测试订阅类型和好处。

**总结**

总之，充分理解 Azure 中非生产性需求的规模，并制定一个满足这些需求的许可策略是很重要的，这种策略的方式是:

1.  有效管理消费成本
2.  减少违反微软产品许可条款的行为
3.  实现更顺畅的操作

根据经验，非生产许可模式经常被误解，导致财务浪费和/或许可证违规。因此，组织在设计阶段就让许可顾问参与进来是非常重要的，这也有助于持续的治理。

**更多信息**

作为许可专家，我们熟悉微软技术非生产用途的各种许可注意事项。我们很乐意帮助您解决任何许可证问题。如有任何问题，请访问我们的[网站](https://www.version1.com/it-service/software-asset-management/)或[联系我们](https://www.version1.com/contact/)。

![](img/85bc7a6618c6512ab1bce77389b46384.png)

Photo by [Windows](https://unsplash.com/@windows?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/microsoft-licensing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

**关于作者:**

Karl 是 Version 1 的首席许可顾问，为全球组织提供 Microsoft 许可证专业知识，并确保客户从他们的 Microsoft 资产中获得最大价值。