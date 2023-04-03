# 使用 Mendix，让“看起来简单的困难部分”变得简单

> 原文：<https://medium.com/mendix/making-the-hard-part-of-making-it-look-easy-easy-with-mendix-5a9472b37593?source=collection_archive---------2----------------------->

![](img/7c75a56b8ae2afab1dd87750b3e4355e.png)

我最近读了 Lorin Hochstein 的这篇文章，Lorin 在文章中讨论了“让它看起来容易的困难部分”。这让我思考这与低代码以及我使用 Mendix 为客户交付软件解决方案的经历有什么关系。作为一名软件工程师，我倾向于首先关注解决方案的工程方面，然后再考虑交付的哲学方面。思考这篇文章如何特别适用于我的低代码交付经历，让我踏上了一段稍微不同的旅程。

让我们先来看一下独立的 Mendix 解决方案。在 Mendix 中开发解决方案时，展示熟练的行为相当容易。你甚至不需要特别专业就能做到这一点。至少对于大多数独立解决方案来说是这样。许多元素都被抽象出来，这样你就可以专注于成为一个创造者，而不是担心解决方案的技术复杂性。当然，在某些情况下，需要更深层次的技术技能和专业知识，但是在某种程度上，其中的一些也可以抽象到更容易理解和实现的程度。与其他系统集成只是一个例子，说明了额外的技术技能是有帮助的。因此，带来额外的深入的技术和业务技能和理解总是有好处的，但是对于一些非独立的 Mendix 解决方案，Mendix 如何帮助我们使困难的部分看起来容易呢？

我最初来自 SAP NetWeaver 背景，我习惯于传统的软件开发。我知道改变事情是多么的耗时和昂贵，或许也知道让客户满意是多么的困难。由于 SAP 系统的复杂性，回答问题有时会花费很长时间，而且您需要对复杂的 SAP 环境的不同方面有深入的技术理解。在 SAP 过去几十年的发展和向云的过渡中，该产品无疑跟上了人们的期望，但同时，这也带来了自身的一系列挑战。要成为该领域的专家，并让交付看起来毫不费力，可能是一项挑战。从这个意义上说，SAP 并不是唯一的。得益于 Mendix 与 SAP 的合作伙伴关系以及抽象 SAP 连接器的开发，我们可以加快许多 SAP 解决方案的开发，尤其是在所需功能的标准 SAP 费奥里应用程序不存在的情况下。

我们已经在 Mendix 中为客户交付了相当数量的 SAP 应用程序，并且总是出现相同的问题。对于其中的每一项，我们都可以提供选项。我们反复遇到的一些问题是:

*   在与我的 SAP 系统交互时，该解决方案会包含我的 SAP 角色吗？
*   应用程序是否会被设计为在云中公开但不存储敏感信息？
*   该解决方案是否支持我们的单点登录提供商，使您的应用体验看起来无缝？
*   该解决方案将与 SAP 的各种内部和云解决方案集成吗？
*   能否在我们的私有云基础设施上部署 Mendix？

由于 Mendix 与 SAP 的合作伙伴关系，将 Mendix 应用程序轻松连接到 SAP 的大量工作已经完成，从而可以轻松实现上述问题的选项。Mendix 中 SAP 构建模块的一些很好的例子有:

*   面向 SAP 解决方案的 [OData 连接器](https://appstore.home.mendix.com/link/app/74525/Mendix/OData-Connector-for-SAP-solutions)和面向 SAP 解决方案的 [OData 查询构建器连接器](https://appstore.home.mendix.com/link/app/110732/Mendix/OData-Query-Builder-Connector-for-SAP-solutions)有助于轻松连接到 SAP 的 OData 服务
*   用于 SAP 云平台的 [XSUAA 连接器通过 SAP 云平台用于 SAP](https://appstore.home.mendix.com/link/app/78091/Mendix/XSUAA-Connector-for-SAP-Cloud-Platform) SSO
*   SAP Leonardo 机器学习服务的[连接器](https://appstore.home.mendix.com/link/app/107221/Mendix/Connector-for-SAP-Leonardo-Machine-Learning-Foundation)

每个客户的系统环境都是不同的，这些差异会带来不同的挑战，但是 Mendix 已经在一些方面做了大量的工作。由于对这些技术方面的抽象，展示熟练的行为变得前所未有的简单，并且处理“让它看起来简单的困难部分”来帮助你实现它！

# 刚接触 Mendix？

创建有影响力的应用程序 Mendix 是一个低代码应用程序开发平台，可让您更快地投入使用并获得成功。

![](img/86e1fc3d52c4d5473c6c151f792999ec.png)

[Click here to create your own free app with Mendix and share it with unlimited users](https://signup.mendix.com/link/signup/?source=direct)