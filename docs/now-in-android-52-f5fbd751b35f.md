# 现在在 Android #52 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-52-f5fbd751b35f?source=collection_archive---------4----------------------->

![](img/f6e35e37c73eebb97b21a5ab2fc56ac6.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [分页](https://www.youtube.com/watch?v=Pw-jhS-ucYA&list=PLWz5rJ2EKKc9L-fmWJLhyXrdPi1YKmvqS)、 [Gradle](https://goo.gle/gradle-mad) 、 [AndroidX](https://developer.android.com/jetpack/androidx/versions/all-channel) 、 [Media3](https://developer.android.com/jetpack/androidx/releases/media3) 、 [Emoji2](/androiddevelopers/support-modern-emoji-99f6dea8e57f) 、 [CameraX](https://developer.android.com/training/camerax) 、[无障碍](https://www.youtube.com/watch?v=wWDYIGk0Kdo)、 [App 启动](https://android-developers.googleblog.com/2021/11/improving-app-startup-facebook-app.html)、 [WearOS](https://developer.android.com/codelabs/compose-for-wear-os) 等等！

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 🥳100 万 YouTube 用户

感谢大家关注 Now in Android 系列和 Android 开发者 YouTube 频道提供的一切。在 Android 开发者峰会期间，我们的 YouTube 频道达到了 100 万订阅用户！这里有一个小视频来感谢大家。

# 疯狂技能:分页📑还有格拉德🐘

[分页](https://developer.android.com/topic/libraries/architecture/paging/v3-overview)系列已经结束！在第三集， [TJ](https://tunjid.medium.com/) 展示了可以通过分页执行的不同操作。像插入分隔符、何时创建新的寻呼机以及消费的定制选项等转换。

在接下来的视频中，Android 社区的 Erik Zuo 将与您分享一个分页技巧。

而作为系列的收尾， [TJ](https://tunjid.medium.com/) 和[达斯汀](https://twitter.com/itsdustinlam)在平时的直播 Q & A 回答你的问题。

在**分页**之后，一个新的 MAD 技能系列开始了，这次是关于 Gradle 的！在下面的视频中，[缪拉](/@yenerm)介绍了这个系列以及你将在其中学到的一切。

在第一集中，Murat 解释了 Android 构建系统是如何工作的，以及如何配置你的构建。

在第二集中，你将学习如何通过编写你自己的插件来扩展你的构建。

最后，在第三集中，你将把你的插件向前推进一步，学习如何使用新的[构件](https://developer.android.com/studio/build/extend-agp#variant-api-artifacts-tasks) API 访问各种构建构件。

## 更多疯狂的内容

但是等等！如果这还不够，还有更多疯狂的内容！

对于正在进行的内容，一定要查看 YouTube 上的[疯狂技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# AndroidX 释放🚀

自从上次 Android 中的非 ADS Now 事件以来，很多库被升级为稳定库！比如 [AppCompat](https://developer.android.com/jetpack/androidx/releases/appcompat#1.4.0) 、 [Activity](https://developer.android.com/jetpack/androidx/releases/activity#1.4.0) 和 [Fragment](https://developer.android.com/jetpack/androidx/releases/fragment#1.4.0) 现在都在 1.4.0 中，稳定支持多回栈。支持现代表情符号的表情符号 2 1.0。[生命周期](https://developer.android.com/jetpack/androidx/releases/lifecycle#2.4.0) 2.4，引入了`[repeatOnLifecycle](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=id#(androidx.lifecycle.Lifecycle).repeatOnLifecycle(androidx.lifecycle.Lifecycle.State,%20kotlin.coroutines.SuspendFunction1))`和`[flowWithLifecycle](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=id#(kotlinx.coroutines.flow.Flow).flowWithLifecycle(androidx.lifecycle.Lifecycle,%20androidx.lifecycle.Lifecycle.State))`生命周期感知协同程序 API。[分页](https://developer.android.com/jetpack/androidx/releases/paging#3.1.0) 3.1 给`LoadState`带来了一些行为变化。还有 [Wear tiles](https://developer.android.com/jetpack/androidx/releases/wear-tiles#1.0.0) 你可以用它来为 Wear OS 设备构建定制的 tiles，已经升级到 1.0 了。

所有这些都已经稳定了！去用吧！你可以在这里看到所有的 AndroidX 发行说明[。](https://developer.android.com/jetpack/androidx/versions/all-channel)

## 喷气背包媒体 3

[Jetpack Media3](https://developer.android.com/jetpack/androidx/releases/media3) 库的第一个 alpha 可用。Media3 是一个媒体播放支持库的集合，包括 ExoPlayer。下面的文章解释了团队为什么创建 Media3，它包含什么，以及它如何简化你的应用架构。

[](https://android-developers.googleblog.com/2021/10/jetpack-media3.html) [## Jetpack Media3 简介

### 今天，我们将发布 Jetpack Media3 的第一个 alpha 版本。这是一个媒体播放支持库的集合…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/10/jetpack-media3.html) 

# 文章📚

[梅根](/@magicalmeghan)写到了刚刚稳定下来的新[表情库。阅读更多关于表情符号的问题以及表情符号 2 如何帮助你解决的信息。](https://developer.android.com/jetpack/androidx/releases/emoji2#1.0.0)

[](/androiddevelopers/support-modern-emoji-99f6dea8e57f) [## 支持现代表情符号

### 表情符号！他们无处不在！自从它们发行以来，它们已经成为我们语言的一大部分。它们是一种…

medium.com](/androiddevelopers/support-modern-emoji-99f6dea8e57f) 

我们得到了更多关于 [CameraX](https://developer.android.com/training/camerax) 的文章！这次是关于 CameraX 中的一个新功能，例如，将 CameraX 产生的 YUV 格式转换为用于图像分析功能的 RGB，该功能在 [TensorFlow Lite](https://www.tensorflow.org/lite) 中可用。阅读博客文章，了解更多关于这些格式以及如何使用新的转换功能的信息。

[](/androiddevelopers/convert-yuv-to-rgb-for-camerax-imageanalysis-6c627f3a0292) [## 将 YUV 转换为 RGB 用于 CameraX 图像分析

### CameraX 是一个 Jetpack 支持库，旨在帮助简化相机应用程序的开发。它支持不同的…

medium.com](/androiddevelopers/convert-yuv-to-rgb-for-camerax-imageanalysis-6c627f3a0292) 

缩短应用程序启动时间不是一项简单的任务，需要深入了解影响它的因素。今年，Android 团队和脸书应用团队一直在共同研究指标和分享方法，以改善应用程序的启动。在这篇博客文章中阅读更多的发现。

[](https://android-developers.googleblog.com/2021/11/improving-app-startup-facebook-app.html) [## 改善应用程序启动:来自脸书应用程序的教训

### 由谷歌和脸书团队发布。由谷歌 Android 团队的 Kateryna Semenova 和 Tim Trueman 撰写…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/11/improving-app-startup-facebook-app.html) 

# 无障碍系列🌐

可访问性系列继续介绍如何创建自定义的可访问性操作来使您的应用程序更易访问。您可以为可访问性服务提供一个自定义操作，并实现与该操作相关的逻辑。更多信息，请看下面这一集！

在这另一集中，您可以了解更多关于 [StateDescription](https://developer.android.com/reference/androidx/core/view/ViewCompat#setStateDescription(android.view.View,%20java.lang.CharSequence)) API 的知识，何时使用`stateDescription`和`contentDescription`，以及如何向最终用户表示错误状态。

# 🧑‍新代码实验室💻

如果你对 Wear OS 和 Jetpack Compose 感兴趣，该团队为 Wear OS codelab 发布了一个新的 [Compose。在那里，您可以了解 Wear OS 如何与 Compose 一起工作，有哪些特定于 Wear OS 的组件可用，等等！](https://developer.android.com/codelabs/compose-for-wear-os)

# 亚行播客 Episodes🎙

![](img/56cbf77c8eb643f17b28503340b06b41.png)

自从 Android 上一期 Now 发布以来，已经有一集 [Android 开发者后台](https://adbackstage.libsyn.com/)发布。请点击下面的链接，或在您最喜欢的播客客户端查看:

在 [**第 178 集:主持人 3，嘉宾 0**](https://adbackstage.libsyn.com/episode-178-hosts-3-guests-0) ， [Chet](https://medium.com/u/cb2c4874d3e9?source=post_page-----f5fbd751b35f--------------------------------) ， [Romain](https://medium.com/u/c967b7e51f8b?source=post_page-----f5fbd751b35f--------------------------------) 和 [Tor](https://medium.com/u/8251a5f98c9d?source=post_page-----f5fbd751b35f--------------------------------) 坐下来谈论 Android 开发者峰会，特别是 Android Studio 中的所有新功能，以及其他一些话题，如 Chet 的新 jank 统计库、Android 12L 版本等等。

# 那么现在…👋

这一次就这样了，有[分页](https://www.youtube.com/watch?v=Pw-jhS-ucYA&list=PLWz5rJ2EKKc9L-fmWJLhyXrdPi1YKmvqS)和 [Gradle](https://goo.gle/gradle-mad) MAD 技能系列、 [AndroidX](https://developer.android.com/jetpack/androidx/versions/all-channel) 发布、新的 Jetpack [Media3](https://developer.android.com/jetpack/androidx/releases/media3) 库、关于[表情 2](/androiddevelopers/support-modern-emoji-99f6dea8e57f) 、 [App 启动](https://android-developers.googleblog.com/2021/11/improving-app-startup-facebook-app.html)和 [CameraX](https://developer.android.com/training/camerax) 、[辅助功能](https://www.youtube.com/watch?v=wWDYIGk0Kdo)视频、新的 [Wear OS codelab](https://developer.android.com/codelabs/compose-for-wear-os) 以及

请尽快回到这里，等待 Android 开发者世界的下一次更新。