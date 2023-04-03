# 现在在 Android #69 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-69-f84f27a08e5d?source=collection_archive---------0----------------------->

![](img/5225ab814d1d7aea3671e25d317466f6.png)

Illustration by Virginia Poltrack

## Compose Camp，MAD Skills: Compose，Android Studio Dolphin，AndroidX releases，Jetpack Compose Tracing，Deep Links 和 ADB。

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 第 69 集视频和播客

*现在在安卓系统中*也以视频和播客的形式提供。

# 组成营地

我们推出了 [Compose Camp](http://d.android.com/compose-camp) ，这是一系列面对面的虚拟会议，在这里你可以学习如何与你的同龄人一起使用 Jetpack Compose 构建 Android 应用程序。Compose Camp 有两个方向:初学者方向适合完全的 Android 初学者，包括没有编码经验的人，而有经验的方向适合想要学习如何迁移到 Compose 并停止使用 XML 的 Android 开发人员。与谷歌开发者社区[一起学习](https://developers.google.com/community/gdg)是一种很好的方式，可以与你所在行业的学生和同行联系，一起应对技术挑战，互相学习技能，你可以直接应用到你的项目中。拿起你的“露营装备”,看看你如何能参加一个在你附近的[写作营](http://d.android.com/compose-camp)!

[](https://android-developers.googleblog.com/2022/09/learn-jetpack-compose-at-compose-camp-near-you.html) [## 在您附近的作曲营地学习 Jetpack Compose！

### 如果你是 Android 开发的新手或者刚刚开始编程，请查看初学者教程，在那里…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/09/learn-jetpack-compose-at-compose-camp-near-you.html) 

# 疯狂技能:作曲

说到作曲， [Chris](https://medium.com/u/5e0374fd3b15?source=post_page-----f84f27a08e5d--------------------------------) 在 Compose 上开始了一个全新的 MAD 技能系列。本系列是开始学习如何思考并开始使用 Compose 构建应用程序的好地方。

在第一集里，你将学习如何用 Compose 来改变你的思维。与视图系统不同，使用 Compose 构建可以让您专注于“什么”,而不是“如何”。换句话说，你声明了你希望你的 UI 包含什么，但是你没有一步一步地告诉它如何去做。在本期节目中，了解更多关于如何在写作中思考的信息！

[](/androiddevelopers/thinking-in-compose-c4ef150bb7cf) [## 写作中的思考

### 我们撰写基础系列的这篇文章详细介绍了在撰写中思考意味着什么。因为喷气背包撰写…

medium.com](/androiddevelopers/thinking-in-compose-c4ef150bb7cf) 

下一集将介绍可组合函数，即 Compose 的构建块。您可以通过使用`@Composable`注释来创建可组合的函数。因为 composables 创建起来既快又容易，所以很容易将 UI 组织成一个可重用的组件库。在这一集里学习更多关于如何创建可组合函数的知识！

[](/androiddevelopers/composable-functions-a505ab20b523) [## 可组合函数

### 在上一篇 MAD Skills Compose Basics 文章中，您学习了如何在 Compose 中思考——您用 Kotlin 描述了您的 UI…

medium.com](/androiddevelopers/composable-functions-a505ab20b523) 

# 安卓工作室:海豚

安卓工作室海豚来了！在这篇文章中， [Takeshi](https://medium.com/u/4c602366fd32?source=post_page-----f84f27a08e5d--------------------------------) 讨论了三个关键主题:Jetpack Compose、Wear OS 和开发效率。令人兴奋的功能包括合成动画检查器、Wear OS 模拟器配对助手和 Gradle 管理的虚拟设备。在博客文章或视频中了解所有新功能！

[](https://android-developers.googleblog.com/2022/09/android-studio-dolphin.html) [## 安卓工作室海豚

### 由 Android 产品经理 Yuri Blaise 发布，Android Studio 团队深入研究了如何使其更容易制作…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/09/android-studio-dolphin.html) 

# AndroidX 释放🚀

最近在 [AndroidX](https://developer.android.com/jetpack/androidx/versions) 发布了一堆有趣的东西。值得注意的是，[注释版本 1.5.0](https://developer.android.com/jetpack/androidx/releases/annotation#annotation-1.5.0) 变得稳定并完全移植到 Kotlin 源代码，从而支持 Kotlin 特定的目标使用站点和其他 Kotlin 兼容的注释特性。[活动版本 1.6.0](https://developer.android.com/jetpack/androidx/releases/activity#1.6.0) 和[片段版本 1.5.3](https://developer.android.com/jetpack/androidx/releases/fragment#1.5.3) 也趋于稳定， [DrawerLayout 版本 1.2.0](https://developer.android.com/jetpack/androidx/releases/drawerlayout#1.2.0-alpha01) 在 alpha01， [RecyclerView 版本 1.3.0](https://developer.android.com/jetpack/androidx/releases/recyclerview#recyclerview-1.3.0-rc01) 在 rc01。

# 文章📚

[Ben](https://medium.com/u/84718b19bc40?source=post_page-----f84f27a08e5d--------------------------------) 介绍了 Compose Composition Tracing，这是一个允许在 Android Studio Flamingo 系统跟踪分析器中显示 Jetpack Compose 可组合函数的新功能。这个特性为您提供了系统跟踪的低侵入性，方法跟踪组合中的细节层次。这对于检查您的 Compose 应用程序的性能并找出您的应用程序可能无法如您所愿执行的原因非常有用。在文章中了解更多关于这个特性的信息！

[](/androiddevelopers/jetpack-compose-composition-tracing-9ec2b3aea535) [## Jetpack 撰写撰写跟踪

### 今天，我们推出了第一个 alpha 合成合成跟踪，一个允许显示 Jetpack 的新功能…

medium.com](/androiddevelopers/jetpack-compose-composition-tracing-9ec2b3aea535) 

深度链接速成班继续进行 [Summers](https://medium.com/u/129892725592?source=post_page-----f84f27a08e5d--------------------------------) 写一篇关于深度链接故障排除的文章。他回顾了深层链接可能出现的常见问题以及如何解决这些问题。

[](/androiddevelopers/deep-links-crash-course-part-3-troubleshooting-your-deep-links-61329fecb93) [## 深层链接速成班:第 3 部分深层链接故障排除

### 这篇博文将带你调试使用 adb 的应用程序中的深层链接，检查你的数字资产链接…

medium.com](/androiddevelopers/deep-links-crash-course-part-3-troubleshooting-your-deep-links-61329fecb93) 

# ADB 播客片段

自从上次在
安卓发布以来，已经有一集[安卓开发者后台](https://adbackstage.libsyn.com/)。请点击下面的链接，或在您最喜欢的播客客户端查看:

在第 189 集:[视频会议](https://adbackstage.libsyn.com/episode-189-video-conference)， [Tor](https://medium.com/u/8251a5f98c9d?source=post_page-----f84f27a08e5d--------------------------------) 和 [Chet](https://medium.com/u/cb2c4874d3e9?source=post_page-----f84f27a08e5d--------------------------------) 与来自 Android 媒体团队的 [Marc](https://medium.com/u/27823e454ddd?source=post_page-----f84f27a08e5d--------------------------------) ， [Toni](https://medium.com/u/d994f3760649?source=post_page-----f84f27a08e5d--------------------------------) 和 [Andrew](https://medium.com/u/25af5ba1a589?source=post_page-----f84f27a08e5d--------------------------------) 交谈，他们在那里从事视频技术和 API 工作，如 ExoPlayer。我们讨论了 ExoPlayer 和平台媒体功能的发展，以及正在进行的和不久的将来的功能。

# 那么现在…👋

这就是本周的[作曲营](https://android-developers.googleblog.com/2022/09/learn-jetpack-compose-at-compose-camp-near-you.html)、[狂技能:作曲](https://www.youtube.com/watch?v=4UXJTeb9Khg&list=PLWz5rJ2EKKc-CG9riunK996aI6cRhXFDC)、[安卓工作室海豚](https://android-developers.googleblog.com/2022/09/android-studio-dolphin.html)、 [AndroidX 发布](https://developer.android.com/jetpack/androidx/versions)、 [Jetpack 作曲追踪](/androiddevelopers/jetpack-compose-composition-tracing-9ec2b3aea535)、[深度链接](/androiddevelopers/deep-links-crash-course-part-3-troubleshooting-your-deep-links-61329fecb93)，以及新一集 [ADB](https://adbackstage.libsyn.com/) 。

请尽快回到这里，等待 Android 开发者世界的下一次更新。