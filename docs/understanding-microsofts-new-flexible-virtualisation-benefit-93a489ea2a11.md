# 了解微软新的灵活虚拟化优势

> 原文：<https://medium.com/version-1/understanding-microsofts-new-flexible-virtualisation-benefit-93a489ea2a11?source=collection_archive---------1----------------------->

![](img/1342a2bbf58e6a36e7a19e21312f6bd0.png)

Photo by [Vishnu Mohanan](https://unsplash.com/@vishnumaiea?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

10 月的[微软月度更新](/version-1/monthly-microsoft-license-update-october-2022-2a4d0d294b15)是一个繁忙的月份，发布了 Windows Server 许可变更([https://www . Microsoft . com/licensing/terms/product/for all software](https://www.microsoft.com/licensing/terms/product/ForallSoftware))和新的灵活虚拟化优势。我的同事、微软首席许可顾问[卡尔·奥多尔蒂](https://www.linkedin.com/in/odohertykarl/)和我将在下面的帖子和视频聊天中重点讨论灵活虚拟化的优势:

这一优势将对客户将自己的许可证交给某些第三方外包商的方式产生重大影响。因此，在我们深入细节之前，让我们先了解一下谁是“授权外包商”，或者更简单地说，谁是**而不是**授权外包商。

**微软授权外包商—概述**

“授权外包商”合作伙伴是指 ***非*** 的任何服务提供商，或使用“列表提供商”的数据中心资源转售云服务的服务提供商，这些服务提供商部署了适用的软件来提供服务。

简而言之，“列出的提供商”包括:

微软 Azure(有一些特定的例外)

亚马逊 AWS

谷歌云

阿里巴巴

重要的是要重申，新的灵活虚拟化优势将**而不是**适用于上面列出的提供商。

**2022 年 10 月 1 日前许可景观**

为了便于理解，让我们快速回顾一下 2022 年 10 月 1 日之前的许可安排。从本质上讲，它在以下方面具有相对的限制性:

你可以带给“授权外包商”的产品类型

可以部署许可证的服务器类型

从历史上看，当涉及到将许可证引入“授权外包商”环境时，在大多数情况下，您都能够做到这一点，*但是，*您必须将软件部署在**专用硬件**上。

因此，如果您使用来自“授权外包商”的共享服务器，并且您希望将自己的许可证带到第三方主机，这可能会违反微软的许可条款。如果您拥有许可证移动性权利，并且许可证上有有效的软件保障(SA ),并且外包商是许可证移动性合作伙伴，则此情况例外。

此外，如果您的第三方主机被微软认证为多租户主机(QMTH ),则他们获得了微软的特别授权，可以托管安装在多租户环境中的 Windows 虚拟桌面和微软 365 应用。

![](img/551c3551406c04f32930efd717ed5cb1.png)

Photo by [Workperch](https://unsplash.com/@workperch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/microsoft?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

**有什么变化**

这里变化的关键因素是客户在将自己的许可证交给“授权外包商”时可以获得的灵活性，授权外包商是指在所列提供商之外的数据中心为客户提供共享和专用服务器的第三方。

这包括可以通过订阅获得的任何许可证，或作为附带软件保障的完整许可证获得的任何许可证，包括:

Windows 操作系统

结构化查询语言

Windows 桌面操作系统

系统中心

面向企业的微软 365 应用

这一变化的关键区别在于，与针对特定客户的专用硬件相比，能够在共享服务上部署该软件。

需要注意的一些要点:

这些许可条款自 2022 年 10 月 1 日起生效。

主动软件保障(SA)是一个关键的先决条件。

如果您通过软件订阅(如 CSP)购买许可证，您也将获得这一好处，因此它并不仅限于 SA 客户。

如果您有内部许可证，并希望将其交给“授权外包商”，他们需要有有效的 SA，否则您将面临超出微软许可条款的风险。

回顾这些变化后，我们认为有一些潜在混淆的具体领域值得进一步解释:

**这是否会改变产品被外包给外包商时的许可方式？**当您将许可条款带到“授权外包商”的第三方托管环境中时，许可条款对于内部部署是相同的。例如，如果您拥有与产品相关的特定灾难恢复优势，这些优势将转移到“授权外包商”的环境中。

**执照合规责任**。如果您将许可证带到“授权外包商”的环境中，您将对这些许可证负责(除非您有第三方的托管服务),但是请注意，您仍然是被许可方，在审计场景中，责任将落在您身上。运用与内部许可证相同的尽职调查来管理这些许可证。

这是一个潜在的领域，微软将增加他们的审计活动。微软已经开放市场，允许授权外包商之间更大的竞争；但是，与此同时，他们完全有权保护自己的资产，并确保客户仍然遵守他们的许可证政策，并相应地管理他们的许可证合规性，因此，Microsoft 可能会发出更多的审核请求。

**我们以前说过，现在还要再说一遍——不要假设“云=许可证合规性”**

在与包括托管服务在内的第三方协商合同时，请检查以确保该托管服务是否包含软件资产管理组件，更重要的是，谁负责合规性。如果在合同开始时这一点不是绝对明确的，那么当面临审计和潜在的违规罚款时，这很快就会成为一个真正的问题。

**执照流动性。****灵活虚拟化的优势类似于通过软件协议实现的许可证移动，因为它允许部署到云环境，不同之处在于灵活虚拟化的优势适用于许多软件协议许可证移动未涵盖的产品。**

**此外，现在更多的客户也可以使用它。通过 SA 进行许可证移动要求客户使用“授权移动合作伙伴”，这是另一个需要处理的复杂问题。但是，当您根据灵活虚拟化优势进行部署时，您通常将拥有与内部部署相同的权限。**

**重要的是要澄清，在某些情况下，尤其是在“列出的提供商”周围，您仍然需要许可证移动性，例如。如果您使用 AWS，许可证移动性存在于共享租赁或专用主机上，因此您仍有机会以建设性和经济高效的方式将自己的许可证提供给那些“列出的提供商”。**

**微软最终是否会开放竞争环境，为“列出的提供商”创造与“授权外包商”社区相同的灵活性，或者增加“列出的提供商”名单上的提供商数量，还有待观察。**

**![](img/086803177e99d9b7ed70a72f59d2770f.png)**

**Photo by [Surface](https://unsplash.com/@surface?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/microsoft?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)**

****更多信息****

**如果您正在考虑将您的内部许可迁移到第三方托管提供商，那么在迁移到云之前，值得与许可专家(如 Version 1)讨论，以考虑所有许可机会、影响、陷阱和优势。**

**自带执照(BYOL)可能是一个雷区。作为 Microsoft 许可证专家，我们可以为您提供正确配置 Microsoft 许可证的最佳建议，以明确定义和记录的流程为基础，为您的软件投资提供合规、成本优化和功能最大化的回报。**

**如果你对第一版有任何疑问，请访问我们的[网站](https://www.version1.com/it-service/software-asset-management/)页面，或者[联系我们](https://www.version1.com/contact/)。此外，对于所有未来版本 1 的微软许可证更新，请在 LinkedIn[上关注我。](https://www.linkedin.com/in/wjdnelson/)**

****关于作者:** 威廉·尼尔森是微软 SAM 第 1 版的销售专员。**