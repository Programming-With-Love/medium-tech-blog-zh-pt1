# 现在在 Android #58 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-58-a8919ba01d45?source=collection_archive---------4----------------------->

![](img/5cd6b6f4e350f41c31d5b09d8160d650.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [Android 13 DP2](https://android-developers.googleblog.com/2022/03/second-preview-android-13.html) 、 [MAD 技能架构](https://www.youtube.com/watch?v=TPWmfJq16rA&list=PLWz5rJ2EKKc8GZWCbUm3tBXKeqIi3rcVX)、Android TV & Google TV 集成、 [FHIR SDK](/androiddevelopers/our-fhir-sdk-for-android-developers-9f8455e0b42f) 、[大屏](https://android-developers.googleblog.com/2022/03/helping-users-discover-quality-apps-on.html)、 [Play 开发者报告 API](https://android-developers.googleblog.com/2022/03/play-developer-reporting-API.html) 、[性能类](https://android-developers.googleblog.com/2022/03/using-performance-class-to-optimize.html)、 [AndroidX 发布](https://developer.android.com/jetpack/androidx/versions/all-channel)

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 安卓 13 DP 2😍

最近我们分享了 Android 13 开发者预览版 2，有更多的新功能和变化供您在应用中尝试！一些值得注意的功能包括开发者可降级权限，允许您的应用程序通过降级之前授予的运行时权限来保护用户隐私，以及蓝牙 LE Audio，帮助用户接收高保真音频而不牺牲电池寿命；它还可以在不同的用例之间无缝切换，这是蓝牙 Classic 无法实现的。查看帖子中的所有新功能！

[](https://android-developers.googleblog.com/2022/03/second-preview-android-13.html) [## Android 13 开发者预览版 2

### 上个月，我们发布了 Android 13 的第一个开发者预览版，围绕我们的核心主题隐私和…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/03/second-preview-android-13.html) 

# 疯狂技能:建筑😎

![](img/d13236bdc18811b0a00032988c1c2f06.png)

新架构[狂技能](https://www.youtube.com/watch?v=TPWmfJq16rA&list=PLWz5rJ2EKKc8GZWCbUm3tBXKeqIi3rcVX)系列有两集新剧。

Jose 介绍了数据层及其两个组件:存储库和数据源。你将深入了解这两者的角色，并理解它们的区别。您还将了解数据不变性、错误处理、线程测试等等！

[TJ](https://medium.com/u/10f0ee47a699?source=post_page-----a8919ba01d45--------------------------------) 在这一集的 MAD skills 中介绍了 UI 层使用 JetNews 示例应用程序作为案例研究您将了解 UI 层管道、UI 状态曝光、UI 状态消耗以及更多！

对于正在进行的内容，一定要查看 YouTube 上的[疯狂技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# 与安卓电视和谷歌电视系列整合📺

《与安卓电视和谷歌电视融合》系列还有两集。

米盖尔和阿德昆勒讨论账户关联。Google 帐户链接允许您安全地将用户的 Google 帐户与他们在您平台上的帐户链接起来，从而允许 Google 应用程序和设备访问您的服务，这是 Android TV & Google TV 上的许多集成所必需的。他们讨论了 OAuth 的基础知识，比如实现您的授权、令牌交换和撤销端点。您还将了解 Web OAuth、Streamlined 和 App Flip 链接流之间的区别。

在下一个视频中， [Paul](https://medium.com/u/9e7508235c54?source=post_page-----a8919ba01d45--------------------------------) 探讨了整合和验证媒体会话的最佳实践，这是 Android 应用程序与媒体内容交互的统一方式。MediaSessions 允许不同的设备(如智能扬声器、手表、外设和附件)出现，并与回放和内容元数据进行交互。

# 面向 Android 开发者的 FHIR SDK🏥

中低收入国家的社区卫生工作者使用移动设备作为开展社区外联和提供关键卫生服务的重要工具。不幸的是，缺乏数据互操作性意味着患者记录是支离破碎的，护理人员可能只会收到不完整的信息。去年，谷歌[引入了](https://blog.google/technology/health/working-who-power-digital-health-apps/?_ga=2.23799353.208967627.1646097286-1931423887.1635179599)与世界卫生组织的合作，以建立一个开源软件开发工具包，旨在帮助开发人员使用快速医疗互通资源(FHIR) [医疗数据全球标准](https://www.who.int/teams/digital-health-and-innovation/smart-guidelines/fhir-based-smart-guidelines)构建移动解决方案。阅读这篇文章，了解这个 SDK 如何帮助您创建应用程序来帮助中低收入国家的社区卫生工作者。

[](/androiddevelopers/our-fhir-sdk-for-android-developers-9f8455e0b42f) [## 面向 Android 开发人员的 FHIR SDK

### 本博客由 Heath AI 产品管理高级总监 Katherine Chou 和……

medium.com](/androiddevelopers/our-fhir-sdk-for-android-developers-9f8455e0b42f) 

# 帮助用户在大屏幕上发现优质应用🔎

大屏幕的采用正在快速增长，现在有超过 2.5 亿活跃的 Android 平板电脑、可折叠手机和 ChromeOS 设备。为了帮助人们充分利用他们的设备，我们在 Google Play 中进行了三大改变，以使用户能够发现和参与高质量的应用程序和游戏:排名和可推广性的变化，低质量应用程序的提醒，以及特定于设备的评级和评论。请在帖子中阅读所有相关内容！

[](https://android-developers.googleblog.com/2022/03/helping-users-discover-quality-apps-on.html) [## 帮助用户在大屏幕上发现优质应用

### 大屏幕的覆盖范围正在扩大，现在有超过 2.5 亿活跃的 Android 平板电脑、可折叠手机和 ChromeOS 设备。作为…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/03/helping-users-discover-quality-apps-on.html) 

# 文章📚

在这篇文章中，Lauren 谈到了 Android vitals，这是一种跟踪您的应用程序在 Google Play 控制台中的表现的好方法。现在，Android vitals 有了新的使用案例，包括构建内部仪表盘、与其他数据集结合进行更深入的分析、自动化故障排除和发布。在这篇文章中学习更多关于 API 和如何使用它的知识！

[](https://android-developers.googleblog.com/2022/03/play-developer-reporting-API.html) [## 通过新的 Play 开发者报告 API 访问 Android 生命数据

### 质量是你的游戏或应用在 Google Play 上取得成功的基础，而 Google Play 控制台中的 Android 命脉是一个…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/03/play-developer-reporting-API.html) 

[alpha](https://developer.android.com/jetpack/androidx/releases/core#core_performance_version_10_2) 中的 [Jetpack 核心性能库](https://developer.android.com/reference/kotlin/androidx/core/performance/package-summary?hl=en)已经启动！该库使您能够轻松识别设备的功能，并相应地定制您的用户体验。作为开发人员，这意味着您可以可靠地将具有相同性能水平的设备分组，并针对这些不同的组定制您的应用程序的行为。这使您能够为使用更多或更少功能设备的用户提供最佳体验。看看[唐](https://medium.com/u/7f5a2cb6598e?source=post_page-----a8919ba01d45--------------------------------)和弗朗索瓦的文章。

[](https://android-developers.googleblog.com/2022/03/using-performance-class-to-optimize.html) [## 使用性能等级优化您的用户体验

### 今天，我们将在 alpha 中发布 Jetpack 核心性能库。这个库使你很容易理解…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/03/using-performance-class-to-optimize.html) 

# AndroidX 释放🚀

让我们来看看自 Android 版《Now》最后一集以来，AndroidX 版本发生了什么。

我们在 alpha-01 中有几个库，包括 [Activity 版本 1.6.0-alpha01](https://developer.android.com/jetpack/androidx/releases/activity#1.6.0-alpha01) 、[custom view-pooling container 版本 1.0.0-alpha01](https://developer.android.com/jetpack/androidx/releases/customview#customview-poolingcontainer-1.0.0-alpha01) 和 [Junit-Gtest 版本 1.0.0-alpha01](https://developer.android.com/jetpack/androidx/releases/test#junit-gtest-1.0.0-alpha01) 。

[汽车 App 版本 1.2.0-rc01](https://developer.android.com/jetpack/androidx/releases/car-app#1.2.0-rc01) 和 [Mediarouter 版本 1.3.0-rc01](https://developer.android.com/jetpack/androidx/releases/mediarouter#1.3.0-rc01) 也在 rc-01 中。

# 那么现在…

这次就这些了 [Android 13 DP2](https://android-developers.googleblog.com/2022/03/second-preview-android-13.html) 、 [MAD 技能架构](https://www.youtube.com/watch?v=TPWmfJq16rA&list=PLWz5rJ2EKKc8GZWCbUm3tBXKeqIi3rcVX)、Android TV & Google TV 集成、 [FHIR SDK](/androiddevelopers/our-fhir-sdk-for-android-developers-9f8455e0b42f) 、[大屏](https://android-developers.googleblog.com/2022/03/helping-users-discover-quality-apps-on.html)、 [Play 开发者报告 API](https://android-developers.googleblog.com/2022/03/play-developer-reporting-API.html) 、[性能类](https://android-developers.googleblog.com/2022/03/using-performance-class-to-optimize.html)、 [AndroidX 发布](https://medium.com/r?url=https%3A%2F%2Fdeveloper.android.com%2Fjetpack%2Fandroidx%2Fversions%2Fall-channel)。请尽快回到这里，等待 Android 开发者世界的下一次更新。