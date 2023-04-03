# 现在在 Android #55 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-55-ff44f10d7d28?source=collection_archive---------8----------------------->

![](img/9691fdaaf86cd37a6f37c51a23435b61.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [安卓工作室](https://developer.android.com/studio)、[数据仓库](https://developer.android.com/topic/libraries/architecture/datastore)、[扫视](https://developer.android.com/jetpack/androidx/releases/glance)、[汽车操作系统](https://developer.android.com/training/cars)、[谷歌地图](https://github.com/googlemaps/android-maps-compose)、[基线简介](https://developer.android.com/studio/profile/baselineprofiles)、[对讲](https://developer.android.com/guide/topics/ui/accessibility/testing#talkback)、[大屏幕](https://developer.android.com/large-screens)等等！

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 安卓工作室大黄蜂🐝稳定的

Android Studio 大黄蜂(2021.1.1)现已稳定。我们已经对其进行了修补，以解决一些发布问题，因此请务必升级！它改进了典型开发人员工作流程的功能:**构建和部署，概要分析和检查，以及设计**。

一些值得注意的新增功能包括 Android Studio 和您的持续集成(CI)服务器之间的统一测试执行、通过 Wi-Fi 支持 ADB 的便捷配对流程、帮助您识别和分析应用程序中的 jank 的改进分析器工具，以及无需将应用程序部署到设备即可预览动画和 UI 交互的新方法。

[](https://android-developers.googleblog.com/2022/01/android-studio-bumblebee-202111-stable.html) [## Android Studio 大黄蜂(2021.1.1)稳定

### Android Studio 团队一直忙于 Android Studio bumble bee(2021 . 1 . 1)的稳定发布🐝还有安卓……

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/01/android-studio-bumblebee-202111-stable.html) 

# 疯狂技能:数据存储🗄️

数据存储疯狂技能系列将继续播出更多剧集。在第三集中，[新美乐股份公司](https://medium.com/u/f4d5f1a633bb?source=post_page-----f9cbc3119514-----------------------------------)覆盖了[偏好数据存储](https://developer.android.com/topic/libraries/architecture/datastore)，这是两个[数据存储](https://developer.android.com/topic/libraries/architecture/datastore)实现之一。由于 Preferences DataStore 使用键值对来存储较小的数据集，因此 Jetpack 解决方案取代了 [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences) 。

[](/androiddevelopers/all-about-preferences-datastore-cc7995679334) [## 关于首选项数据存储的所有信息

### 在本帖中，我们将看看首选项数据存储，这是两种数据存储实现之一。我们将讨论如何…

medium.com](/androiddevelopers/all-about-preferences-datastore-cc7995679334) 

在第四集，[新美乐股份公司](https://medium.com/u/f4d5f1a633bb?source=post_page-----f9cbc3119514-----------------------------------)讲述了[原型数据存储](https://developer.android.com/topic/libraries/architecture/datastore#datastore-typed)，另一个实现。不同之处在于，Proto DataStore 使用由[协议缓冲区](https://developers.google.com/protocol-buffers)支持的类型化对象来存储较小的数据集，同时提供类型安全。

[](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879) [## 关于原始数据存储的所有信息

### 在本帖中，我们将了解 Proto DataStore，这是两种数据存储实现之一。我们将讨论如何创建…

medium.com](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879) 

[新美乐股份公司](https://medium.com/u/f4d5f1a633bb?source=post_page-----f9cbc3119514-----------------------------------)还制作了一集关于数据存储最佳实践的节目，在那里你将学习如何执行同步工作，以及如何让它与 Kotlin 数据类序列化和 Hilt 一起工作。这是最佳实践第 1 部分！因此，请留意未来的剧集😄

# 一瞥:用简单的⌚️制成的瓷砖

去年，我们宣布了[耐磨瓷砖 API](https://developer.android.com/reference/kotlin/androidx/wear/tiles/package-summary) 。为了补充 Java API，我们很高兴地宣布, [**Glance**](https://developer.android.com/jetpack/androidx/releases/glance) 中添加了对穿戴操作系统磁贴的**支持，这是一个基于 Jetpack Compose 构建的新框架，旨在使其更容易构建 Android 应用程序之外的表面。由于该库处于 alpha 版本，我们希望得到您的反馈。**

[](https://android-developers.googleblog.com/2022/01/announcing-glance-tiles-for-wear-os.html) [## 公告一瞥:瓷砖让穿戴变得简单

### 去年，我们发布了耐磨瓷砖 API。为了补充 Java API，我们很高兴地宣布支持…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/01/announcing-glance-tiles-for-wear-os.html) 

# 为 Android 汽车操作系统构建应用🚘

[汽车应用程序库](https://developer.android.com/jetpack/androidx/releases/car-app)版本 1.2 已经处于测试阶段，允许应用程序开发人员开始为 Android 汽车操作系统构建他们的导航、停车和充电应用程序。现在，开发者可以开始使用[汽车操作系统模拟器](https://developer.android.com/training/cars/testing#test-automotive-os)在 Android 汽车操作系统和 Android Auto 上构建和测试这些类别的应用。

[](https://android-developers.googleblog.com/2022/01/building-apps-for-android-automotive-os.html) [## 为 Android 汽车操作系统构建应用

### 今天，我们宣布推出汽车应用程序库 1.2 测试版，使应用程序开发人员能够开始…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/01/building-apps-for-android-automotive-os.html) 

# AndroidX 释放🚀

既然我们已经讨论了库更新，那么让我们来看看 AndroidX 版本的最新进展。

**导航 2.4 现已稳定**！你可以在[发行说明](https://developer.android.com/jetpack/androidx/releases/navigation#2.4.0)中看到这个版本引入了多少。它在 Kotlin 中被重写，具有两个窗格集成，导航路线+ Kotlin DSL 改进，导航合成的第一个稳定版本，以及多个后台堆栈支持。有了这个， [**剑柄-导航-作曲库**](https://developer.android.com/jetpack/androidx/releases/hilt#hilt-navigation-compose-1.0.0) 也达到 1.0 稳定。

**滑动窗格布局 1.2** 也很稳定，加上一堆其他的[改进](http://slidingpanelayout)，现在已经可以折叠感知了！

由于 **AndroidX 窗口库**达到了其第一个 1.0.0 [稳定里程碑](https://developer.android.com/jetpack/androidx/releases/window#1.0.0)，所以[sliding panel layout](https://developer.android.com/reference/androidx/slidingpanelayout/widget/SlidingPaneLayout)的自动折叠感知行为是可能的。这个库通过 WindowInfoTracker 和 FoldingFeature APIs 增加了对折叠手机的支持。

[**CameraX 库**版本 1.1](https://developer.android.com/jetpack/androidx/releases/camera#1.1.0-beta01) 达到 beta，从现在开始，所有 CameraX 库都将在同一个版本号上对齐。

该团队还发布了一个集成了 [**谷歌地图和 Jetpack Compose**](https://github.com/googlemaps/android-maps-compose) 的库。它包含 Android 版地图 SDK 的合成组件。您可以在[项目的自述文件](https://github.com/googlemaps/android-maps-compose#readme)中了解更多信息。

[](https://github.com/googlemaps/android-maps-compose) [## GitHub-Google Maps/android-Maps-Compose:Jetpack 为 Android 的地图 SDK 编写组件

### 此存储库包含 Android 地图 SDK 的 Jetpack Compose 组件。启用 Kotlin 的项目 Jetpack…

github.com](https://github.com/googlemaps/android-maps-compose) 

# 文章📚

[Kateryna Semenova](https://twitter.com/skateryna) 写了关于使用基准配置文件提高**应用性能的文章**或者如何将启动时间提高 40%！启动时间很重要，[基线配置文件](https://developer.android.com/studio/profile/baselineprofiles)是提供配置文件和改善用户体验的新机制。

[](https://android-developers.googleblog.com/2022/01/improving-app-performance-with-baseline.html) [## 通过基线配置文件提高应用性能

### 或者 DevRel 工程师 Kateryna Semenova 发布的如何将启动时间提高 40%；拉胡尔·拉维库马尔，软件…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/01/improving-app-performance-with-baseline.html) 

如果你对媒体感兴趣，想知道谷歌的高性能音频库如何提高卡拉 ok 应用 Smule 的录制质量和完成率，看看这篇文章吧！

[](https://android-developers.googleblog.com/2022/02/smule-adopts-googles-oboe-to-improve.html) [## Smule 采用谷歌的双簧管来提高录音质量和完成率

### 作为有史以来下载量最大的歌唱应用，Smule Inc .一直在 Android 上投资，以改善整体音频…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/02/smule-adopts-googles-oboe-to-improve.html) 

如果你喜欢阅读公司如何使用谷歌产品取得成功，这里有一些关于[微软 Lens 如何使用 CameraX](https://developer.android.com/stories/apps/microsoft-camerax) 提高开发人员生产力的故事，以及 [Zomato 如何将他们的应用速度提高 30%](https://developer.android.com/stories/apps/zomato) 的故事。

# 新文档📖

您使用协程或工作管理器进行后台工作吗？团队更新了[后台工作指南](https://developer.android.com/guide/background)，帮助你选择最适合你的用例的库。这取决于工作是否是持久的，如果它需要立即运行，那么它就是长时间运行的，或者是可推迟的。

如果你在 [Android TV](https://developer.android.com/training/tv) 上工作，你应该知道团队创造的[可访问性最佳实践](https://developer.android.com/training/tv/accessibility)。它为本地和非本地应用程序提供建议。了解为什么可访问性对您的电视应用程序很重要，如何在使用[对讲](https://developer.android.com/guide/topics/ui/accessibility/testing#talkback)时评估您的应用程序，如何采用系统字幕设置，以及[更多](https://developer.android.com/training/tv/accessibility)！

# 无障碍系列🌐

谈论可访问性…可访问性系列的下一个是谷歌屏幕阅读器 [TalkBack](https://developer.android.com/guide/topics/ui/accessibility/testing#talkback) ！在此视频中，了解什么是 TalkBack，如何设置它，如何使用它在应用程序中导航，以及如何使用它来提高应用程序的可访问性。

# 亚行播客 Episodes🎙

![](img/215c8785b29b84029e26c8c15fb8ada8.png)

自从 Android 上一期 Now 发布以来，已经有一集 [Android 开发者后台](https://adbackstage.libsyn.com/)发布了。请点击下面的链接，或在您最喜欢的播客客户端查看:

在 [**第 182 集:大屏幕是一件大事**](https://adbackstage.libsyn.com/episode-182-large-screens-are-a-big-deal) 中，克拉拉、弗洛里纳和丹尼尔加入了你通常的主持人，谈论大屏幕，它们是什么，以及它们对应用程序开发人员意味着什么。您还将了解在大屏幕设备上构建高质量体验所需的资源:从示例和指南到规范布局和新的 API(如窗口大小类)。声明:弗洛里纳对此非常兴奋，不要错过史诗般的*大银幕！大屏幕！大屏幕！*简介！

# 那么现在…👋

这一次到此为止，随着 Android Studio Bumblebee 的稳定， [DataStore MAD Skills 系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8to3Ere-ePuco69yBUmQ9C)、 [AndroidX](https://developer.android.com/jetpack/androidx/versions/all-channel) 发布， [Glance](https://developer.android.com/jetpack/androidx/releases/glance) 为 Wear OS 添加磁贴，[汽车应用程序库](https://developer.android.com/jetpack/androidx/releases/car-app) beta，关于[基线配置文件的文章](https://android-developers.googleblog.com/2022/01/improving-app-performance-with-baseline.html)、 [Oboe](https://android-developers.googleblog.com/2022/02/smule-adopts-googles-oboe-to-improve.html) ，关于[后台工作的文档更新](https://developer.android.com/guide/background)和 [Android TV 可访问性](https://developer.android.com/training/tv/accessibility)、[可访问性](https://www.youtube.com/watch?v=wWDYIGk0Kdo)请尽快回到这里，等待 Android 开发者世界的下一次更新。