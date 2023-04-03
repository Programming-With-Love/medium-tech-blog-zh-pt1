# Windows Server 的 Azure 混合优势—概述

> 原文：<https://medium.com/version-1/azure-hybrid-benefit-for-windows-server-an-overview-1ca4b6a33df3?source=collection_archive---------0----------------------->

![](img/f33a0e0913922c724dabf0897d0c53ca.png)

Photo by [An Tran](https://unsplash.com/@vinhan) on [Unsplash](https://unsplash.com/)

在我们与企业客户的日常交往中，我们仍然遇到少数企业不完全理解或没有充分利用 Azure 混合优势，尽管他们的许可方法和软件资产管理方法已经成熟。

在这篇文章和附带的视频(如下)，我和微软 SAM 销售专员第 1 版的威廉·尼尔森(T1)将概述什么是 Azure Hybrid Benefit (AHB)，分享一些节省成本的技巧，更重要的是，由于 AHB 是一个基于信任的平台，看看关于治理的一些考虑。

## **什么是 Azure 混合优势？**

如果您已经购买了 Windows Server Standard 或 Windows Server Datacentre 的内部许可证，并且这些许可证附带了软件保障，微软将允许您在 Azure 中利用您在这些许可证上的投资。

这样做，你可以消除 Azure 中与 Windows Server 相关的软件租赁费用；其他成本仍然适用，例如存储和计算成本。但是，总体而言，我们估计每台 Azure 虚拟机可以节省 20–40%的成本(软件保障成本仍然是一个因素，需要在未来仔细研究。)

## **标准 v 数据中心版**

从几个方面来看 Azure 混合优势是很重要的。您目前获得了哪些 Windows Server 版本的软件保障许可—大多数组织很可能会有标准版和数据中心版。

当我们看数据中心版时，需要考虑与标准版的几个核心区别；

*   就 Azure 混合优势而言，特别是双重使用权；如果您在本地部署了 Windows Server 数据中心，并且 SA 是最新的，那么您可以根据您拥有的内核数量在 Azure 中同时运行相应的机器，同时以共享租赁的方式在本地部署这些机器。这里的警告是，它必须是一个典型的共享租赁，大多数客户将在 Azure 而不是专用主机上拥有它。如果是专用主机，您可以在迁移期间运行 180 天，然后取消这些权利与本地环境的关联。
*   常见的场景是:Azure 中的共享租户和虚拟机。如果您在内部部署了带有软件保障的 Windows Server 数据中心，那么将该权利应用到您正在运行的 Azure 虚拟机是有意义的，而不会在 Azure 中产生这些虚拟机的许可证成本。

本质上，利用您已经承诺的内部成本的优势，同时将这些优势应用到 Azure 中的机器上，只需支付您的计算成本。这代表了成本节约方面的立竿见影。

如果我们现在考虑 Windows Server 标准版，这里的主要区别是 Windows Server 标准版没有给你双重使用权；唯一的例外是在您进行迁移时——在迁移期间，Microsoft 提供了 180 天的并发使用权。

您必须了解标准版和数据中心版之间的区别。请记住，这是一个基于信任的模型，如果您无意中继续使用 Windows Server Standard Edition 许可 Windows Server 的内部部署实例，然后带来这些 Standard Edition 权利并使用它们来获得 Azure Hybrid 优势，这个等式的一端将会不符合要求。您的 Azure 混合优势将被错误分配，或者您的内部部署环境(应使用 Windows Server Standard Edition 获得许可)不再获得准确许可。仔细考虑如果在审计过程中发现这种不符合项，将会采取什么行动。

Windows Server Standard 和数据中心之间有一个经常被忽略的重要相似之处，那就是您获得的核心授权。虽然其中一个在本地环境中比另一个贵得多，但是从 Windows Server 标准版获得的 Azure 混合优势的核心数量与 Windows Server 数据中心版完全相同。

## **治理**

由于 Azure Hybrid Benefit 是一个“基于信任”的模型，所以最重要的是你要纳入全面的治理实践，以确保你不违反微软的产品条款。请务必确保您的 Azure Hybrid 权益与您在当前协议中的权益相匹配。建议彻底了解以下内容:

*   应用了什么？
*   这在哪里被应用？
*   这是谁申请的，为什么？
*   这是否以最佳方式应用于使用？

这就是有效的软件资产管理(SAM)实践带来真正好处的地方，确保您不会过度使用或相反地使用不足，并保持合规。

如果您无意中违反了规则，微软可能会让您参与审计，并承担支付追溯成本的风险。还有一个风险是，在如何启用 AHB 方面受到更大的控制，这可能会显著提高您的 Azure 月度常规支出，并可能支付追溯成本来覆盖不合规期间。

## **节约成本小贴士**

1.将您的优势优先分配给性能最高的系列虚拟机

与购买在本地使用的 Windows Server 许可证的固定成本不同，在 Azure 中租赁 Windows Server 许可证的相对成本会增加，这取决于 Azure VM 的性能级别-性能越高，租赁 Windows Server OS 的成本越高。因此，作为一个单独的考虑，建议首先将 Windows Server core 许可证分配给性能最高的 Azure 虚拟机。

2.将您的优势优先分配给内核数量最多的虚拟机。

Azure Windows 虚拟机必须分配至少 8 个核心许可证，以符合 AHB 条款。因此，4 核 Azure Windows 虚拟机需要为 AHB 分配与 8 核 Azure Windows 虚拟机相同数量的 Windows Server 核心许可证。这将增加总拥有成本，降低投资回报。

3.续订软件保障—仔细了解续订 Windows Server 数据中心服务协议 v 续订 Windows Server 标准服务协议的成本效益。在某些情况下，例如您的内部环境收缩(这需要仔细考虑和未来的技术路线图规划)，可能有理由质疑为 Windows Server 数据中心提供软件保障是否有任何优势。

4.自动关机—如果您的计算机在部分时间运行，例如上午 8 点到晚上 8 点，那么了解在这类计算机没有全时运行时是否值得对其应用 AHB 是很重要的。

5.企业协议的开发测试折扣—如果您在生产订阅中运行非生产机器，能否将它们转换为“开发测试”订阅，并使用 Visual Studio 或开发人员许可证授权和折扣，在“开发测试”环境中消除 Windows Server 许可证的成本。考虑你的机器在 Azure 中是否处于正确的位置，是否被认为是正确的类型:生产或非生产。

在 Azure Hybrid Benefit 中有很多需要理解的东西，比如确保你已经以正确和最佳的方式应用了该优势，并且你已经有了良好的治理来确保你正在管理不合规的风险。

## **更多信息**

作为微软许可证专家，我们在 Azure Hybrid Benefit 的应用和管理方面拥有深厚的专业知识，并且很乐意帮助您解决任何微软许可证问题。如有任何问题，请访问我们的[网站](https://www.version1.com/it-service/software-asset-management/)或[联系我们](https://www.version1.com/contact/)。

观看我们下面的短视频来听这段对话。

**关于作者:**
Karl 是[版本 1](https://www.version1.com/it-service/software-asset-management/) 的首席许可顾问，为全球组织提供微软许可专业知识，并确保客户从他们的微软资产中获得最佳价值。