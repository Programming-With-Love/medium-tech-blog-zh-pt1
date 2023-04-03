# 现在在 Android #46 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-46-f728e796f38?source=collection_archive---------3----------------------->

![](img/8e1a3d4bc0a36c354aa63f103e15cbdd.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 狂技能[性能](https://goo.gle/mad-performance)和[手柄](https://youtu.be/mnMCgjuMJPA)、[窗口管理器](https://link.medium.com/SEHJSWI25ib)、Android 12 [Widgets](https://link.medium.com/4QkjEZG25ib) 、[辅助功能](https://youtu.be/rtyjbUxUmG8)等等！

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 视频和播客形式的 NiA46

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

[](http://nowinandroid.googledevelopers.libsynpro.com/46-mad-skills-hilt-windowmanager-android-12-widgets-and-more) [## 现在在 Android 中:46-MAD 技能刀柄，WindowManager，Android 12 widgets，等等！

### 欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。今天…

nowinandroid.googledevelopers.libsynpro.com](http://nowinandroid.googledevelopers.libsynpro.com/46-mad-skills-hilt-windowmanager-android-12-widgets-and-more) 

# 更好地为用户和开发者评分和评论⭐

Google Play 正在使评分更加个性化，更能体现每个用户所能期待的体验:

*   从 2021 年 11 月起，手机用户将开始看到特定于其注册国家的评级。
*   2022 年初，平板电脑、Chromebooks 和可穿戴设备等其他外形的用户将开始看到针对他们所用设备的评级。

Google Play 还对 Play Console 进行了增强，以帮助开发者了解他们的评级和评论，特别是跨外形因素:设备类型洞察、更灵活的日期和时间段选择，以及轻松下载数据。

[](https://android-developers.googleblog.com/2021/08/making-ratings-and-reviews-better-for.html) [## 为用户和开发者提供更好的评级和评论

### 评级和评论很重要。他们对用户报告的内容提供有价值的定量和定性反馈…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/08/making-ratings-and-reviews-better-for.html) 

# 疯狂技能系列🧰

![](img/a989746501bdfba5c9c687a5321241c6.png)

## 表演⏲️

在通常的现场问答环节中，性能系列已经接近尾声，卡门·杰克森和乔舒亚·吉尔帕特里克将与弗洛里纳·芒特内斯库一起回答性能问题。

如果你错过了其中一集，请查看[表演疯狂技能系列播放列表](https://goo.gle/mad-performance)。

## 希尔特·🗡️

Hilt 是 Jetpack 推荐的 Android 上依赖注入的解决方案，其 MAD 技能系列才刚刚起步！你想知道这个系列是关于什么的吗？观看介绍视频了解更多信息:

剧集前两集已经在这里了！在第一集中，我**介绍了依赖注入(DI)和 Hilt** ，将它与手工依赖注入进行了比较，向您展示了 Hilt 如何通过删除样板 DI 代码来简化您的应用程序。

在第二集， [Eric Chang](https://medium.com/u/b72abd445fe9?source=post_page-----f728e796f38--------------------------------) 谈到测试最佳实践。如何在使用 Hilt 时进行测试，不同的 API，以及一些优化你的构建并最大限度地利用 Hilt 进行测试的技巧和诀窍。

## 更多疯狂的内容

但是等等，还有更疯狂的内容！

对于正在进行的内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# 文章📚

[Meghan Mehta](https://medium.com/u/401951cd4c3e?source=post_page-----f728e796f38--------------------------------) 写了关于 **Block Store** 的文章，这是一个新的 API，它允许你的应用程序存储用户凭证，以后可以检索这些凭证来重新验证新设备上的用户。这篇博文解释了*为什么*、*何时*以及*如何*使用 Block Store。

[](https://link.medium.com/ILuDQCL25ib) [## 通过 Block Store 实现无缝转账

### 新移动设备的魔力。打开脆盒，剥去塑料，揭开无瑕疵的屏幕…

link.medium.com](https://link.medium.com/ILuDQCL25ib) 

[**Jetpack 的 WindowManager**](https://developer.android.com/jetpack/androidx/releases/window) **库已经在内测**！并且 [Pietro Maggi](https://medium.com/u/1810439c8f4b?source=post_page-----f728e796f38--------------------------------) 写了一篇关于库中包含的新 API 以及如何在你的应用中使用它们的博文:

*   `[WindowLayoutInfo](https://developer.android.com/reference/androidx/window/layout/WindowLayoutInfo)`:包含窗口的显示特征，如窗口是否包含折叠或铰链。
*   `[FoldingFeature](https://developer.android.com/reference/androidx/window/layout/FoldingFeature)`:使您能够监控可折叠设备的折叠状态，以确定设备的姿态。
*   `[WindowMetrics](https://developer.android.com/reference/androidx/window/layout/WindowMetrics)`:提供当前窗口指标或整体显示指标。

[](https://link.medium.com/SEHJSWI25ib) [## 解开窗口管理器

### 为可折叠设备和大屏幕设备优化应用程序 Android 中的屏幕尺寸正在快速变化，并且随着…

link.medium.com](https://link.medium.com/SEHJSWI25ib) 

**Android 12**中的小工具得到了惊人的改进。阅读 [Murat Yener](https://medium.com/u/e947fef0dfe0?source=post_page-----f728e796f38--------------------------------) 的博客文章，学习高级功能，使您的小工具更具交互性，更易于配置，并提供更好的用户界面体验。你可能还记得在 Android 12 之前，重新配置一个小部件意味着用户必须删除他们现有的小部件，然后用新的配置重新添加它。嗯…现在已经不是这样了！👏

[](https://link.medium.com/4QkjEZG25ib) [## 在 Android 12 中使用您的 widget 做更多事情！

### 这篇文章是我写的关于为 Android 12 更新你的小部件的系列文章的第二篇。在最后…

link.medium.com](https://link.medium.com/4QkjEZG25ib) 

Duolingo 发现，由于应用架构中的可扩展性问题，他们的性能和开发人员的工作效率下降了。在这篇由 [Kateryna Semenova](https://medium.com/u/b85a51f012d7?source=post_page-----f728e796f38--------------------------------) 撰写的博客文章中，你可以了解他们如何通过重构模型-视图-视图模型架构，并使用 Android Jetpack 的匕首和刀柄进行依赖注入，来解决这些性能问题并重新获得开发人员的生产力。

[](https://android-developers.googleblog.com/2021/08/android-app-excellence-duolingo.html) [## 性能和速度:Duolingo 如何在 Android 上采用 MVVM

### 由于 Android 软件架构中的可扩展性问题，Duolingo 的应用程序开始经历成长的烦恼。他们…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/08/android-app-excellence-duolingo.html) 

**卓越的应用体验对企业非常重要**。 *App excellence* 是我们希望帮助所有开发者实现的目标。Jacob Lehrbaum[的这篇博文讲述了你的公司可以遵循的内部最佳实践，以在你的应用中获得 A+用户体验。免责声明:这可能需要跨职能的方法，工程、设计、产品和业务团队朝着共同的目标努力。](https://medium.com/u/88221b3b3cc1?source=post_page-----f728e796f38--------------------------------)

[](https://android-developers.googleblog.com/2021/08/working-towards-android-app-excellence.html) [## 努力打造卓越的 Android 应用

### 出色的应用体验对企业来说非常重要。事实上，近四分之三的安卓应用用户离开了 5 星…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/08/working-towards-android-app-excellence.html) 

# 辅助功能视频🌐

一个关于 Android 可访问性的新系列视频已经开始了！第一个视频由 [Caren Chang](https://medium.com/u/b6f9dc502595?source=post_page-----f728e796f38--------------------------------) 制作，介绍了演示如何在考虑可访问性的情况下构建 Android 应用的系列。

在第二个视频中， [Shailen Tuli](https://medium.com/u/abe027023236?source=post_page-----f728e796f38--------------------------------) 向您展示了在辅助功能设置中发现的不同的**配置和功能，以及为什么人们可能会使用它们。**

# ADB 播客片段🎧

自从上一集《现在》在安卓发布以来，又有一集新的[安卓开发者后台](http://adbackstage.libsyn.com/)发布。

亚行发布[第 173 集](http://adbackstage.libsyn.com/episode-173-more-benchmarking)覆盖 ***更多标杆*** 。在这一集中，Chet、Romain 和 Tor 与来自工具包性能团队的 Chris Craik 和 Rahul Ravikumar 讨论了最近发布的 [macrobenchmark](https://developer.android.com/studio/profile/macrobenchmark) 工具+库。听听它，了解基准库如何工作，如何使用它们，它们如何与系统跟踪相关，等等。

# 那么现在…👋

这一次就这样，对用户和开发者来说[评级和评论更好](https://android-developers.googleblog.com/2021/08/making-ratings-and-reviews-better-for.html)；关于[表演](https://goo.gle/mad-performance)和[剑柄](https://www.youtube.com/watch?v=mnMCgjuMJPA&list=PLWz5rJ2EKKc_9Qo-RBRYhVmME1iR4oeTK)的狂技系列；关于 [Block Store](https://link.medium.com/ILuDQCL25ib) 、 [WindowManager](https://link.medium.com/SEHJSWI25ib) 、[Android 12](https://link.medium.com/4QkjEZG25ib)、 [Duolingo app 改进](https://android-developers.googleblog.com/2021/08/android-app-excellence-duolingo.html)、 [app 卓越](https://android-developers.googleblog.com/2021/08/working-towards-android-app-excellence.html)的文章；可访问性[视频](https://youtu.be/uG1v_7KA37E)和新的[播客](http://adbackstage.libsyn.com/episode-172-privacy-features-in-android-12)。请尽快回到这里，等待 Android 开发者世界的下一次更新。