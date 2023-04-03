# 面向 Linux 的 Azure 混合优势

> 原文：<https://medium.com/version-1/azure-hybrid-benefit-for-linux-bccf1fb8a87f?source=collection_archive---------6----------------------->

适用于 Windows Server 和 SQL Server 的 Azure 混合优势(AHB)是一项软件保障(s a)优势，当应用于 Azure 中 AHB 涵盖的许可证时，允许您删除与 Windows Server 或 SQL Server 数据库相关的软件租赁费用。

![](img/7e20c87cf2ad3ad6048a81c9ad2fad85.png)

Photo by: Towfiqu Barbhuiya on Unsplash

微软最近向市场推出了一个新的 AHB 产品，重点是 Linux，我们认为，许多使用 Linux 的客户并没有完全理解这个好处，因此错过了潜在的成本节约。

在这篇文章(以及下面的视频)中，我和微软第一版的首席许可顾问卡尔·奥多尔蒂将会更详细地讨论使用 AHB 的注意事项，以及在使用 Linux 时如何利用这一优势。

## **这项福利涵盖哪些操作系统？**

微软对这项优惠所涵盖的操作系统有所限制。在这个特殊的例子中，是 Red Hat 和 SUSE Linux 客户在 Azure 中部署他们的工作负载。

## **主要的好处是什么？**

在 Linux 上使用 AHB 的主要好处之一是降低成本。例如，如果没有 Linux 版 AHB，在 Azure 中部署运行 Red Hat Enterprise Linux 或 SUSE Linux Enterprise Server 的虚拟机的成本默认情况下会按“按需付费”(PAYG)的方式收取。该费用包括计算和操作系统租赁费用，但是通过利用 Linux AHB，您可以免除操作系统租赁费用，允许您携带自己的操作系统。

此外，如果您正在迁移已经获得内部许可的现有工作负载，您可以将现有订阅引入 Azure，而不是采用 PAYG 模式，即您支付订阅费用，同时维护现有的内部订阅。在这种情况下，您可以避免重复支付。

## **配置怎么样？**

有一个配置过程需要在 Azure 端和 Red Hat 或 SUSE 端进行。

*   对于 Red Hat，您需要在 Red Hat 的云访问计划下注册，对于 SUSE，您需要在 SUSE 客户中心下注册您的订阅，无论您是直接从 SUSE 还是从合作伙伴处购买。
*   注册完成后，您可以将任何现有的 PAYG 工作负载转换为使用 AHB Linux，然后使用“自带订阅”(BYOS)，这也可以通过使用 BYOS 并返回 PAYG 的相反方向来完成。这为配置提供了一定程度的灵活性。
*   Linux 的 AHB 还可以与 Azure Marketplace 的 Red Hat Enterprise Linux 和 SUSE Linux Enterprise OSs 的标准映像结合使用。

好消息是，如果您还没有启用这项功能，现在就可以开始使用它，并开始看到成本节约。

## **支持呢？**

鉴于以下问题，需要仔细考虑对 Red Hat 和 SUSE 的支持。根据我们的理解以及我们从 Red Hat 和 SUSE 看到的情况，支持模型的工作方式如下:

*   如果您采用的是 BYOS 模式，并且直接从供应商处购买了订阅，那么他们会提供支持。
*   如果你采用 PAYG 模式，在你联系红帽或 SUSE 之前，Azure 或微软是你的第一线支持。

这是一个重要的区别，特别是对于需要立即支持的关键问题。我们并不是说微软不能回应或解决这些问题，但是，你可能会觉得直接去找产品供应商更有好处。

此外，使用 PAYG 模式，您可能会开始失去一些您多年来建立的 Red Hat 或 SUSE 客户经理和技术支持关系，因为您不会购买太多的订阅。

重要的是要了解谁在为你提供支持，你能多快获得支持，以及你已经建立了哪些关系，这样你就不会失去任何有用的联系。

## **更多信息**

有关此主题的更多信息，请参考下面的链接。

*   [微软关于 PAYG 市场虚拟机 Azure 混合优势如何应用于 Linux 虚拟机的文档。](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/azure-hybrid-benefit-linux)
*   [红帽常见问题解答&微软 Azure 推荐实践。](https://access.redhat.com/articles/2758981#can-i-use-my-existing-red-hat-enterprise-linux-subscription-for-microsoft-azure-2)
*   [SUSE on Azure 混合优势](http://Azure Hybrid Benefit Support | Support | SUSE)。

作为许可证专家，我们熟悉涵盖各种技术平台的各种 1 级供应商许可证注意事项和成本优化机会，并乐意帮助您解决任何许可证问题。如有任何问题，请访问我们的[网站](https://www.version1.com/it-service/software-asset-management/)或[联系我们](https://www.version1.com/contact/)。

观看我们下面的短视频来听这段对话。

![](img/a3a854d62d17f03b87f8fa0d66031139.png)

**关于作者:** Richard Ojo 是版本 1 的 SAM 许可顾问。