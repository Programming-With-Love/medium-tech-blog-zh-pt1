# 现在在 Android #25 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-25-8596a08554d7?source=collection_archive---------0----------------------->

![](img/8009a1a5e41ed4b5dcda1240d532d401.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## Android 11 发布、Jetpack 数据存储、隐私更改、Android GPU Inspector 和播客剧集

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 视频和播客形式的 NiA25

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 录像

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

# 安卓 11:到了！

![](img/61f5119079b8e452460bf00122d779e3.png)

经过几个月的预览版和测试版发布，我们成功了:Android 11 正式推出，源代码推送到 [Android 开源项目(AOSP)](https://source.android.com/) 。

我已经为开发人员和用户讨论了这个版本中的特性，但是这里有一些提示和提醒:

*   **UI 改进**，包括通知面板中的对话、气泡和同步的 IME 动画。
*   **轻松控制**连接的设备和媒体。
*   **隐私增强**如一次性和自动重置权限、范围存储改进、后台位置和生物识别强度 API(为早期版本添加了新的 Jetpack 生物识别库)。
*   **开发者增强**比如新的[退出原因 API](https://developer.android.com/reference/kotlin/android/app/ActivityManager#gethistoricalprocessexitreasons) ，开发者选项面板中的行为切换，ADB 增量安装，以及更多针对平台 API 的 Kotlin 可空性注释。

我们还在 Google Play 系统更新方面取得了更大的进步(最初被称为“主线”，因为像我这样的人会对命名变化感到困惑)，为更多模块提供支持，以便我们可以在整个生态系统中更频繁地更新核心系统功能。

如果你想要一个更长的列表，看看 Stephanie Saad Cuthbertson 关于开发者特性的博客以及 Dave Burke 关于用户特性的文章。

# Jetpack 数据存储

[Jetpack DataStore](https://developer.android.com/jetpack/androidx/releases/datastore#1.0.0-alpha01) 是我们提供的一个新库，作为共享首选项的替代(和改进)。这个新的库使用 Kotlin 协同例程和 Flow 来实现更简单的异步读写，这是前进的方向。它现在只是在 alpha 中，但是请查看它，看看最终如何迁移到它。

数据存储中有两种不同的 APIs 方法。Preferences DataStore 本质上完全取代了 SharedPreferences，它使用键-值对来存储简单数据，就像 SharedPreferences 一样。但是 Preferences DataStore 比 SharedPreferences 更强大和健壮，因为它自动处理适当的异步读写(而不是 SharedPreferences 的同步写方法……然后您必须想出如何脱离 UI 线程来完成)。

第二个 API 是 Proto DataStore，它允许您为更丰富、类型安全的对象数据存储创建一个模式，由 protobufs 提供支持。

从查看下面的[数据存储文章](http://Preferences codelab and a Proto codelab to jump-start your learning)开始。然后尝试使用[偏好代码实验室](https://codelabs.developers.google.com/codelabs/android-preferences-datastore/#0)和[原型代码实验室](https://codelabs.developers.google.com/codelabs/android-proto-datastore/#0)来启动您的学习，并下载[库](http://Preferences codelab and a Proto codelab to jump-start your learning)。

[](https://android-developers.googleblog.com/2020/09/prefer-storing-data-with-jetpack.html) [## 首选使用 Jetpack 数据存储存储数据

### 欢迎 Jetpack 数据存储，现在在 alpha 中-一个新的和改进的数据存储解决方案，旨在取代…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/09/prefer-storing-data-with-jetpack.html) 

# 文章和视频

## 适应最近的 Android 隐私变化

[Fred Chung](https://medium.com/u/ac312b7e211e?source=post_page-----8596a08554d7--------------------------------) 写了[一篇文章](/androiddevelopers/adapt-your-app-for-the-latest-privacy-best-practices-d7469a547314)帮助开发者理解和适应最近的隐私变化。随着我们继续为用户数据访问提供更好的控制和透明度，每个版本都在这一领域带来了一些变化，Android 11 也不例外。虽然这些更改中的许多都受到 targetSdk 标志的限制，但是您将希望了解并最终迁移您的代码，因此您可能希望阅读本文以了解如何做。

特别是，文章谈到:

*   **包访问** : Android 11 限制了包在设备上的可见性。也就是说，应用程序无法再访问用户设备上其他任意应用程序的信息。这篇文章讨论了它现在的工作方式，并链接到一个[实施指南](https://developer.android.com/training/basics/intents/package-visibility-use-cases)。
*   **增量位置权限** : Android 11 要求前台+后台的位置访问是增量请求的(先前台，后后台，用户进入设置，授予后台位置权限)。
*   **前台服务**用于定位、麦克风和摄像机接入。
*   **不可重置的 id**:随着我们让开发人员摆脱不可重置的设备标识符，这一变化已经发展了几个版本。在 Android 11 中，对 getIccId()的调用不再返回有用的信息，所以你应该找到其他方法来获取你需要的信息(提示:改为使用可重置的标识符)。首先，请阅读我们提供的关于唯一标识符最佳实践的指南。

## Android GPU 检查器

今年早些时候，我们发布了一款新的评测工具，您可以使用它来帮助调整 3D 图形性能。然而，当我们继续工作的时候，它只在早期预览版中可用。

快进到…现在，Android GPU Inspector 以“公开测试版”的形式推出，供大家使用。(阅读:它仍然是[测试版](https://github.com/google/agi/releases/tag/v1.0.0)，团队仍在努力，但我们现在已经准备好让更多的人来测试它，试用它，并[向我们发送反馈](https://github.com/google/agi/issues)。)

该工具类似于我们提供的其他分析工具，如 Android Studio 的 CPU profiler 和独立的 [systrace/perfetto 工具](https://developer.android.com/topic/performance/tracing)，但它包含特定于 GPU 的低级信息，可以帮助开发人员(特别是那些编写游戏和其他性能敏感的图形应用程序的开发人员)调整他们的 3D 性能。

Android GPU Inspector 依赖于与设备驱动的配合，所以[设备支持](https://gpuinspector.dev/docs/devices)目前仅限于 Pixel 4 和 Pixel 4 XL，不过以后找 AGI 支持更多设备。

你可以在这里下载工具[，通过下面的文章和视频了解更多。](https://gpuinspector.dev/)

[](https://android-developers.googleblog.com/2020/09/android-gpu-inspector-open-beta.html) [## Android GPU Inspector 公开测试版

### 随着 Android 11 在 Pixel 上的推出，Android GPU Inspector (AGI)已经从有限的开发者预览版升级到…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/09/android-gpu-inspector-open-beta.html) 

# 播客剧集

## ADB 148:[约束|运动][布局|编辑器]

![](img/777e9c30ba6810eaac518ecac621b2b5.png)

肖恩·麦克奎蓝和我与 T2 的尼古拉斯·罗德和 T4 的约翰·霍福德谈论了 MotionEditor，该软件最近在 Android Studio 4.0 中稳定运行。但只要我们在谈论这个工具，我们也广泛地谈论了 MotionLayout 以及 ConstraintLayout 和其他设计工具。

[](https://androidbackstage.blogspot.com/2020/09/adb-148-constraintmotionlayouteditor.html) [## ADB 148:[约束|运动][布局|编辑器]

### 肖恩·麦克奎蓝和我与尼古拉斯·罗德和约翰·霍福德谈论了 MotionEditor，它最近在…

androidbackstage.blogspot.com](https://androidbackstage.blogspot.com/2020/09/adb-148-constraintmotionlayouteditor.html) 

## 与苹果交谈

肖恩·麦克奎蓝在他的播客中加入了彼得-约翰欢迎节目，谈论 Jetpack Compose。

# 那么现在…

这次到此为止。所以去[了解一下 Android 11](https://android-developers.googleblog.com/2020/09/android11-final-release.html) ！下载新的 [Jetpack 数据存储库](https://android-developers.googleblog.com/2020/09/prefer-storing-data-with-jetpack.html)库！了解[适应最新的隐私变化](/androiddevelopers/adapt-your-app-for-the-latest-privacy-best-practices-d7469a547314)！玩玩[安卓 GPU 检查员](https://android-developers.googleblog.com/2020/09/android-gpu-inspector-open-beta.html)！收听最新的[亚行](https://androidbackstage.blogspot.com/2020/09/adb-148-constraintmotionlayouteditor.html)和[与苹果](https://anchor.fm/talkingwithapples/episodes/Talking-Jetpack-Compose-in-Alpha-with-Sean-McQuillan-ejbqi8)对话播客！请尽快回到这里，收听 Android 开发者世界的下一次更新。