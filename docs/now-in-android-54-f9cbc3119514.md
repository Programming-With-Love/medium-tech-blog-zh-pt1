# 现在在 Android #54 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-54-f9cbc3119514?source=collection_archive---------5----------------------->

![](img/ddfeb08f832b8929255e1e77671156b2.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [Gradle](https://goo.gle/gradle-mad) ， [DataStore](https://www.youtube.com/watch?v=mdQjuZbLv9Y) ， [AndroidX](https://developer.android.com/jetpack/androidx/versions/all-channel) ，[拖拽](/androiddevelopers/simplifying-drag-and-drop-3713d6ef526e)，[扫视](https://developer.android.com/jetpack/androidx/releases/glance)，[看脸](https://developer.android.com/jetpack/androidx/releases/wear-watchface)， [App 架构](https://developer.android.com/jetpack/guide)，[看下一个](https://www.youtube.com/watch?v=QFMIP5GOo70)，[玩 PC 游戏](https://developers.googleblog.com/2022/01/googleplaygames.html)，等等！

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。距离我们上次真正的更新已经有很长时间了；我们的上一篇博客关注的是 2021 年的一些值得注意的时刻，所以这一期的标题可能更好一些，叫做“Android 最近”

The same content from this post, but with less detail and infinitely more talking.

# 用于 Glance 小部件的 Jetpack Alpha🔍

我们发布了第一个版本的 Jetpack Glance，这是一个新的框架，旨在更快更容易地为主屏幕和其他表面构建应用程序小部件。Glance 提供了类似的现代的、声明性的 Kotlin APIs，你已经习惯了使用 [Jetpack Compose](https://developer.android.com/jetpack/compose) ，帮助你用更少的代码构建漂亮的、响应迅速的应用程序部件。Glance 提供了一组自己的组件来帮助构建“简略”的体验——从今天开始使用应用程序小部件组件，但以后还会有更多。使用 Jetpack Compose 运行时，Glance 将这些组件翻译成可以在应用程序小部件中显示的 RemoteViews。

[](https://android-developers.googleblog.com/2021/12/announcing-jetpack-glance-alpha-for-app.html) [## 为应用程序小部件发布 Jetpack Glance Alpha

### Android 12 修改了许多 Android 用户的一个关键功能，应用程序小部件，使它们更有用，更漂亮，而且…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/12/announcing-jetpack-glance-alpha-for-app.html) 

# 喷气背包手表脸库⌚

我们推出了在 Kotlin 从头编写的 [Jetpack 手表面部库](https://developer.android.com/jetpack/androidx/releases/wear-watchface)，包括可穿戴支持库的所有功能以及许多新功能，例如:

*   手表和手机都支持的表盘样式(无需您自己的数据库或配套应用程序)。
*   在手机上支持一个[所见即所得](https://en.wikipedia.org/wiki/WYSIWYG#:~:text=In%20computing%2C%20WYSIWYG%20(%2F%CB%88,web%20page%2C%20or%20slide%20presentation.)的手表界面配置 UI。
*   较小的、独立的库(仅包含您需要的内容)。
*   通过促进开箱即用的良好电池使用模式来改进电池，例如在电池电量低时自动降低交互帧速率。
*   新的屏幕截图 API，用户可以在手表和手机上实时预览手表表面的变化。

如果您仍在使用 Wearable Support 库，我们强烈建议迁移到新的 Jetpack 库，以利用新的 API 和即将推出的功能和错误修复。

[](https://android-developers.googleblog.com/2021/12/develop-watch-faces-with-stable-jetpack.html) [## 使用稳定的 Jetpack 手表面部库开发手表面部

### 表盘是人们在智能手表上表达自己的最明显的方式之一，也是…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/12/develop-watch-faces-with-stable-jetpack.html) 

# 重建我们的应用架构指南📐

我们发布了[经过修改的应用架构指南](https://developer.android.com/jetpack/guide)，其中包含了最佳实践。随着 Android 应用程序规模的增长，重要的是设计具有适当架构的代码，以允许应用程序扩展，提高质量和健壮性，并使测试更容易。该指南包含了针对 [UI](https://developer.android.com/jetpack/guide/ui-layer) 、[域](https://developer.android.com/jetpack/guide/domain-layer)和[数据](https://developer.android.com/jetpack/guide/data-layer)层的页面，其中深入探讨了更复杂的主题，比如如何处理 UI 事件。我们也有一个[学习途径](https://developer.android.com/courses/pathways/android-architecture)来引导你完成它。

[](https://android-developers.googleblog.com/2021/12/rebuilding-our-guide-to-app-architecture.html) [## 重建我们的应用架构指南

### 随着 Android 应用程序规模的增长，重要的是设计代码时要有一个合适的架构，使应用程序能够…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/12/rebuilding-our-guide-to-app-architecture.html) 

# 谷歌在 PC 上玩游戏测试版🎮

我们宣布[在韩国、台湾和香港开放 PC 上 Google Play 游戏的注册](https://play.google.com/googleplaygames?pcampaignid=PR-Global-oo-gpgbeta-DevBlog)作为测试版，允许参与测试的用户通过谷歌构建的独立应用程序在他们的 PC 上玩一系列 Google Play 游戏。[开发者网站](https://developer.android.com/games/playgames)有一个表达兴趣的表格，以及关于将你的安卓游戏带到个人电脑上的信息。它涉及许多与你为 Chrome OS 设备优化游戏相同的更新，比如对鼠标和键盘控制的支持。

[](https://developers.googleblog.com/2022/01/googleplaygames.html) [## Google Play 游戏测试版在韩国、台湾和香港推出

### 去年 12 月，我们宣布 Google Play 游戏将登陆个人电脑。作为我们更广泛目标的一部分，使我们…

developers.googleblog.com](https://developers.googleblog.com/2022/01/googleplaygames.html) 

# MAD 技能:Gradle 和数据存储💡

![](img/be9006ec098e2b280e3a3a08f58e8ec7.png)

[MAD Skills](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7) 继续滚动，关于现代 Android 开发的技术内容。

# 疯狂技能:格拉德🐘

我们结束了关于 Gradle 和 [Android Gradle 插件 API](https://developer.android.com/reference/tools/gradle-api)的系列。

首先， [Murat](https://medium.com/u/e947fef0dfe0?source=post_page-----f9cbc3119514--------------------------------) 更深入地介绍了构建定制插件，除了前面介绍的变体 API 之外，还包括工件 API。它演示了如何构建一个插件，该插件可以用 git 版本自动更新应用程序清单中指定的版本代码。在 AGP 7.0 版本中，您可以使用这些 API 来控制构建输入，读取，修改，甚至替换中间和最终的工件。

[](/androiddevelopers/gradle-and-agp-build-apis-taking-your-plugin-to-the-next-step-95e7bd1cd4c9) [## 格雷尔和 AGP 构建 API:让你的插件更上一层楼！

### 欢迎阅读 MAD 技能系列中关于 Gradle 和 Android Gradle 插件 API 的一篇新文章。在上一篇文章中，您…

medium.com](/androiddevelopers/gradle-and-agp-build-apis-taking-your-plugin-to-the-next-step-95e7bd1cd4c9) 

接下来， [Alex Saveau](https://medium.com/u/1475a354c5ca?source=post_page-----f9cbc3119514--------------------------------) ，Gradle Play Publisher 和 Version Orchestrator 插件的维护者，提供了一个关于如何使用 AGP 和 Gradle APIs 操作 Android 构建工件的教程。

然后，我们做了一个关于格拉德勒和 AGP 构建 API 的现场问答，其中[弗洛里纳](https://medium.com/u/d5885adb1ddf?source=post_page-----f9cbc3119514--------------------------------)与[缪拉](https://medium.com/u/e947fef0dfe0?source=post_page-----f9cbc3119514--------------------------------)、[杰罗姆·多切兹](https://medium.com/u/c7609de1c53?source=post_page-----f9cbc3119514--------------------------------)和[沃伊泰克·卡利斯基](https://medium.com/u/b913acc64439?source=post_page-----f9cbc3119514--------------------------------)一起。

这个[总结帖](https://android-developers.googleblog.com/2021/12/mad-skills-gradle-and-agp-build-apis.html)总结了整个系列。

[](https://android-developers.googleblog.com/2021/12/mad-skills-gradle-and-agp-build-apis.html) [## 疯狂技能格雷尔和 AGP 建立 API 总结！

### 这是一个总结！我们刚刚完成了一个关于 Gradle 和 Android Gradle 插件构建 API 的新的 MAD 技能系列。在这个…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/12/mad-skills-gradle-and-agp-build-apis.html) 

# 疯狂技能:数据存储🗄️

[新美乐股份公司](https://medium.com/u/f4d5f1a633bb?source=post_page-----f9cbc3119514--------------------------------)开始[狂技能:数据存储](https://www.youtube.com/watch?v=mdQjuZbLv9Y)。DataStore 是 Android Jetpack 中的一个线程安全、非阻塞的库，它提供了一种安全、一致的方式来存储少量数据，如首选项或应用程序状态，取代了 SharedPreferences。它提供了一个存储由协议缓冲区支持的类型化对象的实现(Proto DataStore)和一个存储键值对的实现(Preferences DataStore)。

[](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7) [## Jetpack 数据存储简介

### DataStore 是一个 Jetpack 数据存储库，它提供了一种安全一致的方式来存储少量数据…

medium.com](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7) 

# 更多疯狂的内容

但是等等！如果这还不够，还有更多疯狂的内容！

对于正在进行的内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# AndroidX 释放🚀

自从上一集《现在在安卓》之后，很多库都升级到了稳定版！ [**合成 ConstraintLayout**](https://developer.android.com/jetpack/androidx/releases/constraintlayout#constraintlayout-compose-1.0.0) 支持 ConstraintLayout 语法进行合成。我们还发布了[**coordinator layout 1.2**](https://developer.android.com/jetpack/androidx/releases/coordinatorlayout#1.2.0)[**汽车 App 1 . 1 . 0**](https://developer.android.com/jetpack/androidx/releases/car-app#1.1.0)[**房间 2 . 4 . 0**](https://developer.android.com/jetpack/androidx/releases/room#2.4.0)[**Sqlite 2 . 2 . 0**](https://developer.android.com/jetpack/androidx/releases/sqlite#2.2.0)[**收藏 1.2.0**](https://developer.android.com/jetpack/androidx/releases/collection#1.2.0) ，以及[**佩戴 Watchface 1**](https://developer.android.com/jetpack/androidx/releases/wear-watchface#1.0.0)

我们发布了 Jetpack Compose 1.2 的第一个 alpha，以及 alpha for[**Glance 1 . 0 . 0**](https://developer.android.com/jetpack/androidx/releases/glance#1.0.0-alpha01)[**Core-Ktx 1 . 8 . 0**](https://developer.android.com/jetpack/androidx/releases/core#1.8.0-alpha01)[**work manager 2 . 8 . 0**](https://developer.android.com/jetpack/androidx/releases/work#2.8.0-alpha01)[**media router 1 . 3 . 0**](https://developer.android.com/jetpack/androidx/releases/mediarouter#1.3.0-alpha01)[**emoji 2 1 . 1 . 0**](https://developer.android.com/jetpack/androidx/releases/emoji2#1.1.0-alpha01)，

你可以在这里看到所有的 AndroidX 发行说明[。](https://developer.android.com/jetpack/androidx/versions/all-channel)

# 文章📚

[Alex](https://medium.com/u/e4ae3ec302ba?source=post_page-----f9cbc3119514--------------------------------) [写了](/androiddevelopers/jetnews-for-every-screen-4d8e7927752)关于 [Jetnews](https://github.com/android/compose-samples/tree/main/JetNews) 最近的更新，改进了它在大大小小的移动设备上的行为。它描述了我们的设计和开发过程，以便您可以了解我们的理念和相关的实现步骤，以便使用 Jetpack Compose 构建针对所有屏幕优化的应用程序，包括如何构建列表/详细信息布局。

[](/androiddevelopers/jetnews-for-every-screen-4d8e7927752) [## 每个屏幕的 Jetnews

### 更新 Jetnews，通过添加自适应列表细节布局，在手机、平板电脑、可折叠和 Chrome 操作系统上更好地工作。

medium.com](/androiddevelopers/jetnews-for-every-screen-4d8e7927752) 

[保罗](https://medium.com/u/9e7508235c54?source=post_page-----f9cbc3119514--------------------------------) [写了](/androiddevelopers/simplifying-drag-and-drop-3713d6ef526e)关于 drag & drop，以及 Android Jetpack[dragandrop library alpha](https://developer.android.com/jetpack/androidx/releases/draganddrop)如何更容易地处理拖放到你的应用中的数据。

[](/androiddevelopers/simplifying-drag-and-drop-3713d6ef526e) [## 简化拖放

### 今天我们将 DropHelper 引入 Jetpack drag alpha 版本。

medium.com](/androiddevelopers/simplifying-drag-and-drop-3713d6ef526e) 

# 无障碍系列🌐

[可访问性系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8OENfLdh3zM5T6IRdlVYKj)继续，从如何正确实现在一段时间后消失的 UI 元素开始。

我们还将介绍[可访问性扫描器](https://play.google.com/store/apps/details?id=com.google.android.apps.accessibility.auditor)如何通过建议可访问性方面的改进来帮助您为所有用户改进您的应用程序。

最后，我们调查 [Espresso](https://developer.android.com/guide/topics/ui/accessibility/testing#espresso) 和[可访问性测试框架](https://github.com/google/Accessibility-Test-Framework-for-Android)如何帮助你创建自动化的可访问性测试。

除了[可访问性系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8OENfLdh3zM5T6IRdlVYKj)之外，我们还有资源帮助您[了解更多关于 Android](https://goo.gle/3gvL8HC) 的可访问性，以及[如何构建更易访问的 Android 应用](https://developer.android.com/guide/topics/ui/accessibility)。

# 安卓电视和谷歌电视📺

[万由里](https://medium.com/u/678c18c97ff4?source=post_page-----f9cbc3119514--------------------------------)报道了安卓电视&谷歌电视[观看下一个 API](https://developer.android.com/training/tv/discovery/watch-next-add-programs) 的最佳实践，它通过允许你的内容显示在观看下一行来增加你的应用的参与度。

# 亚行播客 Episodes🎙

![](img/a2c85c3e87d656fb391558c05b0c83ec.png)

自从 Android 上一期 Now 发布以来，已经有三集 [Android 开发者后台](https://adbackstage.libsyn.com/)发布了。点击下面的链接，或者在你最喜欢的播客客户端查看它们:

在[第 179 集:Flibberty Widget](https://adbackstage.libsyn.com/flibberty-widget) 、 [Chet](https://medium.com/u/cb2c4874d3e9?source=post_page-----f9cbc3119514--------------------------------) 和 [Romain](https://medium.com/u/c967b7e51f8b?source=post_page-----f9cbc3119514--------------------------------) 与来自伦敦工程办公室的 Nicole McWilliams 和 Petr ermák 谈论他们在[应用 Widget](https://developer.android.com/guide/topics/appwidgets/overview)和数字福利方面的工作。

在[第 180 集:Kotlin 魔法平台](https://adbackstage.libsyn.com/episode-180-kotlin-magic-platform)、 [Chet](https://medium.com/u/cb2c4874d3e9?source=post_page-----f9cbc3119514--------------------------------) 、 [Tor](https://medium.com/u/8251a5f98c9d?source=post_page-----f9cbc3119514--------------------------------) 、 [Romain](https://medium.com/u/c967b7e51f8b?source=post_page-----f9cbc3119514--------------------------------) 与 Android Toolkit 团队的 [Yigit Boyar](https://medium.com/u/9f0ead35e83b?source=post_page-----f9cbc3119514--------------------------------) 谈论 Kotlin 多平台。

在[第 181 集:架构→结尾 bug 少](https://adbackstage.libsyn.com/episode-181-architecture-fewer-bugs-at-the-end)，[切特](https://medium.com/u/cb2c4874d3e9?source=post_page-----f9cbc3119514--------------------------------)和[托尔](https://medium.com/u/8251a5f98c9d?source=post_page-----f9cbc3119514--------------------------------)与[伊吉特博雅](https://medium.com/u/9f0ead35e83b?source=post_page-----f9cbc3119514--------------------------------)聊天(又来了！)和 Android 开发者关系团队的 Manuel Vivo 讨论应用架构。该团队已经发布了[新的架构指南](https://developer.android.com/jetpack/guide)，我们在这里讨论该指南，以及我们的架构建议如何应用于新的 [Jetpack Compose](https://developer.android.com/jetpack/compose) 世界。

# 那么现在…👋

这就是 Android 中“现在和最近”的时间，有 [Gradle](https://goo.gle/gradle-mad) 和 [DataStore](https://www.youtube.com/watch?v=mdQjuZbLv9Y) MAD Skills 系列， [AndroidX](https://developer.android.com/jetpack/androidx/versions/all-channel) 发布，关于[的文章跨多个屏幕尺寸构成](/androiddevelopers/jetnews-for-every-screen-4d8e7927752)，以及[拖放](/androiddevelopers/simplifying-drag-and-drop-3713d6ef526e)。我们推出了 [Jetpack Glance](https://developer.android.com/jetpack/androidx/releases/glance) alpha、[Jetpack Watch Face library](https://developer.android.com/jetpack/androidx/releases/wear-watchface)、我们的[改进版应用架构指南](https://developer.android.com/jetpack/guide)，以及[Google Play Games on PC](https://developers.googleblog.com/2022/01/googleplaygames.html)的测试版。我们介绍了用于电视的 [Watch Next](https://www.youtube.com/watch?v=QFMIP5GOo70) API，并有播客介绍了 [Widgets](https://adbackstage.libsyn.com/flibberty-widget) 、 [Kotlin 多平台](https://adbackstage.libsyn.com/episode-180-kotlin-magic-platform)和[架构](https://adbackstage.libsyn.com/episode-181-architecture-fewer-bugs-at-the-end)。请尽快回到这里，等待 Android 开发者世界的下一次更新。