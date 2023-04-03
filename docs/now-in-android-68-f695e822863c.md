# 现在在 Android #68 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-68-f695e822863c?source=collection_archive---------1----------------------->

![](img/b05c6487477f7c4afd61358cb29be4df.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 隐私沙箱、模块化、架构、性能、深度链接、Android Go 版、Android 13、Jetpack Glance 等等！

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 第 68 集视频和播客

*现在在 Android 中*也以视频和播客的形式提供。

# 隐私沙盒开发者预览版 5🔐

[隐私沙盒](https://developer.android.com/design-for-safety/privacy-sandbox)旨在开发新技术，改善用户隐私，为移动应用提供有效的个性化广告体验。开发者预览版 5 发布了，这个版本是一个重要的里程碑，将成为即将到来的隐私沙盒测试版的基础。请继续给我们您的反馈！查看博客文章中的变化:

[](https://android-developers.googleblog.com/2022/09/privacy-sandbox-developer-preview-5-is-here.html) [## 隐私沙盒:开发者预览版 5 来了！

### 由 Android 开发者关系 Fred Chung 今天发布，我们将在 Android 开发者上发布隐私沙盒…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/09/privacy-sandbox-developer-preview-5-is-here.html) 

# 🧩应用程序模块化指南

该团队刚刚发布了新的[模块化指南](https://developer.android.com/topic/modularization)。关于这个主题的指导一直是社区的最高要求之一，现在就在这里！该指南分为两部分。[概述页面](https://developer.android.com/topic/modularization)给你一个高层次的、理论性的概述。[通用模块化模式页面](https://developer.android.com/topic/modularization/patterns)深入探究了现代 Android 架构背景下的实际例子。请查看指南公告博客文章，了解更多相关信息。

[](https://android-developers.googleblog.com/2022/09/announcing-new-guide-to-android-app-modularization.html) [## 发布新的 Android 应用模块化指南

### 随着应用程序的规模和复杂性的增长，管理、构建和扩展变得越来越困难。一种方法是…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/09/announcing-new-guide-to-android-app-modularization.html) 

# 更多架构指导🏛

除了模块化，团队发布了更多的架构内容！

如果您需要让您的应用程序离线工作，我们可以满足您的需求。**新的** [**构建离线优先应用指南**](https://developer.android.com/topic/architecture/data-layer/offline-first) 帮助您设计应用，以正确处理读取和写入，并在没有互联网连接的设备中处理同步和冲突解决。

另一个新的向导是 UI 层 docs 中的 [**状态持有者和 UI 状态页面**](https://developer.android.com/topic/architecture/ui-layer/stateholders) **。并非所有内容都需要出现在 ViewModel 类中。本页介绍了在 UI 层中可以找到的不同类型的状态持有者，以及他们的职责。**

最后，如果您想了解关于架构的所有知识，并了解我们当前的最佳实践，**请查看** [**架构途径**](https://developer.android.com/courses/pathways/android-architecture) ，它更新了我们今年早些时候制作的架构 MAD 技能系列的所有视频和新文档。

# 疯狂技能:表演⚡️

MAD Skills 系列以通常的现场问答结束，其中 [Ben Weiss](https://twitter.com/keyboardsurfer) 、[Tomámlynari](https://twitter.com/mlykotom)、Carmen Jackson 和 [Chris Craik](https://twitter.com/chris_craik) 回答了你的问题。

为了总结这个系列，Ben 写了这篇博客，其中包含了所有视频的摘要！不要错过它！

[](/androiddevelopers/mad-skills-performance-wrap-up-33688abfc51f) [## MAD 技能表演—总结

### 八月份，MAD 技能视频和博客系列将帮助您开始学习性能。这是什么的要点…

medium.com](/androiddevelopers/mad-skills-performance-wrap-up-33688abfc51f) 

# AndroidX 释放🚀

自上一集以来，有一些值得强调的 AndroidX 版本。

[Core 和 core-ktx](https://developer.android.com/jetpack/androidx/releases/core) 达到 1.9.0 **稳定**。这个版本提高了与 Android 13 的兼容性，增加了可访问性框架和 compat APIs 之间的对等性，以及其他一些功能。其他版本包括**新内测** [汽车 app](https://developer.android.com/jetpack/androidx/releases/car-app#1.3.0-beta01) 1.3，以及**新内测** [导航](https://developer.android.com/jetpack/androidx/releases/navigation#2.6.0-alpha01) 2.6 和[测试 Ui 自动机](https://developer.android.com/jetpack/androidx/releases/test-uiautomator#2.3.0-alpha01) 2.3。

在此[链接](https://developer.android.com/jetpack/androidx/versions/all-channel)中查看其余的 AndroidX 版本。

# 文章📚和视频🎥

开发者关系团队写了关于抖音如何在 Android 上增强视频社交体验的文章。他们能够通过遵循 Android 的[性能指导](https://developer.android.com/topic/performance)，并运用他们对开发工具的深刻理解，如 [Android Gradle 插件](https://developer.android.com/studio/build/dependencies)和 [Jetpack 库](https://developer.android.com/jetpack/androidx/explorer)，显著提高他们的整体性能。点击此处阅读更多内容！

[](https://android-developers.googleblog.com/2022/08/precise-improvements-how-tiktok-enhanced-its-social-experience-on-android.html) [## 精确改进:抖音如何在 Android 上增强其视频社交体验

### 抖音服务于广泛的用户群体。对于世界各地的用户来说，他们中的一些人不可避免地会经历…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/08/precise-improvements-how-tiktok-enhanced-its-social-experience-on-android.html) 

Sabs 开始了一个关于深层链接的速成系列课程。第一部分是一篇[博文](/androiddevelopers/the-deep-links-crash-course-part-1-introduction-to-deep-links-2189e509e269)和一个[视频](https://www.youtube.com/watch?v=1qFIg-lz4Ys)的深度链接介绍。了解什么是深度链接，从 URIs 到应用程序链接，等等！

[](/androiddevelopers/the-deep-links-crash-course-part-1-introduction-to-deep-links-2189e509e269) [## 深度链接速成班，第 1 部分:深度链接介绍

### “深度链接能做什么？”我欢迎你参加深度链接速成班系列的第一部分…

medium.com](/androiddevelopers/the-deep-links-crash-course-part-1-introduction-to-deep-links-2189e509e269) 

第二部分介绍了不同类型的深层链接。如何设置、测试它们，并围绕它们构建最佳用户体验。

[](/androiddevelopers/the-deep-links-crash-course-part2-deep-links-from-zero-to-hero-37f94cc8fb88) [## 深度链接速成班，第二部分:从零到英雄的深度链接

### 在本帖中，我们将仔细看看不同类型的深层链接。

medium.com](/androiddevelopers/the-deep-links-crash-course-part2-deep-links-from-zero-to-hero-37f94cc8fb88) 

[Marcel](/@marxallski) 写了关于[用 Jetpack Glance](/androiddevelopers/experimenting-with-jetpack-glance-35fbffe520f4) 做实验的文章，其中包含了一个[独立的实验库](https://github.com/google/glance-experimental-tools)来补充 Jetpack Glance 中开发时通常需要但目前还不可用的工具。目前，它包括一个在你的应用程序中显示 RemoteViews 的组件(启用[实时编辑](https://developer.android.com/jetpack/compose/tooling#live-edit))，一个查看嵌入在应用程序中的 AppWidget 快照并与之交互的调试工具，以及一个 app widget 的 Material3 脚手架。

[](/androiddevelopers/experimenting-with-jetpack-glance-35fbffe520f4) [## 试用 Jetpack Glance

### 我们很高兴地宣布，新的 Jetpack Glance 框架已经达到 alpha-04。

medium.com](/androiddevelopers/experimenting-with-jetpack-glance-35fbffe520f4) 

[本·特伦戈夫](/@bentrengrove)在《作曲中写到[调试改编。看看吧，因为它还包含了一个](/androiddevelopers/jetpack-compose-debugging-recomposition-bfcf4a6f8d37)[的截屏](https://www.youtube.com/watch?v=SWBN0y0lFNY)，Ben 在 Jetsnack 中修复了一个性能问题，这是一个 Compose 示例。为此，Ben 还使用了 Android Studio 中的布局检查器，在那里你可以看到可组合函数的[重组和跳过计数](https://developer.android.com/jetpack/compose/tooling#recomposition-counts)。

[](/androiddevelopers/jetpack-compose-debugging-recomposition-bfcf4a6f8d37) [## Jetpack 合成:调试重组

### 在这篇文章中，我想向你展示我是如何研究 Jetsnack 中的一个性能问题的，以及我是如何进行调试和…

medium.com](/androiddevelopers/jetpack-compose-debugging-recomposition-bfcf4a6f8d37) 

[Nikariha](https://twitter.com/theDroidLady) 开始了另一个关于 Android Go 版优化的博客系列。第一部分介绍了 Android Go edition，为什么你想为它而构建，以及一些基于从 Google apps 构建 Gboard 和相机的经验的最佳实践。

[](https://android-developers.googleblog.com/2022/09/optimize-for-android-go-lessons-from-google-apps-part-1.html) [## 为 Android 优化(Go 版):谷歌应用的教训-第 1 部分

### 开发者关系工程师 Niharika Arora 发布的 Android 操作系统将计算能力带到了…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/09/optimize-for-android-go-lessons-from-google-apps-part-1.html) 

# 亚行播客 Episodes🎙

![](img/a057919c02eca1629e146b638b31b27b.png)

自从 Android 上一期 Now 发布以来，已经有一集 [Android 开发者后台](https://adbackstage.libsyn.com/)发布了。请点击下面的链接，或在您最喜欢的播客客户端查看:

在[第 188 集:Android 13](https://adbackstage.libsyn.com/episode-188-android-13) 中，Chet、Romain 和 Tor 谈论了他们最喜欢的新版本 Android 的一些新功能和变化，无论是对用户还是开发者。

# 那么现在…👋

这一次到此为止，随着[隐私沙盒](https://android-developers.googleblog.com/2022/09/privacy-sandbox-developer-preview-5-is-here.html)、[模块化](https://developer.android.com/topic/modularization)、[离线-优先](https://developer.android.com/topic/architecture/data-layer/offline-first)和[状态持有者](https://developer.android.com/topic/architecture/ui-layer/stateholders)架构指导、[性能狂技能系列](/androiddevelopers/mad-skills-performance-wrap-up-33688abfc51f)、 [AndroidX 发布](https://developer.android.com/jetpack/androidx/versions/all-channel)、关于[性能的文章](https://android-developers.googleblog.com/2022/08/precise-improvements-how-tiktok-enhanced-its-social-experience-on-android.html)、[深度链接](/androiddevelopers/the-deep-links-crash-course-part-1-introduction-to-deep-links-2189e509e269)、 [Jetpack 一览](/androiddevelopers/experimenting-with-jetpack-glance-35fbffe520f4)、[调试重组请尽快回到这里，等待 Android 开发者世界的下一次更新！](/androiddevelopers/jetpack-compose-debugging-recomposition-bfcf4a6f8d37)