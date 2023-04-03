# 现在在 Android #30 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-30-5396897fa946?source=collection_archive---------0----------------------->

![](img/0256e05f78d86749a4611e739ef85164.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 应用捆绑包、材料设计组件、新的目标 API 要求、新的片段和流程文档，以及一些文章和视频

欢迎来到 Android 中的[，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。](https://medium.com/androiddevelopers/tagged/now-in-android)

# 视频和播客形式的 NiA30

现在安卓系统中的《T2》也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 录像

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

# MAD 技能:应用捆绑包和材料设计组件

![](img/fc20bc88829b723d4fbcdb33c81d013a.png)

[MAD Skills](https://developer.android.com/series/mad-skills) 系列继续滚动，提供关于现代 Android 开发的新技术内容。

应用捆绑包上的[系列以](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9RJo0uMB_Di3xZ_ZLeau-D)[来自谷歌开发专家 Angelica Oliveira](https://www.youtube.com/watch?v=c5o1bN5_vos&list=PLWz5rJ2EKKc9RJo0uMB_Di3xZ_ZLeau-D&index=6) 的提示结束，然后是[现场+录制的 Q & A](https://www.youtube.com/watch?v=fnsxImWDxj0&list=PLWz5rJ2EKKc9RJo0uMB_Di3xZ_ZLeau-D&index=7) 与我(询问问题)加上 [Ben Weiss](https://medium.com/u/65fe4f480b1c?source=post_page-----5396897fa946--------------------------------) 、 [Wojtek Kaliciński](https://medium.com/u/b913acc64439?source=post_page-----5396897fa946--------------------------------) 和 Iurii Makhno(提供 A 的)。您可以在总结博客中找到所有 App Bundles 剧集(以视频和文章形式)的链接:

[](https://android-developers.googleblog.com/2020/11/mad-skills-become-android-app-bundle.html) [## 疯狂技能——成为安卓应用捆绑包专家

### 现代 Android 开发的 Android App Bundle 系列刚刚结束。我们以现场问答结束…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/11/mad-skills-become-android-app-bundle.html) 

上周，MAD Skills 继续推出关于[材料设计组件](http://goo.gle/mdc)的新系列，这是一个使用[材料设计指南](https://material.io/)简化建筑应用的库。

首先是尼克·布彻(Nick Butcher)的一集，讲述了我们为什么建议 Android 开发者使用材料设计组件。该视频概述了 MDC 提供的各种功能，包括主题支持、内置过渡和默认材质风格的组件:

这一内容在之前的文章中也有涉及:

[](/androiddevelopers/we-recommend-material-design-components-81e6d165c2dd) [## 我们推荐材料设计组件

### 原因如下

medium.com](/androiddevelopers/we-recommend-material-design-components-81e6d165c2dd) 

接下来， [Nick Rout](https://medium.com/u/37290b859aca?source=post_page-----5396897fa946--------------------------------) 发布了一个关于材质主题化的小插曲，通过[materialsthemebuilder 示例项目](https://github.com/material-components/material-components-android-examples/tree/develop/MaterialThemeBuilder)向你展示如何使用和定制材质主题:

除了视频之外，您还可以查看最近关于 MDC 主题化的文章，包括[颜色](/androiddevelopers/material-theming-with-mdc-color-860dbba8ce2f)、[版式](/androiddevelopers/material-theming-with-mdc-type-8c2013430247)和[形状](/androiddevelopers/material-theming-with-mdc-shape-126c4e5cd7b4)。

本周， [Chris Banes](https://medium.com/u/9303277cb6db?source=post_page-----5396897fa946--------------------------------) 发布了第三集，通过使用 Android 10 的 [Force Dark](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme#force_dark) 功能和 MDC 的 DayNight 主题，用 MDC 创建了一个黑暗主题。

[Chris](https://medium.com/u/9303277cb6db?source=post_page-----5396897fa946--------------------------------) 最近也以文章形式发表了这个内容:

[](/androiddevelopers/dark-theme-with-mdc-4c6fc357d956) [## MDC 的黑暗主题

### 使用材料设计组件实现黑暗主题

medium.com](/androiddevelopers/dark-theme-with-mdc-4c6fc357d956) 

本周我们将有更多的 MDC 内容登陆，下周四还有一场现场问答。详情请继续关注 [MDC 播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8SmtMNw34wvYkqj45rV1d3)。

对于正在进行的 MAD 内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者[这个指向所有内容的便捷登陆页面](https://developer.android.com/series/mad-skills)。

# 应用捆绑包和目标 API 要求

2021 年末，将会出现对目标 API(针对新的和更新的应用)和应用捆绑包的需求。海林在博客上公布了所有细节。简单来说:

【2021 年 8 月:

*   新的应用程序需要达到 API 等级 30。
*   新应用将需要使用应用捆绑包来发布到 Play Store。
*   资产或功能超过 150MB 的新应用程序需要通过[播放资产交付](https://developer.android.com/guide/app-bundle/asset-delivery)和/或[播放功能交付](https://developer.android.com/guide/app-bundle/play-feature-delivery)进行交付。新应用将不再支持扩展文件(OBB)。

**2021 年 11 月:**

*   应用程序更新将需要针对 API 级别 30。

[](https://android-developers.googleblog.com/2020/11/new-android-app-bundle-and-target-api.html) [## 2021 年新的 Android 应用捆绑包和目标 API 级别要求

### 2021 年，我们将继续我们的年度目标 API 级别更新，要求新应用程序的目标 API 级别为 30 (Android…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/11/new-android-app-bundle-and-target-api.html) 

# 证明文件

## 零散的文档

片段为 UI 开发人员提供了一个重要的架构元素，允许您以自包含的方式管理应用程序 UI 的较小部分。无论你是在片段中使用[导航](https://developer.android.com/guide/navigation)还是单独使用片段，知道如何在你的应用程序中最好地使用它们是很好的。我们知道全面、最新的文档对于理解如何使用工具和 API 是多么重要。虽然不赞成使用的 API 告诉您应该避免什么，但是文档应该为您指出正确的方向并解释最佳实践。

因此，团队已经对片段文档进行了实质性的重写，为片段的各个方面，包括生命周期、状态、测试等等，提供了更加清晰和最新的指导。点击此处查看最新文档(包括底部链接的小节):

[](https://developer.android.com/guide/fragments) [## 碎片|安卓开发者

### 表示应用程序用户界面的可重用部分。一个片段定义和管理它自己的布局，有它自己的生命周期…

developer.android.com](https://developer.android.com/guide/fragments) 

[伊恩·莱克](https://medium.com/u/51a4f24f5367?source=post_page-----5396897fa946--------------------------------)，他一直在修复和增强 AndroidX 中的片段，在他的 [twitter feed](https://twitter.com/ianhlake/status/1331682563547557888) 中注释了这些文档变化。

## 科特林流

还有一套全新的关于 Kotlin flow 的文档，包含了从使用 flow 的基础到测试再到新的`StateFlow`和`SharedFlow`API 的所有信息。也一定要查看关于使用流量的[视频(我将在下面谈到)。](https://www.youtube.com/watch?v=emk9_tVVLcc)

[](https://developer.android.com/kotlin/flow) [## 科特林流程|安卓开发者

### 在协同例程中，流是一种可以顺序发出多个值的类型，这与返回…

developer.android.com](https://developer.android.com/kotlin/flow) 

# 文章和视频

## 测试启动性能

上周我发表了一篇关于如何自动化应用程序启动性能的文章。我一直在研究一般的启动性能，并希望找到一种合理的、自动化的方法来推导多次连续运行的启动持续时间。我为任何对启动性能测试感兴趣的人发布了我的方法。

[](/androiddevelopers/testing-app-startup-performance-36169c27ee55) [## 测试应用启动性能

### 测试发射性能可能很棘手，但也不一定如此

medium.com](/androiddevelopers/testing-app-startup-performance-36169c27ee55) 

## 匕首->刀柄

在他的文章《从匕首到剑柄》中，曼纽尔·维沃提出了一个问题:“值得吗？”(剧透预警:“很可能……但要看你的情况。”)

本文涵盖了考虑迁移的一些重要原因，包括测试 API、一致性以及与 AndroidX 扩展的集成。

[](/androiddevelopers/migrating-from-dagger-to-hilt-is-it-worth-it-4cbbc8c93e33) [## 从匕首到刀柄的迁移——值得吗？

### 以下是您的团队是否应该投资从匕首到刀柄的迁移的一些原因。

medium.com](/androiddevelopers/migrating-from-dagger-to-hilt-is-it-worth-it-4cbbc8c93e33) 

## Hilt 入门

说到 Hilt， [Filip Stanis](https://medium.com/u/cdfde7c9672b?source=post_page-----5396897fa946--------------------------------) 发表这篇文章是为了帮助开发者开始使用 Hilt，即使是那些以前没有依赖注入或 Dagger 经验的人。所以，如果这一切对你来说都是新的，那么继续读下去。

虽然标题暗示了这篇文章是为 Kotlin 开发人员编写的，但这实际上是关于文章中的代码片段。本文中的一般方法和技术也适用于使用 Java 编程语言的开发人员。

[](/androiddevelopers/a-pragmatic-guide-to-hilt-with-kotlin-a76859c324a1) [## 实用指南剑柄与科特林

### 在 Android 应用中使用依赖注入的简单方法

medium.com](/androiddevelopers/a-pragmatic-guide-to-hilt-with-kotlin-a76859c324a1) 

## 随波逐流

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----5396897fa946--------------------------------) 在 Kotlin 词汇系列中发布了一个新视频，讨论了使用 [Kotlin 流](https://www.youtube.com/redirect?q=https%3A%2F%2Fd.android.com%2Fkotlin%2Fflow&v=emk9_tVVLcc&event=video_description&redir_token=QUFFLUhqbS1RRXhTckNWa1BETkxtcERiejJ2X2x2c3ptZ3xBQ3Jtc0trT3k2Z1Y4d213WUg0aVU2QU1taGhLU1IwZlBVX2NxTU4yalJLSkxOSEdDM1puWFY5amQ5aXF5RXZoV19hbjEzdlNWM0V5ajh3QWd4NW9jS3lTaENpeXdoQVlMT1g2R1NydXJWcVFmRUJ2MUd4RXdQNA%3D%3D)来发出数据流。它建立在他早期的视频[的基础上，所以你可能想先看看那个视频……跟上潮流。](https://www.youtube.com/watch?v=bM7PVVL_5GM&feature=youtu.be)

## 科特林扩展:查看绑定与合成

[David Winer](https://twitter.com/davidjwiner) 发表了一篇博客，讨论 Kotlin 合成以及视图绑定(这两者都是消除代码中那些讨厌的`findViewById()`调用的机制)。文章指出，在 Kotlin 插件的未来版本中，合成将被弃用(原因详见文章)。这篇文章还讨论了`@Parcelize`扩展，它将继续被推荐和支持。

 [## Kotlin Android 扩展的未来

### Android Kotlin Extensions Gradle 插件(不要与 Android KTX 混淆)于 2017 年发布，并带来了两个…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/11/the-future-of-kotlin-android-extensions.html) 

## 背景位置

在最近的 Android 版本中，围绕保护用户数据和让用户对如何访问他们的数据有更多的控制和透明度，有了许多变化。关注的主要领域之一是位置，因为用户可能不希望应用程序访问这些数据，并且可能希望非常小心地控制这种访问。

按照这种思路，Google Play 政策将很快要求在后台运行时需要访问位置的应用程序(从 Play Store)请求访问权限。本文详细介绍了请求它的过程。

[](https://android-developers.googleblog.com/2020/11/tips-for-getting-your-app-approved-for-background-location-access.html) [## 让您的应用程序获得后台位置访问批准的提示

### 在隐私方面，我们致力于为用户提供数据访问的控制权和透明度。用户…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/11/tips-for-getting-your-app-approved-for-background-location-access.html) 

# 那么现在…

这次到此为止。所以去[疯](https://www.youtube.com/c/AndroidDevelopers/playlists?view=50&sort=dd&shelf_id=1)换 [App 捆绑](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9RJo0uMB_Di3xZ_ZLeau-D)和[材质设计组件](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8SmtMNw34wvYkqj45rV1d3)！查看[明年对应用捆绑包和目标 API 的需求](https://android-developers.googleblog.com/2020/11/new-android-app-bundle-and-target-api.html)！阅读关于[片段](https://developer.android.com/guide/fragments)和[科特林流](https://developer.android.com/kotlin/flow)的最新文档！查看媒体上的 [Android 开发者出版物](https://medium.com/androiddevelopers)、Android 开发者博客[和 YouTube 上的](https://android-developers.googleblog.com/) [Android 开发者频道](https://www.youtube.com/c/AndroidDevelopers/playlists?view=50&sort=dd&shelf_id=1)上的最新开发者内容！请尽快回到这里，收听 Android 开发者世界的下一次更新。