# 现在在 Android #39 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-39-d2a4fa5fdbd2?source=collection_archive---------6----------------------->

![](img/356332c200ed7e5abccdbf4bb92a0d86.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## Android Studio 4.2 & Hilt，Google Play 改动，#MAD 技能:导航，后台任务检查器，冷到热流

# Android Studio 4.2 发布至稳定渠道

![](img/fb8cf52d527e7aff7f33842ec63983e3.png)

Android Studio 4.2 现已在稳定发布渠道发售。阅读[博客](https://android-developers.googleblog.com/2021/05/android-studio-42.html)了解新内容的详细信息，包括帮助您的项目迁移到最新 Android Gradle 插件版本的新工具。我们还增强了许多东西，如[数据库检查器](https://developer.android.com/studio/inspect/database)、[系统跟踪](https://developer.android.com/topic/performance/tracing)、SafeArgs 支持、应用更改和新项目向导。像往常一样，在这里下载[在这里](https://developer.android.com/studio/)，在这里发布[。](https://source.android.com/source/report-bugs#developer-tools)

[](https://android-developers.googleblog.com/2021/05/android-studio-42.html) [## Android Studio 4.2

### 我们很高兴地宣布，Android Studio 4.2 现在可以在稳定发布渠道下载。的…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/05/android-studio-42.html) 

# 希尔特是稳定的，并准备生产

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----d2a4fa5fdbd2--------------------------------) Hilt 是利用 Dagger DI 库的更简单、更固执己见的方式，消除了样板文件并减少了错误。它为流行的 Jetpack 库(如 ViewModel、WorkManager、Navigation 和 Compose)提供直接注入支持。( [DI 基础](https://developer.android.com/training/dependency-injection)，[文档](https://developer.android.com/training/dependency-injection/hilt-android))

[](/androiddevelopers/hilt-is-stable-easier-dependency-injection-on-android-53aca3f38b9c) [## 剑柄稳定！Android 上更容易的依赖注入

### Jetpack 推荐的 Android 应用依赖注入(DI)解决方案，已经很稳定了！

medium.com](/androiddevelopers/hilt-is-stable-easier-dependency-injection-on-android-53aca3f38b9c) 

# Google Play 更新

Google Play 宣布了围绕应用元数据的即将到来的政策变化，例如禁止图标、标题和开发者名称中的某些关键词。在你的列表中，还有新的特征图形、截图、视频和简短描述指南。[博客](https://android-developers.googleblog.com/2021/04/updated-guidance-to-improve-your-app.html)有所有的细节，包括推出变革的时间表。

[](https://android-developers.googleblog.com/2021/04/updated-guidance-to-improve-your-app.html) [## 更新指南，提高您在 Google Play 上的应用质量和发现能力

### 当 Google Play 在 2008 年推出时，开发者很容易因为只有几百个应用和游戏而受到关注…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/04/updated-guidance-to-improve-your-app.html) 

Play 也[在博客](https://android-developers.googleblog.com/2021/05/new-safety-section-in-google-play-will.html)上写了一个即将到来的安全部分，给用户更多的透明度和对他们数据的控制。为此，我们将要求您分享有关您收集和存储的身份识别数据类型、这些数据的使用方式以及实施隐私政策的信息。

[](https://android-developers.googleblog.com/2021/05/new-safety-section-in-google-play-will.html) [## Google Play 中的新安全部分将让应用程序如何使用数据变得透明

### 我们与开发人员密切合作，让 Google Play 成为一个安全、可信的空间，让数十亿人享受最新的…

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/05/new-safety-section-in-google-play-will.html) 

# 疯狂技能:导航

![](img/fec41517bd58f1e075e9c8948e5b4a5d.png)

在 MAD Skills，我们强调现代 Android 开发的地方， [Murat Yener](https://medium.com/u/e947fef0dfe0?source=post_page-----d2a4fa5fdbd2--------------------------------) 继续围绕一个咖啡和甜甜圈跟踪应用进行导航的[系列。](https://www.youtube.com/watch?v=fiQiMy0HzsY&list=PLWz5rJ2EKKc9VpBMZUS9geQtc5RJ2RsUd)

在[第 2 集](/androiddevelopers/navigation-conditional-navigation-e82d7e4905f0)， [Murat](https://medium.com/u/e947fef0dfe0?source=post_page-----d2a4fa5fdbd2--------------------------------) 展示了如何添加条件导航，让人们直接导航到跟踪咖啡和甜甜圈的片段，或者只跟踪甜甜圈的片段。(没有咖啡的甜甜圈！？)他还讲述了如何为条件导航构建一个测试。

对于喜欢阅读内容的人来说，这里是文章的形式:

[](/androiddevelopers/navigation-conditional-navigation-e82d7e4905f0) [## 导航:条件导航

### 这是导航系列的第二篇文章。如果你更喜欢看这个内容而不是阅读，检查…

medium.com](/androiddevelopers/navigation-conditional-navigation-e82d7e4905f0) 

[第 3 集](/androiddevelopers/navigation-nested-graphs-and-include-tag-92fd62739b73)讲述了嵌套和包含的导航图，以及如何使用它们来帮助模块化应用。

嵌套/包含图表，文章:

[](/androiddevelopers/navigation-nested-graphs-and-include-tag-92fd62739b73) [## 导航:嵌套图形和包含标签

### 这是第二个导航系列的第三篇文章。如果你更喜欢看这些内容而不是阅读…

medium.com](/androiddevelopers/navigation-nested-graphs-and-include-tag-92fd62739b73) 

## 但是等等，还有呢！

对于正在进行的内容，一定要查看 YouTube 上的[疯狂技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者[这个指向所有内容的便捷登陆页面](https://developer.android.com/series/mad-skills)。

# 文章

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----d2a4fa5fdbd2--------------------------------)

[](/androiddevelopers/things-to-know-about-flows-sharein-and-statein-operators-20e6ccb2bc74) [## 关于流的 shareIn 和 stateIn 操作符需要知道的事情

### Flow.shareIn 和 Flow.stateIn 操作符将冷流转换为热流:它们可以多播信息…

medium.com](/androiddevelopers/things-to-know-about-flows-sharein-and-statein-operators-20e6ccb2bc74) 

[Murat Yener](https://medium.com/u/e947fef0dfe0?source=post_page-----d2a4fa5fdbd2--------------------------------)

[](/androiddevelopers/background-task-inspector-30c8706f0380) [## 后台任务检查器

### Android Studio 包括多个检查器，如布局检查器和数据库检查器，以帮助您…

medium.com](/androiddevelopers/background-task-inspector-30c8706f0380) 

# ADB 播客片段

![](img/d033fd60898f567cc3210866b53970a1.png)

自从上一期《现在》发布以来，已经有几集 [Android 开发者的后台](http://adbackstage.libsyn.com/)贴出来了。

[**第 161 集**](http://adbackstage.libsyn.com/episode-161-datastories) **:** [Tor](https://medium.com/u/8251a5f98c9d?source=post_page-----d2a4fa5fdbd2--------------------------------) ， [Chet](https://medium.com/u/cb2c4874d3e9?source=post_page-----d2a4fa5fdbd2--------------------------------) ， [Romain](https://medium.com/u/c967b7e51f8b?source=post_page-----d2a4fa5fdbd2--------------------------------) 与 Rohit Sathyanarayana 和[弗洛里纳 Muntenescu](https://medium.com/u/d5885adb1ddf?source=post_page-----d2a4fa5fdbd2--------------------------------) 谈论 [DataStore](https://developer.android.com/topic/libraries/architecture/datastore) ，一个取代 SharedPreferences 的库。

[**第 162 集**](http://adbackstage.libsyn.com/episode-162-kotlin-symbol-processing) **:**

# 那么现在…

这次到此为止。阅读关于[在 Android Studio 4.2](https://android-developers.googleblog.com/2021/05/android-studio-42.html) 中你能做的一切。用[刀柄](/androiddevelopers/hilt-is-stable-easier-dependency-injection-on-android-53aca3f38b9c)简化你的 DI。查看来自 Google Play 的[元数据](https://android-developers.googleblog.com/2021/04/updated-guidance-to-improve-your-app.html)和[安全](https://android-developers.googleblog.com/2021/05/new-safety-section-in-google-play-will.html)帖子。观看[狂技能:导航](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9VpBMZUS9geQtc5RJ2RsUd)系列最新剧集。阅读关于[转换流程](/androiddevelopers/things-to-know-about-flows-sharein-and-statein-operators-20e6ccb2bc74)和新[后台任务检查器](/androiddevelopers/background-task-inspector-30c8706f0380)的文章。听我们聊一聊[数据存储](http://adbackstage.libsyn.com/episode-161-datastories)和 [Kotlin 符号处理](http://adbackstage.libsyn.com/episode-162-kotlin-symbol-processing)。请务必在 5 月 18 日至 20 日观看谷歌 I/O 上的所有公告。请尽快回到这里，收听 Android 开发者世界的下一次更新。