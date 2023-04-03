# 现在在 Android #48 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-48-c499493bb83?source=collection_archive---------5----------------------->

![](img/1b9afa19dd4b883cbc794e328bcac1b1.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [安卓开发者峰会](https://developer.android.com/dev-summit)、[安卓 12](https://android-developers.googleblog.com/2021/10/android-12-is-live-in-aosp.html) 、[手柄](https://www.youtube.com/watch?v=mnMCgjuMJPA&list=PLWz5rJ2EKKc_9Qo-RBRYhVmME1iR4oeTK)、[分页](https://www.youtube.com/watch?v=Pw-jhS-ucYA&list=PLWz5rJ2EKKc9L-fmWJLhyXrdPi1YKmvqS)、[游戏控制台用户管理](https://android-developers.googleblog.com/2021/09/improved-google-play-console-user.html)、[权限](https://android-developers.googleblog.com/2021/09/making-permissions-auto-reset-available.html)、 [WearOS](https://android-developers.googleblog.com/2021/09/wear-os-jetpack-libraries-now-in-stable.html) 、[无障碍](https://www.youtube.com/watch?v=rtyjbUxUmG8&list=PLWz5rJ2EKKc8OENfLdh3zM5T6IRdlVYKj)、[安卓基础](https://developer.android.com/courses/android-basics-kotlin/course)

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

Now in Android videos provided as a courtesy for those who prefer watching to reading

# Android 开发峰会将于 2021 年 10 月 27-28 日回归！📆

加入我们 10 月 27 日至 28 日举行的[2021 年 Android 开发峰会](https://developer.android.com/dev-summit)！该展会将于太平洋时间 10 月 27 日上午 10 点以 Android Show 拉开帷幕:这是一个技术主题演讲，您将在这里听到所有最新的开发者新闻和更新。在那里，我们有超过 30 场关于一系列技术 Android 开发主题的会议，我们将现场回答您的#AskAndroid 问题。

[](https://android-developers.googleblog.com/2021/09/android-dev-summit.html) [## Android Dev 峰会于 2021 年 10 月 27-28 日回归！

### Android 开发峰会回来了！几周后，10 月 27 日至 28 日加入我们，了解 Android 的最新更新…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/09/android-dev-summit.html) 

# Android 12 在 AOSP 上线了！🤖

我们[发布了 Android 12](https://android-developers.googleblog.com/2021/10/android-12-is-live-in-aosp.html) ，推送给了 [Android 开源项目](https://source.android.com/) (AOSP)。它将在今年晚些时候出现在设备上。感谢您在测试期间的反馈。

Android 12 引入了名为 [Material You](https://material.io/blog/announcing-material-you) 的新设计语言，以及[重新设计的小工具](https://developer.android.com/about/versions/12/features#widgets)、[通知 UI 更新](http://developer.android.com/about/versions/12/behavior-changes-12#custom-notifications)、[拉伸滚动](https://developer.android.com/about/versions/12/overscroll)，以及[应用启动闪屏](https://developer.android.com/guide/topics/ui/splash-screen)。我们减少了核心系统服务使用的 CPU 时间，增加了[性能等级设备功能](https://developer.android.com/about/versions/12/features/performance-class)，使 ML 加速器驱动程序在平台版本之外可更新，并防止应用程序从后台启动前台服务和使用[通知蹦床](https://developer.android.com/about/versions/12/behavior-changes-12#notification-trampolines)来提高性能。新的[隐私仪表盘](https://developer.android.com/about/versions/12/features#privacy-dashboard)、[大概位置](https://developer.android.com/about/versions/12/behavior-changes-12#approximate-location)、[麦克风和摄像头指示器](https://developer.android.com/about/versions/12/behavior-changes-all#mic-camera-indicators) / [切换](https://developer.android.com/about/versions/12/behavior-changes-all#mic-camera-toggles)，以及[附近设备权限](https://developer.android.com/about/versions/12/features/bluetooth-permissions)让用户对隐私有更多的洞察和控制。我们通过用于丰富内容插入的[统一 API](https://developer.android.com/about/versions/12/features/unified-content-api)、[兼容媒体转码](https://developer.android.com/about/versions/12/features/compatible-media-transcoding)、更简单的[模糊和效果](https://developer.android.com/reference/android/graphics/RenderEffect)、AVIF 图像支持、[增强的触觉](https://developer.android.com/about/versions/12/features#haptics)、新的[相机效果/功能](https://developer.android.com/about/versions/12/features#camera-sensor-support)、[改进的本机崩溃调试](https://developer.android.com/reference/kotlin/android/app/ActivityManager#gethistoricalprocessexitreasons)、[对圆角屏幕的支持](https://developer.android.com/about/versions/12/features#rounded_corner_apis)、[下载时播放](https://developer.android.com/games/distribute/play-as-you-download)和[游戏模式 API 来改善用户体验](https://developer.android.com/games/gamemode)

[](https://android-developers.googleblog.com/2021/10/android-12-is-live-in-aosp.html) [## Android 12 在 AOSP 上线了！

### 今天我们将把源代码推向 Android 开源项目(AOSP ),并正式发布最新版本的…

Android-developers . Google blog . COMT](https://android-developers.googleblog.com/2021/10/android-12-is-live-in-aosp.html) 

# **kot Lin 的 Android 基础第 6 单元上线啦！🎉**

为 Kotlin 中的 [Android 基础设计的](https://developer.android.com/courses/android-basics-kotlin/course)[最终单元](https://developer.android.com/courses/android-basics-kotlin/unit-6)已经上线，该单元涵盖了使用 Android Jetpack 的 [WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) API。Kotlin 中的 Android 基础知识指导学生学习用 Kotlin 编程，同时学习构建 Android 应用程序，最终开发一系列应用程序来开始 Android 开发者之旅。

你可以在这里看到全部课程[。](https://developer.android.com/courses/android-basics-kotlin/course)

# 增强了 Google Play 控制台🧑‍的用户管理💼

[游戏控制台](http://play.google.com/console)中的[用户和权限工具](https://android-developers.googleblog.com/2021/09/improved-google-play-console-user.html)拥有一个新的、不混乱的界面和新的团队管理功能，可以更容易地确保每个团队成员拥有正确的权限集来履行他们的职责，而不会过度暴露无关的业务数据。

我们重新编写了权限名称和描述，阐明了帐户和应用程序级别权限之间的区别，添加了新的搜索、过滤和批量编辑功能，并添加了将这些信息导出到 CSV 文件的功能。此外，Play Console 用户可以在有正当理由的情况下请求访问操作，并且我们引入了权限组，以便更容易地一次向共享相同或相似角色的用户分配多个权限。

[](https://android-developers.googleblog.com/2021/09/improved-google-play-console-user.html) [## 改进的 Google Play 控制台用户管理:访问请求、权限组等等

### 用户管理是各种规模企业的重要职责。挑战在于确保每一个…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/09/improved-google-play-console-user.html) 

# 让数十亿台设备可以使用权限自动重置🔐

Android 11 引入了[权限自动重置](https://developer.android.com/about/versions/11/privacy/permissions#auto-reset)，当一个应用程序几个月不用时，自动重置其[运行时权限](https://developer.android.com/guide/topics/permissions/overview#runtime)。在【2021 年 12 月，我们开始向运行安卓 6.0 (API 等级 23)或更高版本的 [Google Play 服务](https://developers.google.com/android)的设备推出这一功能，用于面向安卓 11 (API 等级 30)或更高版本的应用。用户可以手动启用针对 API 级别 23 到 29 的应用程序的权限自动重置。

一些应用和权限自动免于撤销，如企业使用的活动设备管理员应用，以及由企业策略固定的权限。如果你的应用程序主要在后台工作，没有用户交互，你可以[要求用户](https://developer.android.com/training/permissions/requesting#request-disable-auto-reset)阻止系统重置你的应用程序的权限。

[](https://android-developers.googleblog.com/2021/09/making-permissions-auto-reset-available.html) [## 让数十亿台设备可以使用权限自动重置

### 供稿人:Inara Ramji，软件工程师；罗德里戈·法瑞尔，交互设计师；产品经理詹姆斯·凯利；亨利…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/09/making-permissions-auto-reset-available.html) 

# 佩戴操作系统喷气背包图书馆现在在 stable⌚

五个核心 Android Jetpack Wear 操作系统库[现已稳定](https://android-developers.googleblog.com/2021/09/wear-os-jetpack-libraries-now-in-stable.html)，帮助您遵循最佳实践，减少样板文件，并为您的用户创建高效、简洁的体验。Android Jetpack Wear OS 库包含您在旧的可穿戴支持库中已经习惯的所有熟悉功能，并对 Wear OS 3.0 提供了更好的支持。

我们强烈建议您将 Wear OS 应用程序中的库从 Wear Support 库迁移到它们的 AndroidX 对等库，因为我们在 stable 中提供了它们。

[](https://android-developers.googleblog.com/2021/09/wear-os-jetpack-libraries-now-in-stable.html) [## 穿戴操作系统喷气背包库现在稳定！

### 为了帮助您开发高质量的 Wear OS 应用程序，我们一直忙于更新 Android Jetpack Wear OS…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/09/wear-os-jetpack-libraries-now-in-stable.html) 

# 疯狂技能:剑柄和翻页💡

![](img/1fd29515d7be968574016f39ba574778.png)

[MAD Skills](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7) 系列继续滚动，关于现代 Android 开发的技术内容。

## 希尔特·🗡️

[狂技能系列](https://www.youtube.com/watch?v=mnMCgjuMJPA&list=PLWz5rJ2EKKc_9Qo-RBRYhVmME1iR4oeTK)上[手柄](https://developer.android.com/training/dependency-injection/hilt-android)， [Android Jetpack](https://developer.android.com/jetpack?gclid=Cj0KCQjw1ouKBhC5ARIsAHXNMI83rl2T3YBGB_ziRvfF7NocjVhQEAtlzqjKHVnImge9Fx8p3kBA1v8aAnsBEALw_wcB&gclsrc=aw.ds) 的依赖注入推荐方案，以一个现场 Q & A .看录音[这里](https://www.youtube.com/watch?v=i27aNF-kYR4)。

MAD Skills: HIlt — Live Q&A (Recorded)

## 分页📄

Android Jetpack 分页库现在是版本 3。分页旨在帮助您在应用程序中处理来自本地存储或通过网络的大型数据集，从而使您的应用程序能够更高效地使用网络带宽和系统资源。TJ 推出了一个新的[疯狂技能系列](https://www.youtube.com/watch?v=Pw-jhS-ucYA&list=PLWz5rJ2EKKc9L-fmWJLhyXrdPi1YKmvqS)来覆盖它，从[这个介绍](https://www.youtube.com/watch?v=Pw-jhS-ucYA)开始。

MAD Skills: Paging — Series Introduction

第一集讲述了如何开始将分页与来自网络数据源的内容整合到您的应用程序中。它涵盖了如何实现一个`[PagingSource](https://developer.android.com/reference/kotlin/androidx/paging/PagingSource)`，创建一个`[Pager](https://developer.android.com/reference/kotlin/androidx/paging/Pager)`，并从页面暴露一个`[PagingData](https://developer.android.com/reference/kotlin/androidx/paging/PagingData)`流。

MAD Skills: Paging — Introduction to Paging

未来几集将讨论如何在 UI 中使用分页数据，以及如何处理来自两个不同来源的分页数据——网络和数据库缓存。

对于正在进行的内容，一定要查看 YouTube 上的[疯狂技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# 无障碍系列🌐

[可访问性系列](https://www.youtube.com/watch?v=rtyjbUxUmG8&list=PLWz5rJ2EKKc8OENfLdh3zM5T6IRdlVYKj)继续介绍如何遵循基本的可访问性原则，以确保你的应用能被尽可能多的用户使用。

[色彩对比](https://developer.android.com/guide/topics/ui/accessibility/apps#text-visibility)不仅可以帮助有视觉障碍的用户使用你的应用，它还可以帮助每个人在户外阳光下使用你的应用。较小的文本比较大的文本需要更大的对比度，视频涵盖了最佳实践以及如何测试您的应用程序，以确保它对最大数量的用户来说是易读的。

Android Accessibility: Color Contrast

视图需要为 TalkBack 的用户提供上下文，以避免他们被宣布为编辑框。该视频介绍了两种不同的方法来避免这个问题:为独立的 EditText 视图添加一个`[android:hint](https://developer.android.com/reference/android/widget/TextView#attr_android:hint)`属性，为每个用作标签的`[TextView](https://developer.android.com/reference/android/widget/TextView)`添加一个`[android:labelFor](https://developer.android.com/reference/android/R.attr#labelFor)`属性。两者都有助于 [TalkBack](https://developer.android.com/guide/topics/ui/accessibility/testing#talkback) 为大幅改善的用户体验提供适当的环境。

Android Accessibility: EditTexts

此外，我们还添加了一个[新的 codelab](https://developer.android.com/codelabs/jetpack-compose-accessibility) ，它涵盖了 Jetpack Compose 中的可访问性最佳实践，例如触摸目标大小、内容描述、点击标签等等。

想要更多的可访问性吗？你很幸运，看看这个全新的[学习途径](https://developer.android.com/courses/pathways/make-your-android-app-accessible)，旨在教你如何让你的应用程序更易访问。

# 文章📚

[Manuel](https://medium.com/u/3b5622dd813c?source=post_page-----c499493bb83--------------------------------) 讲述 [Headspace 的 app 卓越之旅](https://android-developers.googleblog.com/2021/09/investing-in-app-excellence-headspaces.html)；他们花了八个月的时间重构模型-视图-视图模型[架构](https://developer.android.com/jetpack/guide)，用 [Kotlin](https://developer.android.com/kotlin) 重写，并将他们的测试覆盖率从 15%提高到 80%。从 Q2 到 2020 年第四季度，改善的应用体验使月活跃用户增加了 15%，评论得分从 3.5 增加到 4.7。

[](https://android-developers.googleblog.com/2021/09/investing-in-app-excellence-headspaces.html) [## 专注的架构:Headspace 的规模重构

### 撰稿人:Mauricio Vergara，产品营销经理，开发商营销，Marialaura Garcia，助理产品…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/09/investing-in-app-excellence-headspaces.html) 

[Maru](https://medium.com/u/6b5449aa0c3c?source=post_page-----c499493bb83--------------------------------) 继续我们的 app excellence 系列，涵盖卓越绩效的各个方面:

*   稳定性
*   快速加载时间
*   平滑渲染
*   电池经济性
*   最新的 SDK(针对安全性和性能！)

[](https://android-developers.googleblog.com/2021/09/app-performance-to-drive-app-excellence.html) [## 推动应用卓越的应用性能

### 在本系列的上一篇博文中，我们将 app excellence 定义为“创建一个能够提供一致的……

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/09/app-performance-to-drive-app-excellence.html) 

从 [Android 12](https://developer.android.com/about/versions/12) 开始，引入了前台服务启动限制。这些限制可能会使您的应用程序处于调用 setForeground 可能会导致异常的状态。

[Caren](https://medium.com/u/b6f9dc502595?source=post_page-----c499493bb83--------------------------------)

[](/androiddevelopers/using-workmanager-on-android-12-f7d483ca0ecb) [## 在 Android 12 上使用工作管理器

### 在 WorkManager 2.6 中，我们实现了工作人员在任何指定进程中运行的能力，并且截至今天…

medium.com](/androiddevelopers/using-workmanager-on-android-12-f7d483ca0ecb) 

韦恩[回答了](https://android-developers.googleblog.com/2021/10/answering-your-top-questions-on-android.html)自从 7 月份推出 [Android 游戏开发套件](https://developer.android.com/games/agdk) (AGDK)以来，我们收到的一些顶级游戏开发问题，包括 AGDK [库](https://developer.android.com/games/agdk/libraries-overview)和工具，优化 Android 中的内存，即将推出的[游戏模式 API](https://developer.android.com/games/gamemode/gamemode-api) ，以及使用图形 API 来针对 GPU。

[](https://android-developers.googleblog.com/2021/10/answering-your-top-questions-on-android.html) [## 回答关于 Android 游戏开发套件的常见问题

### 我们在 7 月推出了 Android 游戏开发工具包(AGDK ),并收集了一些来自开发者的热门问题…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/10/answering-your-top-questions-on-android.html) 

# AndroidX 释放📚

本周 AndroidX 库中没有太多值得注意的变化；我们做了一些小的修正和更新来支持 Kotlin 1.5.30。 [CameraX 1.1.0-alpha 09](https://developer.android.com/jetpack/androidx/releases/camera#1.1.0-alpha09) 极大地提高了我们 RGBA 输出的效率，我们还更新了相关的 [CameraX 分析文档](https://developer.android.com/training/camerax/analyze)和 [CameraX TFLite 示例](https://github.com/android/camera-samples/tree/main/CameraXTfLite)。

# 文档更新🏫

说到文档更新，我们在[安全设计](https://developer.android.com/design-for-safety)中整合了隐私和安全资源。该页面包括最佳实践和资源，可帮助您设计和实现安全、可靠和私密的应用程序。

# 亚行播客 Episodes🎙

![](img/f42a0aba11d63742f92b2c20ca47f84e.png)

自从 Android 上一期 Now 发布以来，已经有一集 [Android 开发者后台](https://adbackstage.libsyn.com/)发布了。请点击下面的链接，或在您最喜欢的播客客户端查看:

[第 176 集:Android 12 — S 代表系统 UI](https://adbackstage.libsyn.com/episode-176-android-12-s-stands-for-system-ui)

在这一集中，[切特](https://medium.com/u/cb2c4874d3e9?source=post_page-----c499493bb83--------------------------------)、[罗曼](https://medium.com/u/c967b7e51f8b?source=post_page-----c499493bb83--------------------------------)和[托尔](https://medium.com/u/8251a5f98c9d?source=post_page-----c499493bb83--------------------------------)与来自 Android 系统 UI 团队的塞利姆、瓦迪姆和卢卡斯聊天，讨论 Android 12 用户界面的许多新功能。

# 那么现在…

这一次到此为止，随着 [Android Dev Summit 2021](https://developer.android.com/dev-summit) 、 [Android 12](https://android-developers.googleblog.com/2021/10/android-12-is-live-in-aosp.html) 、[手柄](https://www.youtube.com/watch?v=mnMCgjuMJPA&list=PLWz5rJ2EKKc_9Qo-RBRYhVmME1iR4oeTK)和[分页](https://www.youtube.com/watch?v=Pw-jhS-ucYA&list=PLWz5rJ2EKKc9L-fmWJLhyXrdPi1YKmvqS) [MAD 技能系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)、[Kotlin 中的 Android 基础知识](https://developer.android.com/courses/android-basics-kotlin/course)、 [Play 控制台用户管理更新](https://android-developers.googleblog.com/2021/09/improved-google-play-console-user.html)、[权限自动重置到旧设备](https://android-developers.googleblog.com/2021/09/making-permissions-auto-reset-available.html)、 [WearOS Jetpack 库](https://android-developers.googleblog.com/2021/09/wear-os-jetpack-libraries-now-in-stable.html)，更多我们的[可访问性](https://www.youtube.com/watch?v=rtyjbUxUmG8&list=PLWz5rJ2EKKc8OENfLdh3zM5T6IRdlVYKj) [Android 12 工作管理器](/androiddevelopers/using-workmanager-on-android-12-f7d483ca0ecb)和[系统 UI](https://adbackstage.libsyn.com/episode-176-android-12-s-stands-for-system-ui) ， [CameraX 分析更新](https://developer.android.com/training/camerax/analyze)，以及新的[安全设计](https://developer.android.com/design-for-safety)部分[developer.android.com](https://developer.android.com/)。 请尽快回到这里，等待 Android 开发者世界的下一次更新。