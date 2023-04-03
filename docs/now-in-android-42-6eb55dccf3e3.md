# 现在在 Android #42 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-42-6eb55dccf3e3?source=collection_archive---------3----------------------->

![](img/839881ae031404132395bf9afff5515b.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 应用捆绑包、导航、Wear Compose、Android for Cars、附近的 API、协同程序、可折叠设备等等！

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 视频和播客形式的 NiA42

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

[](http://nowinandroid.googledevelopers.libsynpro.com/42-app-bundles-navigation-wear-compose-and-more) [## 现在在 Android 中:42 个应用捆绑包、导航、Wear Compose 等等！

### 欢迎回到 Android 中的现在，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。在…

nowinandroid.googledevelopers.libsynpro.com](http://nowinandroid.googledevelopers.libsynpro.com/42-app-bundles-navigation-wear-compose-and-more) 

# 应用捆绑包📦

**Google Play 将从 2021 年 8 月开始要求新应用与 Android 应用捆绑发布**。这将取代 APK 作为标准出版格式。

如果你还没有转换到应用捆绑包，请阅读这篇博文，了解应用捆绑包的好处以及它们如何帮助开发者和用户。

[](https://android-developers.googleblog.com/2021/06/the-future-of-android-app-bundles-is.html) [## Android 应用捆绑包的未来就在这里

### 自从我们在 2018 年 5 月推出 Android 应用捆绑包以来，我们已经看到我们的开发人员社区采用这一新标准来…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/06/the-future-of-android-app-bundles-is.html) 

# 疯狂的技能:更多导航总结🧭

关于[更多导航](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-vin7SvgoaR6wu24sAw-sE)的系列自上一期 Android Now 结束，以一个[直播 Q & A](https://youtu.be/4srssoBo0HU) 结束。感谢所有的问题，并浏览到录音，看看发生了什么。

像往常一样，这个系列有一个总结博客，上面有该集视频和文章的所有链接，还有相关技术的链接:

[](https://android-developers.googleblog.com/2021/07/mad-skills-navigation-series-2-wrap-up.html) [## 疯狂技能导航系列 2 总结！

### 这是一个总结！！我们刚刚完成了 MAD 技能导航的第二个系列。在这个系列中，我们再次访问了切特的…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/mad-skills-navigation-series-2-wrap-up.html) 

## 但是等等，还有更疯狂的内容！

对于正在进行的内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# AndroidX 释放🚀

alpha 中的一个新库是[**core-Splash Screen**](https://developer.android.com/jetpack/androidx/releases/core#core_splashscreen_version_100_2)，它为 Android 12 中的[闪屏 API](https://developer.android.com/about/versions/12/features/splash-screen)提供了向后兼容性。API 被反向移植到 API 23！

[**Jetpack Compose**](http://goo.gle/compose-docs) 和 [**DataStore**](https://developer.android.com/topic/libraries/architecture/datastore) 现已达到发布候选状态，这意味着 1.0 稳定版即将发布！

既然 Jetpack Compose 很快就要稳定下来，越来越多的技术将开始采用它。这已经是… WearOS 的情况了！ [**Wear Compose**](https://developer.android.com/jetpack/androidx/releases/wear-compose) 是一个使用 Jetpack Compose 为可穿戴设备编写应用的新库。它支持耐磨材料设计，第一个 alpha 版本的库刚刚登陆。阅读更多关于[发行说明](https://developer.android.com/jetpack/androidx/releases/wear-compose#1.0.0-alpha01)中包含的内容！

# 文章📗

## 更好的谷歌附近的 API 的物理故事

在附近的 APIs 系列中， [Isai Damier](https://medium.com/u/a2d70bdb57?source=post_page-----6eb55dccf3e3--------------------------------) 解释了需要物理邻近的不同旅程:单向消息检测、双向通信和使用快速配对的设备配对。

该系列的第一部分是关于第一个用例，即[nearly Messages API](https://developers.google.com/nearby/messages/overview)，它允许你在联网的 Android 和 iOS 设备之间发送小的二进制有效载荷。

[](/androiddevelopers/better-physical-stories-with-googles-nearby-apis-280be707bbf9) [## 更好的谷歌附近的 API 的物理故事

### 作为 Google Play 服务的一部分，Google 向希望增强体验的开发者提供附近的 APIs

medium.com](/androiddevelopers/better-physical-stories-with-googles-nearby-apis-280be707bbf9) 

## 没有互联网的双向通信:邻近连接

在附近的 API 系列的第二部分中， [Isai Damier](https://medium.com/u/a2d70bdb57?source=post_page-----6eb55dccf3e3--------------------------------) 讲述了第二个用例:双向通信，即使在没有互联网可用的情况下，用户也可以相互连接。但这也允许使用[邻近连接 API](https://developers.google.com/nearby/connections/overview) 传输无限量的数据。

[](/androiddevelopers/two-way-communication-without-internet-nearby-connections-b118530cb84d) [## 没有互联网的双向通信:邻近连接

### 附近的连接 API 允许您的用户相互连接，即使没有互联网可用。API…

medium.com](/androiddevelopers/two-way-communication-without-internet-nearby-connections-b118530cb84d) 

## 范围存储神话

应用程序将被要求在今年下半年将其`targetSdkVersion`更新到 API 30。这意味着**您的应用程序将需要使用** [**范围的存储**](https://developer.android.com/training/data-storage#scoped-storage) 。在这篇博客文章中，[妮可·博雷利](https://medium.com/u/2bbf49fa59bf?source=post_page-----6eb55dccf3e3--------------------------------)以问答的形式打破了一些范围存储的神话。

[](/androiddevelopers/scope-storage-myths-ca6a97d7ff37) [## 范围存储神话

### 不，你不必使用 SAF

medium.com](/androiddevelopers/scope-storage-myths-ca6a97d7ff37) 

## WorkManager 在多进程应用中的高级用法

[WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) 2.6 支持工人在任何进程中运行，并允许工人绑定到特定的进程。阅读 [Caren Chang](https://medium.com/u/b6f9dc502595?source=post_page-----6eb55dccf3e3--------------------------------) 如何解释 WorkManager 多进程 API。

[](/androiddevelopers/advanced-usage-of-workmanager-in-multi-process-apps-4ea2d7e75501) [## WorkManager 在多进程应用中的高级用法

### 在 WorkManager 2.5 中，我们使多进程应用程序能够更轻松地访问运行在…中的特定工作管理器实例

medium.com](/androiddevelopers/advanced-usage-of-workmanager-in-multi-process-apps-4ea2d7e75501) 

## 重复生命周期 API 设计故事

阅读我的博客文章，了解新的生命周期感知的`[repeatOnLifecycle](https://developer.android.com/topic/libraries/architecture/coroutines#restart)`协程 API 是如何设计的，以及为什么其他 API 被从`lifecycle-runtime-ktx`库中移除，以帮助开发人员防止错误。

[](/androiddevelopers/repeatonlifecycle-api-design-story-8670d1a7d333) [## 重复生命周期 API 设计故事

### 在这篇博文中，您将了解 Lifecycle.repeatOnLifecycle API 背后的设计决策。

medium.com](/androiddevelopers/repeatonlifecycle-api-design-story-8670d1a7d333) 

## 可折叠设备上的桌面模式

在这篇由[弗朗西斯科·罗马诺](https://medium.com/u/263cf3ffa055?source=post_page-----6eb55dccf3e3--------------------------------)撰写的博客文章中，你将学到一种简单有效的方法来调整你的应用在可折叠设备上运行时的布局。

[](/androiddevelopers/tabletop-mode-on-foldable-devices-d091b3c500b1) [## 可折叠设备上的桌面模式

### 展现您的视频播放器体验

medium.com](/androiddevelopers/tabletop-mode-on-foldable-devices-d091b3c500b1) 

# 汽车用安卓系统🚗

四月份发布的 [Android for Cars 应用程序库](https://developer.android.com/training/cars/apps)为你的汽车带来了导航、停车和充电应用程序。现在，一个新的[版本 1.1 正在 alpha](https://developer.android.com/jetpack/androidx/releases/car-app#1.1.0-alpha01) 中，它具有登录模板、长消息模板、多长度文本和地图交互性等特性。

[](https://android-developers.googleblog.com/2021/06/improve-your-app-mileage-with-android.html) [## 使用 Android for Cars 应用程序库提高您的应用里程

### 4 月，我们宣布了作为 Jetpack 一部分的第一个 Android for Cars 应用程序库版本，达到了一个里程碑…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/06/improve-your-app-mileage-with-android.html) 

# ADB 播客片段🎧

自从上一期《现在》在安卓发布以来，已经有几集[安卓开发者的后台](http://adbackstage.libsyn.com/)发布了。点击下面的链接，或者在你最喜欢的播客客户端查看它们:

[**第 168 集:材料合成**](http://adbackstage.libsyn.com/episode-168-material-composition) 是我们关于 Jetpack Compose (AD/BC)的迷你系列的第三集，其中 Nick 和 Romain 与 Clara Bayarri 和 Matvei Malkov 一起谈论 Compose 对材料设计的支持。他们讨论了 Compose 如何支持现成的素材组件和素材主题化，如何定制自己的组件， [Material You](https://material.io/blog/announcing-material-you) 等等！

在 [**第 169 集:测试**](http://adbackstage.libsyn.com/episode-169-testing) 中，Romain 和 Tor 与来自 Android Studio 团队的 Adarsh Fernando、Arif Sukoco 和 Zhou 一起讲述了最近对测试的改进，如自动化测试快照、测试矩阵工具、统一的 Gradle test runner 以及 Gradle 管理的虚拟设备。

# 那么现在…👋

这次到此为止。疯狂获取关于应用捆绑包的[未来的信息，以及](https://android-developers.googleblog.com/2021/06/the-future-of-android-app-bundles-is.html)[最后的疯狂技能系列](https://android-developers.googleblog.com/2021/07/mad-skills-navigation-series-2-wrap-up.html)中的更多导航！检查一下[核心闪屏](https://developer.android.com/jetpack/androidx/releases/core#core_splashscreen_version_100_2)、[穿戴组合](https://developer.android.com/jetpack/androidx/releases/wear-compose)和[汽车用安卓](https://developer.android.com/jetpack/androidx/releases/car-app#1.1.0-alpha01)的新 alpha 库。阅读关于[附近消息 API](/androiddevelopers/better-physical-stories-with-googles-nearby-apis-280be707bbf9) 、[附近连接 API](/androiddevelopers/two-way-communication-without-internet-nearby-connections-b118530cb84d) 、[多进程应用中的工作管理器](/androiddevelopers/advanced-usage-of-workmanager-in-multi-process-apps-4ea2d7e75501)、 [repeatOnLifecycle API](/androiddevelopers/repeatonlifecycle-api-design-story-8670d1a7d333) 的设计、可折叠设备上的[桌面模式](/androiddevelopers/tabletop-mode-on-foldable-devices-d091b3c500b1)的最新文章！收听[最新亚行播客](http://adbackstage.libsyn.com/)！请尽快回到这里，收听 Android 开发者世界的下一次更新。