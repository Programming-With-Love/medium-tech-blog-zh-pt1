# 现在在 Android #43 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-43-603ddab2bcc8?source=collection_archive---------5----------------------->

![](img/639ab63cdb9de7e62cf8e6930df746b8.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [Android 12 Beta 3](https://android-developers.googleblog.com/2021/07/android-12-beta-3.html) 、 [MAD Performance 系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-xjSI-rWn9SViXivBhQUnp)、 [Android 12 Widgets](/@yenerm/updating-your-widget-for-android-12-92e7de87424c) 、 [Fast Pair、](/androiddevelopers/connect-your-android-users-with-a-tap-fast-pair-ce31d486baff) [作曲动画](http://adbackstage.libsyn.com/episode-170-jetpack-compose-graphics-animation)、 [Android 游戏开发包](https://android-developers.googleblog.com/2021/07/introducing-android-game-development-kit.html)、[游戏开发者峰会](https://android-developers.googleblog.com/2021/07/updates-from-google-for-games-developer.html)

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 视频和播客形式的 NiA43

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

谷歌游戏 2021 开发者峰会⛰

上周举行了 [Google for Games 2021 开发者峰会](https://android-developers.googleblog.com/2021/07/updates-from-google-for-games-developer.html)，我们借此机会宣布了一系列新举措，旨在让 Android 上的游戏开发变得更好。对于游戏开发来说，这相当于我们的 Google I/O。

Google for Games Developer Summit

[](https://android-developers.googleblog.com/2021/07/updates-from-google-for-games-developer.html) [## 来自 Google for Games 开发者峰会的更新

### 去年，我们看到 Android 和 Play 达到了新的高度，人们可以在家里安全地玩更多的游戏。通过不断地…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/updates-from-google-for-games-developer.html) 

# 安卓游戏开发套件🛠️

首先，我们推出了 Android 游戏开发工具包(AGDK ),这是一个工具和库的集合，可以让游戏开发更容易、更快、更好。

Introducing the Android Game Development Kit

[](https://android-developers.googleblog.com/2021/07/introducing-android-game-development-kit.html) [## 介绍 Android 游戏开发套件

### 今天，我们将推出 Android 游戏开发套件(AGDK ),这是一整套工具和库，可帮助您开发…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/introducing-android-game-development-kit.html) 

## 游戏开发库

AGDK 包括解决 Android 游戏开发痛点的 [C 和 C++库](https://developer.android.com/games/agdk/libraries-overview)。使用 [GameActivity](https://developer.android.com/games/agdk/integrate-game-activity) 以及我们的[文本输入](https://developer.android.com/games/agdk/add-support-for-text-input)和[游戏控制器](https://developer.android.com/games/sdk/game-controller)库，你可以编写你的游戏循环，而不必接触 JNI 代码。当您将它与我们现有的[双簧管音频库](https://developer.android.com/games/sdk/oboe)和[帧调步库](https://developer.android.com/games/sdk/frame-pacing)相结合时，我们已经构建了高性能的方法来获得您游戏所需的关键服务。

C/C++ libraries for Android games

## 游戏开发扩展

AGDK 的另一部分是针对 Visual Studio 的 [Android 游戏开发扩展，它现在对所有开发者开放。它允许您在现有的 Visual C++游戏项目中添加 Android 作为目标平台。您可以开发 C /C++多平台游戏，部署到 Android 设备或仿真器上并在其上进行调试，所有这些都在一个 IDE 中使用一组项目文件完成。该扩展支持最新版本的 Android SDK 和 NDK，并与我们最流行的工具配合使用，如 Android Studio profilers、logcat、SDK 管理器和虚拟设备管理器。](https://developer.android.com/games/agde)

Android Game Development Extension

[](https://android-developers.googleblog.com/2021/07/android-game-development-extension-is.html) [## Android 游戏开发扩展现在对所有 Android 游戏开发者开放

### 经过一年多的封闭测试后，产品经理莉莉·拉帕波特发布，我们很高兴地宣布，Android…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/android-game-development-extension-is.html) 

## 优化您的游戏

最后，AGDK 包括工具和库来帮助你进一步优化你的游戏。在库的前端， [Android 性能调优器](https://developer.android.com/games/sdk/performance-tuner)提供了用户遥测功能——现在能够跟踪加载时间。 [Android GPU Inspector beta](https://developer.android.com/agi) 现在支持[帧剖析](https://developer.android.com/agi/frame-trace/frame-profiler-trace)，允许你捕捉应用或游戏的单帧，检查 Vulkan API 的整个状态以及来自 GPU 的剖析数据。它还通过使用 ANGLE 在 Vulkan API 上运行 OpenGL 工作负载来支持 OpenGL 中的概要分析。

Android GPU Inspector frame profiling

# 范围和设备📱

我们在 Google Play 控制台中添加了一个[新工具，它可以获取总体安装基数、崩溃率和 ANR 率等指标，并根据有用的属性(如 Android 平台版本、RAM、SoC、OpenGL ES 版本、Vulkan 版本和屏幕指标)对其进行细分。它还包括同行数据和国家级过滤，您甚至可以导出这些数据以用于您自己的分析工具。除了 reach 和 devices，我们还在控制台中添加了新的评级和评论工具。](https://play.google.com/console/about/reachanddevices/)

Reach and devices

[](https://android-developers.googleblog.com/2021/07/plan-for-success-on-google-play-with.html) [## 借助 Reach 和设备在 Google Play 上取得成功

### Google Play 每月有超过 25 亿活跃用户，分布在世界各地，使用许多不同的设备。你怎么…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/plan-for-success-on-google-play-with.html) 

# 游戏用 Android 12🎮

Android 12 包括下载时的[游戏，让你的用户更快地进入游戏。这意味着一些游戏资产不是在安装时下载的，而是在初始安装后在后台下载的。如果你使用 App Bundle，这一切都不需要你对你的游戏做任何改动。](https://developer.android.com/games/distribute/play-as-you-download)

Play as you download

Android 12 还引入了[游戏模式 API](https://developer.android.com/games/gamemode/gamemode-api)，允许游戏玩家选择性能配置文件，如更好的电池寿命或性能模式。API 允许 Android 交流玩家的偏好，这样你就可以相应地调整你的游戏，但系统也可以代表他们干预自动调整游戏的[游戏模式干预](https://developer.android.com/games/gamemode/gamemode-interventions)。你可以通过一个明显的设置选择退出这些干预，我们已经给[提供了一个表格](https://docs.google.com/forms/d/e/1FAIpQLSdCFlU6eRD5Gp8MKhCqKrg4xe9RVR_EibBTHOpktpXROsWxlw/viewform)，所以你可以帮助我们为你的游戏调整这些干预。

# 还有更多…

峰会包括更多的会议，包括顶级手机游戏开发商的[学习](https://www.youtube.com/watch?v=N-gIUIdFtCo)、[为 Chrome OS 优化游戏](https://www.youtube.com/watch?v=CsD2_LSsU0k)，以及[介绍 Play Integrity API](https://www.youtube.com/watch?v=yA3lTAyW4_M) 。这是事件的[完整播放列表。](https://www.youtube.com/watch?v=e9JCRsLjDr4&list=PLWz5rJ2EKKc_ft4KzJnoWiihgFNvs_prV)

# 安卓 12 beta 3🤖

我们发布了 Android 12 的[第三个测试版，其中包括 API 31](https://android-developers.googleblog.com/2021/07/android-12-beta-3.html) 的[最终 API 以及一些新功能。例如，我们引入了一个新的](https://developer.android.com/sdk/api_diff/31/changes) [ScrollCapture API](https://developer.android.com/reference/android/view/ScrollCaptureCallback) 来帮助支持非常受欢迎的滚动屏幕截图功能。

我们还添加了新的[隐私指示器 API](https://developer.android.com/reference/android/view/WindowInsets#getPrivacyIndicatorBounds())到 [WindowInsets](https://developer.android.com/reference/android/view/WindowInsets) ，让你可以获得指示器的最大界限及其在屏幕上的相对位置，以及新的[权限](https://developer.android.com/reference/android/Manifest.permission#REQUEST_COMPANION_START_FOREGROUND_SERVICES_FROM_BACKGROUND)，允许与[配套设备管理器](https://developer.android.com/guide/topics/connectivity/companion-device-pairing)配对的应用程序启动前台服务。

[AppSearch Jetpack 库](https://developer.android.com/jetpack/androidx/releases/appsearch)允许您在 Android 4.0+上以本地存储模式在您的应用内使用 AppSearch，并将在[平台存储模式](https://developer.android.com/guide/topics/search/appsearch#platform-storage)中支持 Android 12+的中央索引，这允许系统在系统 UI 表面和其他应用内显示您的应用的数据。

[](https://android-developers.googleblog.com/2021/07/android-12-beta-3.html) [## Android 12 Beta 3 和最终 API

### 每个月我们都在让 Android 12 更接近它的最终形式，具有创新的功能，一个适应你的新用户界面…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/android-12-beta-3.html) 

我们还发布了 Android 12 for TV 的[测试版，包括](https://android-developers.googleblog.com/2021/07/android-12-beta-3-for-tv-is-now.html)[刷新率切换设置](https://developer.android.com/reference/android/view/Surface?hl=sk#setFrameRate(float,%20int,%20boolean))，更好的显示模式报告，以及对 Android 的隧道模式的更新，减少了 Android 框架中的媒体处理开销。Android TV 现在支持[背景模糊](https://developer.android.com/reference/android/graphics/RenderEffect#createBlurEffect(float,%20float,%20android.graphics.Shader.TileMode))和 [RenderEffect](https://developer.android.com/reference/android/graphics/RenderEffect) 用于应用内模糊和 [WindowManager](https://developer.android.com/reference/android/view/WindowManager.LayoutParams#setBlurBehindRadius(int)) 用于跨窗口模糊，以及官方支持在 4k 渲染 UI。

[](https://android-developers.googleblog.com/2021/07/android-12-beta-3-for-tv-is-now.html) [## 面向电视的 Android 12 Beta 3 现已上市

### 由 Android TV OS 产品经理 Wolfram Klein 发布，伴随着今天的 Android 12 Beta 3 移动版发布，我们…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/07/android-12-beta-3-for-tv-is-now.html) 

# 介绍 MAD 技能:性能⏲️

![](img/13e1e54c5ab45e72be2a9ca561869c95.png)

[MAD Skills](https://developer.android.com/series/mad-skills) 系列继续提供更多关于现代 Android 开发的技术内容。

本周我们将介绍[性能](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-xjSI-rWn9SViXivBhQUnp)，它涵盖了如何使用系统跟踪和采样分析来调试应用程序中的性能问题。

在第一集中，Carmen 关注 Android Studio 中的[系统跟踪剖析](https://developer.android.com/topic/performance/tracing)。系统跟踪允许你获得你的应用程序正在做什么的详细视图，并且在系统的其余部分正在发生什么的上下文中看到它。Carmen 介绍了用户界面，解释了如何在 Android Studio 和设备上收集跟踪信息，并展示了如何在您的应用程序中设置跟踪信息。

但是等等，还有更疯狂的内容！

对于正在进行的内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# AndroidX 释放

[Jetpack Compose](http://goo.gle/compose-docs) 达到了候选发布版本 2 的状态，这意味着我们离发布真的很近了。由于新的 AGDK 库是 Android Jetpack 的一部分，你也会在那里看到它们。

# 文章📰

## 使用 tap: Fast Pair 连接您的 Android 用户

如果你是一个设备制造商或开发特定设备附带的应用程序的开发人员 [Isai Damier](https://medium.com/u/a2d70bdb57?source=post_page-----603ddab2bcc8--------------------------------) 介绍了如何使用[快速配对服务](https://developers.google.com/nearby/fast-pair/spec)来减少你和你的最终用户必须完成的设备配对工作量——除非你想在你的配套应用程序中处理配对，否则这一切都无需 Android 应用程序编码。

[](/androiddevelopers/connect-your-android-users-with-a-tap-fast-pair-ce31d486baff) [## 使用 tap: Fast Pair 连接您的 Android 用户

### 如果您是一个设备制造商或开发人员，正在开发一个特定设备附带的应用程序(例如…

medium.com](/androiddevelopers/connect-your-android-users-with-a-tap-fast-pair-ce31d486baff) 

## 为 Android 12 更新您的 widget

Murat Yener 开始了一个迷你博客系列，为 Android 12 更新你的应用程序的启动器部件。这个[的第一个帖子](/@yenerm/updating-your-widget-for-android-12-92e7de87424c)涵盖了一些简单的改变，让你的小工具在运行 Android 12 的设备上看起来很棒，同时也在旧版本的 Android 上提供了一致的体验。

[](/androiddevelopers/updating-your-widget-for-android-12-92e7de87424c) [## 为 Android 12 更新您的 widget

### 很长一段时间以来，微件一直是 Android 核心体验的一部分，许多应用程序都有效地使用微件来…

medium.com](/androiddevelopers/updating-your-widget-for-android-12-92e7de87424c) 

# 文档更新🆕

我们已经完全修改、重新设计、更新和扩展了我们的[游戏开发者页面](http://developer.android.com/games)，围绕使用[预建的交钥匙游戏引擎](https://developer.android.com/games/engines/engines-overview)的开发者以及使用[定制引擎或定制现有引擎](https://developer.android.com/games/develop/custom/overview)的开发者的信息来组织它们。

此外，我们还推出了一个新家，用于[构建响应式布局](https://developer.android.com/large-screens)，适应手机、平板电脑、可折叠和 Chrome OS 设备。本页汇集了我们的 API 指南、材料设计资源和 codelabs，帮助您入门。

# ADB 播客片段🎧

自从上一期《现在》在安卓发布以来，又有一期新的[安卓开发者后台](http://adbackstage.libsyn.com/)发布。

![](img/d08e3ba8a5db30d141fb3751dfef19cf.png)

ADB 发布了[第 170 集](http://adbackstage.libsyn.com/episode-170-jetpack-compose-graphics-animation)，这是我们关于 [Jetpack Compose](https://developer.android.com/jetpack/compose) 的连续系列的一部分。在这一集里，尼克和切特与刘朵朵和纳德·贾瓦德一起讨论 Compose 的动画和图形系统。他们解释了如何使传统的命令式系统适应声明式世界，并概述了该库提供的高级组件以及您可以下拉以获得更多控制的低级构建块。

# 那么现在…👋

这一次就到此为止，有 [Android 12 Beta 3](https://android-developers.googleblog.com/2021/07/android-12-beta-3.html) ，新的 [MAD Performance 系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-xjSI-rWn9SViXivBhQUnp)， [Android 12 Widgets](/@yenerm/updating-your-widget-for-android-12-92e7de87424c) ， [Fast Pair，](/androiddevelopers/connect-your-android-users-with-a-tap-fast-pair-ce31d486baff) [Compose Animation](http://adbackstage.libsyn.com/episode-170-jetpack-compose-graphics-animation) ，一个新的[home for responsive layouts](https://developer.android.com/large-screens)，以及 [Android 游戏开发套件](https://android-developers.googleblog.com/2021/07/introducing-android-game-development-kit.html)连同[游戏开发者的许多其他新东西](https://android-developers.googleblog.com/2021/07/updates-from-google-for-games-developer.html)。请尽快回到这里，等待 Android 开发者世界的下一次更新。