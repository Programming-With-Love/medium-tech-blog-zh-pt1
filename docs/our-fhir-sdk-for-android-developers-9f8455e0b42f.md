# 面向 Android 开发人员的 FHIR SDK

> 原文：<https://medium.com/androiddevelopers/our-fhir-sdk-for-android-developers-9f8455e0b42f?source=collection_archive---------2----------------------->

![](img/41a220e2a758581747906b1ddaeca9e6.png)

*这篇博客由 Heath AI 产品管理高级总监 Katherine Chou 和 Android 平台安全负责人 Sudhi Herle 共同撰写*

对于中低收入国家(LMICs)的社区卫生工作者来说，移动设备已经成为开展社区宣传和提供重要卫生服务的重要工具，例如进行健康筛查、分发药物和访问免疫记录。不幸的是，缺乏数据互操作性意味着患者记录在不同的外展计划或应用程序之间是支离破碎的，护理人员不得不使用不完整的信息为患者做出决定。

# **与世卫组织合作，帮助开发者构建安全的移动解决方案**

去年，我们[引入了](https://blog.google/technology/health/working-who-power-digital-health-apps/?_ga=2.23799353.208967627.1646097286-1931423887.1635179599)与世界卫生组织(世卫组织)的合作，以建立一个开源软件开发工具包(SDK)来创建安全的可互操作的移动医疗应用。该 SDK 旨在帮助开发人员使用快速医疗保健互操作性资源(FHIR) [医疗保健数据全球标准](https://www.who.int/teams/digital-health-and-innovation/smart-guidelines/fhir-based-smart-guidelines)构建移动解决方案，该标准正被广泛采用，以解决碎片化问题并促进更加以患者为中心的护理。Android FHIR SDK 将允许开发人员更轻松地创建应用程序，帮助 LMICs 中的社区卫生工作者提供更好的推广和护理。

# **这是怎么回事？让我们仔细看看。**

FHIR 的强大之处在于它能够在移动应用程序、电子健康记录和其他数字临床工具之间实现轻松安全的信息交换。

低收入国家的社区卫生工作者面临的一个挑战是，他们经常不得不在连接不可靠的地区工作。这就是为什么我们设计了我们的 SDK，以允许 Android 应用离线运行，并在本地存储和处理 FHIR 中的数据，从而允许社区卫生工作者继续访问他们提供护理所需的重要信息。

FHIR SDK 还使应用程序开发人员能够更容易地以标准化的方式为医疗保健工作者构建移动工具。以数据捕获库为例。以前，为数据收集问卷构建 Android UI 是一个耗时且容易出错的过程。我们在 SDK 中的数据捕获库实现了 [FHIR 结构化数据捕获指南](https://build.fhir.org/ig/HL7/sdc/index.html)，以在运行时生成 UI 组件，并将响应提取为 FHIR 数据，从而减少所需的开发时间和工作量。这使得开发人员可以集中精力设计应用程序来满足当地社区的需求，而不必担心管理临床内容和数据标准要求。此外，Android FHIR SDK 为开发人员提供了离线搜索功能和云同步 API。此外，由于许多地区的医疗保健系统依赖于本地服务器或地区认可的云服务器，我们使 SDK 与云无关，并可适应开发人员选择的任何 FHIR 服务器。

# **开发人员如何使用 SDK 支持一线医疗工作者**

我们很高兴开始与创新技术提供商合作，将 Android FHIR SDK 应用于增强社区卫生工作者的能力，并实现公平获得高质量的护理。

Ona 是一家在利比亚工作的全球健康技术公司，[目前正在使用 SDK 来支持 *Quest*](https://ona.io/home/introducing-quest-fhir-native-case-management/) ，这是一款开源应用程序，允许开发人员使用 FHIR 来定义表格和捕捉数据，以利用不断增长的 Android FHIR SDK 和世卫组织智能指南生态系统。Ona 正在使用 Quest 来增强那些在卫生和人道主义救援工作第一线工作的人们的能力。

在我们的 SDK 上与我们合作的其他医疗技术合作伙伴包括 [IPRD 解决方案](https://www.iprdsolutions.com/)，我们正在与它合作，帮助 2000 多名社区卫生工作者向尼日利亚的 70 多万人分发疟疾预防网， [Lattice Innovations](https://www.thelattice.in/) ，一家总部位于印度的医疗技术解决方案公司，以及[argussoft](https://www.who.int/publications/m/item/em-care-newsletter-november-2021)，一家为协调紧急护理开发卫生人力平台的印度公司。

# **展望未来**

我们的目标是支持 Android 开发者的生态系统，为 LMICs 开发下一代移动健康工具。要了解更多信息或开始运行，请查看[项目的 Github](https://github.com/google/android-fhir) ，并在我们的活动[中了解更多关于我们与世卫组织的合作，与谷歌健康一起检查](https://www.youtube.com/watch?v=2XQZQR477fg)。