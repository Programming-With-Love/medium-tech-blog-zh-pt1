# 现在在 Android #14 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-14-78a91def4962?source=collection_archive---------1----------------------->

![](img/bd34c1543ef772fc66d9c296b3533d17.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## Android 11 开发者预览版 2、Android X 发布、文章、视频、游戏开发和 ADB 播客剧集

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

> 嘿大家:我希望每个人都保持安全和健康。如果你像我一样躲在家里工作，也许你需要一些开发人员的帮助。以下是 Android 团队最近的一些作品，值得一看。

# 视频和播客形式的 NiA14

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

## 录像

## 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

[](http://nowinandroid.googledevelopers.libsynpro.com/website/now-in-android-14-android-11-developer-preview-2-android-x-releases-game-development-and-more) [## 现在在 Android: 14 - Android 11 开发者预览版 2，Android X 发布，游戏开发，以及…

### 欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。在这个…

nowinandroid.googledevelopers.libsynpro.com](http://nowinandroid.googledevelopers.libsynpro.com/website/now-in-android-14-android-11-developer-preview-2-android-x-releases-game-development-and-more) 

# Android 11:开发者预览版 2

![](img/ea1f6911f84c526536d702e8a45fc27a.png)

Dial it up to Android 11

我在[第 13 集](/androiddevelopers/now-in-android-13-483740e711c0)中谈到了 Android 11 的第一个预览。而现在，下一个版本已经落下了。戴夫·伯克在 Android 开发者博客上发布了一篇关于此次发布的[概述。查看预览版](https://android-developers.googleblog.com/2020/03/android-11-developer-preview-2.html)中已经发布的一些功能的更新，以及一些刚刚推出的新功能，包括我个人最喜欢的功能:IME 动画控制。

## 同步 IME 动画

对于一个平台开发者来说，所有的新特性就像我们的孩子:我们没有偏爱。但真的，IME 动画控制是我最喜欢的-最喜欢的功能。你一直在向我们要求这个特性(我们自己也一直想要！)好几年了，现在到了。

这个想法是，当需要键盘输入时，IME(键盘)会自动弹出。这是用户需要的功能，但它并没有提供用户或开发人员想要的体验，因为外观只是简单地突然出现，造成了视觉上的不连续，导致应用程序在可用屏幕空间发生变化的情况下，突然进入调整后的布局。键盘实际上是有动画效果的……但是应用程序会立即捕捉到动画后的大小，导致这个视觉上的小问题。

开发者想要的，也是新 API 提供的，是既能让[在 IME 位置](https://developer.android.com/reference/android/view/WindowInsetsAnimation.Callback)动画播放时监听它的信息(这样应用程序可以同步它们自己的动画以适应)*又能让*到[控制 IME](https://developer.android.com/reference/android/view/WindowInsetsAnimationController) 的动画。在侦听器和动画控件之间，应用程序现在有足够的能力来创建将应用程序 UI 中的更改与 IME 本身的动画相融合的体验，从而提供更好的体验。

顺便说一下，这个特性实际上已经存在一段时间了。事实上，它几乎*达到了 Android 10…但还没有完全准备好，所以我们不得不为 Android 11 完成它。*

## NDK 图像解码器

Android 11 中添加的另一个新 API 是 NDK 的图像解码器，尤其是对本地代码开发者而言。如果你目前正在 JNI 通过平台 API 解码你的图像，或者更糟(至少从应用程序大小的角度来看)，捆绑其他库来处理图像解码，那么我们有一个交易给你…

您现在可以使用新的 NDK `[ImageDecoder](https://developer.android.com/ndk/reference/group/image-decoder)` API 来处理常见格式的图像(如 JPEG、PNG、GIF、WebP 和 HEIF)。查看 [NDK 指南](https://developer.android.com/ndk/guides/image-decoder)了解更多信息。还可以查看一下 [ImageDecoder 示例](https://github.com/android/ndk-samples/tree/master/teapots/image-decoder)，尤其是 [Texture.cpp](https://github.com/android/ndk-samples/blob/master/teapots/image-decoder/src/main/cpp/Texture.cpp#30) ，看看它是如何工作的。

## Android 11 中的可空性

我们还在 Android 11 中的 SDK APIs 中添加了新的可空性注释。

[David Winer](https://twitter.com/davidjwiner) 在 Android 开发者博客上发布了这篇文章:

[](https://android-developers.googleblog.com/2020/03/handling-nullability-in-android-11-and.html) [## 在 Android 11 及更高版本中处理可空性

### 去年 5 月在 Google I/O 上，我们宣布 Android 将首先进入 Kotlin，现在前 1000 个 Android 应用中超过 60%…

android-developers.googleblog.com](https://android-developers.googleblog.com/2020/03/handling-nullability-in-android-11-and.html) 

其中讨论了 Kotlin 中的可空性以及它如何与用 Java 编程语言编写的 API 一起工作(它通过注释支持可空性，而不是 Kotlin 的语言支持可空性)。

具体来说，Android 11 引入了跨 API 的新注释，这可能会在构建时引入新的警告和错误(这是好事！在构建时而不是运行时发现这些问题！).一些曾经有@RecentlyNullable 或@RecentlyNonNull 的 API(不正确使用时可能会从 Kotlin 编译器抛出警告)被升级到了@Nullable 和@NonNull(反而会生成构建时错误)。我们还向还没有任何注释的 API 添加了新的@RecentlyNullable 和@RecentlyNonNull 注释。因此，构建您的应用程序并寻找新的警告和错误来修复。

## 但是等等，还有呢！

自上一次预览版发布以来，我们在 Android 11 中添加的其他内容包括:

*   现在有一个用于可折叠设备的[铰链角度传感器](https://developer.android.com/reference/android/hardware/Sensor#STRING_TYPE_HINGE_ANGLE)。
*   NNAPI 增加了一个“硬切换操作”，可以更快更准确地进行训练。我不确定这里的细节，但我喜欢这个名字。如果你想了解更多，可以看看[的学术文章](https://arxiv.org/pdf/1710.05941.pdf)，这篇文章解释了 swish 激活功能背后的理论。
*   从 Android 11 开始，希望从前台服务使用麦克风或摄像头的应用程序需要将 [foregroundServiceType](https://developer.android.com/guide/topics/manifest/service-element#foregroundservicetype) 属性添加到它们的清单中，就像 Android 10 中的位置变化一样。请注意，我们也在 ADB 播客的第 133 集[中与工程团队讨论了这一变化，Power Play](http://androidbackstage.blogspot.com/2020/03/episode-133-power-play.html) 。
*   应用程序现在可以在支持设备上请求[可变刷新率](https://developer.android.com/preview/features#frame-rate-api)。
*   模拟器现在支持前置和后置摄像头。

我们做预览版本的全部原因是给你一个机会在这些早期版本上试用你的应用程序，以确保一切都为你工作。这段额外的时间给了你一个机会来修复你看到的任何问题，或者在我们完成发布和发布最终版本之前报告我们需要修复的问题。为了帮助你，我们在开发者选项中添加了一个新的[设置屏幕](https://developer.android.com/preview/test-changes#identify-dev-options)，你可以在其中切换各种行为变化，看看它们是否对你的情况有任何影响。因此，现在是试用 Android 11 并看看它如何为您工作的绝佳时机。

阅读[戴夫·伯克的帖子](https://android-developers.googleblog.com/2020/03/android-11-developer-preview-2.html)获取更多信息，或者去 [Android 11 预览网站](https://developer.android.com/preview)获取所有细节和信息。如果您有任何意见，请给我们[反馈](https://developer.android.com/preview/feedback)，或者填写[调查](https://google.qualtrics.com/jfe/form/SV_9HOzzyeCIEw0ij3?Source=dp-home)，让我们知道您对更新和更改的想法。

# AndroidX 释放

最近有各种值得注意的 AndroidX 版本。

## Beta: CameraX

![](img/9d70aff16ad32d89b528f46e1a9e567d.png)

CameraX 库旨在简化相机功能的开发(即使是在多样化的设备生态系统中),现已进入测试阶段。这意味着当团队在库达到稳定之前修复问题时，API 将保持稳定。

如果您在它第一次以 alpha 形式发布后没有看过它，那么 Beta 版本的一些改进包括:

*   使用 [ProcessCameraProvider](https://developer.android.com/reference/androidx/camera/lifecycle/ProcessCameraProvider) 进行显式摄像机初始化
*   通过新的 [CameraSelector](https://developer.android.com/reference/androidx/camera/core/CameraSelector) API 选择哪个摄像机(前置或后置)用于一个或多个用例
*   使用 [CameraInfo](https://developer.android.com/reference/androidx/camera/core/CameraInfo) 和 [CameraControl](https://developer.android.com/reference/androidx/camera/core/CameraControl) 更轻松地访问和控制变焦和对焦等相机功能的信息

另一个值得注意的版本是 still-in-alpha(撰写本文时为 alpha 08)[相机视图](https://developer.android.com/jetpack/androidx/releases/camera#camera-view-1.0.0-alpha08)模块，它为相机应用程序提供了与视图相关的有用组件。

你可以在 [Oscar Wahltinez](https://medium.com/u/d72e6aca71e9?source=post_page-----78a91def4962--------------------------------) 的[文章](/androiddevelopers/androids-camerax-jetpack-library-is-now-in-beta-bf4cf0cc3ea6)中阅读更多关于这次发布的信息，查看 [CameraX 概述](https://developer.android.com/training/camerax)，或者你可以在 [AndroidX 相机发布页面](https://developer.android.com/jetpack/androidx/releases/camera)上看到更多关于 CameraX 各个模块的信息。

## 稳定的

一些库变得[稳定](https://developer.android.com/jetpack/androidx/versions/stable-channel)，大部分修复了 bug 并增加了一些小功能，包括:

*   [片段 1.2.3](https://developer.android.com/jetpack/androidx/releases/fragment#1.2.3)
*   [分页 2.1.2](https://developer.android.com/jetpack/androidx/releases/paging#2.1.2) ，它修复了 2.1.1 中的一个问题，即一些部分实现的 API 无意中暴露得有点太早，导致了潜在的构建或运行时问题(阅读:停止使用 2.1.1，立即升级到 2.1.2)
*   [2 . 2 . 5 室](https://developer.android.com/jetpack/androidx/releases/room#2.2.5)
*   [Webkit 1.2.0](https://developer.android.com/jetpack/androidx/releases/webkit#1.2.0) ，其中包括 ForceDark API，用于控制以黑暗模式呈现 WebViews
*   工作管理器[2 . 3 . 3](https://developer.android.com/jetpack/androidx/releases/work#2.3.3)T20 和 [2.3.4](https://developer.android.com/jetpack/androidx/releases/work#2.3.4)

## 希腊字母的第一个字母

在最近发布 alpha 版本的许多库中，特别值得注意的是`[Activity 1.2.0-alpha02](https://developer.android.com/jetpack/androidx/releases/activity#1.2.0-alpha02)`，它增加了对 ActivityResultRegistry 的支持，允许您处理`startActivityForResult()` / `onActivityResult()`和`requestPermissions()` / `onRequestPermissionsResult()`流，而无需覆盖 Activity 或 Fragment 中的方法。如果你正在使用这个，你可能还想看看[片段 1.3.0-alpha02](https://developer.android.com/jetpack/androidx/releases/fragment#1.3.0-alpha02) ，它增加了对这些新 API 的支持。另请查看更新的指南[从活动](https://developer.android.com/training/basics/intents/result)中获取结果。

# 文章

最近，许多技术文章登上了 [Android 开发者出版物](https://medium.com/androiddevelopers)，包括:

## 主题覆盖

[Nick Butcher](https://medium.com/u/22c02a30ae04?source=post_page-----78a91def4962--------------------------------) 发布了他的 Android 风格系列文章的下一篇:

[](/androiddevelopers/android-styling-themes-overlay-1ffd57745207) [## Android 风格:主题覆盖

### 在本系列关于 Android 风格的前几篇文章中，我们已经看到了风格和主题之间的区别…

medium.com](/androiddevelopers/android-styling-themes-overlay-1ffd57745207) 

这篇文章介绍了如何使用主题覆盖来设置少量的属性，这些属性然后*覆盖*在层次结构中其余的主题属性之上。覆盖图与完整主题形成对比，后者用于指定更大的属性集，而覆盖图只关注一个小的目标子集。

## 应用捆绑测试

[Ben Weiss](https://medium.com/u/65fe4f480b1c?source=post_page-----78a91def4962--------------------------------) 写了一篇关于如何测试 Android 应用捆绑包的文章:

[](/androiddevelopers/developer-tools-on-play-store-85fb710ee33b) [## Play Store 上的开发者工具

### 支持您的开发和测试工作流

medium.com](/androiddevelopers/developer-tools-on-play-store-85fb710ee33b) 

测试 apk 相对简单；你可以把有问题的 APK 发给你的测试人员去安装。但是在应用捆绑包的世界里，你构建的二进制文件和从 Play Store 下载的 apk 是不一样的，你如何让这个过程为你的测试人员工作呢？

Ben 的一篇文章详细介绍了如何上传专门面向测试受众的包。这篇文章还介绍了 Play Console 的一个全新功能——测试人员现在可以通过一个链接轻松安装你的应用程序的历史版本，这样他们就可以访问有助于重现错误的确切版本，或者他们可以测试应用程序内的更新流程。

## 当，埃努斯，和 R8

我在正在进行的[科特林词汇](https://medium.com/androiddevelopers/tagged/kotlin)系列中贴了一篇文章:

[](/androiddevelopers/when-using-enums-and-r8-3f8f314c0a13) [## 当使用枚举和 R8 时…

### 科特林词汇——开启枚举和 R8 优化

medium.com](/androiddevelopers/when-using-enums-and-r8-3f8f314c0a13) 

这篇文章是我和@Romain 在 KotlinConf 做的一个演示的一个子集，在那里我们讨论了 Kotlin 的各种特性如何在字节码中工作，以及 R8 编译器如何优化其中一些特性的固有开销:

在枚举的情况下，添加一个`switch`语句(或者在 Kotlin 中添加一个`when`语句)会导致生成一个额外的类和数组。使用 R8 编译器可以解决这个问题。

## “暂停”修改器—在引擎盖下

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----78a91def4962--------------------------------) 贴出这篇文章:

[](/androiddevelopers/the-suspend-modifier-under-the-hood-b7ce46af624f) [## “暂停”修改器—在引擎盖下

### 科特林词汇:协程

medium.com](/androiddevelopers/the-suspend-modifier-under-the-hood-b7ce46af624f) 

这解释了 Kotlin 编译器如何在幕后转换协程。(还有一个类似内容的视频——见下文)。

## Android 11 中的存储

[Yacine Rezgui](https://medium.com/u/f51b24785c0d?source=post_page-----78a91def4962--------------------------------) 写了一篇关于 Android 11 中外部存储变化的文章:

[](/androiddevelopers/modern-user-storage-on-android-e9469e8624f9) [## Android 上的现代用户存储

### 为了保护用户数据并减少应用程序占用的空间，Android 10 引入了对以下行为的更改…

medium.com](/androiddevelopers/modern-user-storage-on-android-e9469e8624f9) 

本文讨论了 Android 10 中对作用域存储所做的一些大的改变，以及针对 Android 11 的改变。

## 科特林协程:取消和例外

曼努埃尔·维奥和[弗洛里纳·蒙特内斯库](https://medium.com/u/d5885adb1ddf?source=post_page-----78a91def4962--------------------------------)发布了一个关于科特林协程中取消和异常的三部分系列:

**第 1 部分**提供了协程的快速概述，包括定义和解释必要的部分:协程作用域、作业和协程上下文:

[](/androiddevelopers/coroutines-first-things-first-e6187bf3bb21) [## 协程:先做最重要的事情

### 协同程序中的取消和异常(第一部分)

medium.com](/androiddevelopers/coroutines-first-things-first-e6187bf3bb21) 

**第 2 部分**涵盖(等等…)协程取消。本文描述了如何取消特定的作业以及整个协同作用域。它还讨论了取消是如何合作的，以及如何适当地放弃已经被取消的协同程序中的工作。

[](/androiddevelopers/cancellation-in-coroutines-aa6b90163629) [## 协同程序中的取消

### 协程中的取消和异常(下)

medium.com](/androiddevelopers/cancellation-in-coroutines-aa6b90163629) 

**第 3 部分**通过讨论当事情没有按计划进行时该做什么来结束本系列:如何在协程代码中正确处理异常。例如，默认情况下，协程中的异常将导致取消其协程作用域中所有协程的取消，但是您可以使用 SupervisorJob 来避免这种情况:

[](/androiddevelopers/exceptions-in-coroutines-ce8da1ec060c) [## 协程中的异常

### 协程中的取消和异常(第 3 部分)——必须全部捕获！

medium.com](/androiddevelopers/exceptions-in-coroutines-ce8da1ec060c) 

这些文章来源于[弗洛里纳](https://medium.com/u/d5885adb1ddf?source=post_page-----78a91def4962--------------------------------)和[曼纽尔](https://medium.com/u/3b5622dd813c?source=post_page-----78a91def4962--------------------------------)在科特林科夫的演讲:

# 录像

我们最近也发布了一些视频:

## 易接近

谢林·图利和[林珈安·藤原](https://medium.com/u/fdba971ca390?source=post_page-----78a91def4962--------------------------------)发布了这段视频，帮助开发者了解如何创建可访问的应用程序:

这是我们的一个较长形式的视频，它将一个完整的会议会话打包成一个视频，以便于观看。说到可访问性，提高应用程序可访问性的[原则](https://developer.android.com/guide/topics/ui/accessibility/principles#system-ui-elements)指南现在解释了当您的自定义 UI 小部件从适当的 UI 子类扩展时，您的应用程序如何更容易“免费”访问。

## 科特林词汇

正在播出的[科特林词汇](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_T0fSZc9obnmnWcjvmJdw_)系列现在又有三集了:

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----78a91def4962--------------------------------) 发布了 [Suspend functions](https://youtu.be/IQf-vtIC-Uc) ，解释了 Kotlin 编译器如何在幕后转换协程(这是他发布的文章的另一种形式，上面已经讨论过):

[弗洛里纳·蒙特内斯库](https://medium.com/u/d5885adb1ddf?source=post_page-----78a91def4962--------------------------------)发布了[集合和序列](https://youtu.be/77hfjIYwouw)，讨论了何时使用科特林集合 API 进行急切求值，而不是使用序列进行懒惰求值:

最后我贴了 [D8，R8，和 enums](https://youtu.be/77hfjIYwouw) ，这是我上面提到的使用 enums 和 R8 文章时[的视频版:](/androiddevelopers/when-using-enums-and-r8-3f8f314c0a13)

# 谷歌游戏

像许多开发者和公司一样，我们期待着参加今年的游戏开发者峰会(GDC ),很抱歉我们没能亲自见到你们。虽然活动没有按计划进行，但我们已经组织了一个虚拟的谷歌游戏开发者峰会，作为跨谷歌的努力，并且有许多新的 Android 工具和功能要介绍。比如:

*   Android Studio 的系统跟踪工具现在增加了本地分析功能
*   支持 Android 生命体征中的本机调试符号，以帮助您在现场诊断崩溃
*   Unity 开发者更容易访问 Google APIs

您还可以注册预览，例如:

*   Visual Studio 的游戏开发扩展，可以更容易地将 Android 支持添加到您的跨平台游戏中
*   一个新的 Android GPU 检查工具

Android 开发者博客上有一篇[文章，概述了我们已经宣布和发布的各种东西。YouTube 上还有一个](https://android-developers.googleblog.com/2020/03/google-for-games-developer-summit-march.html) [Google for Games 开发者峰会播放列表](https://www.youtube.com/watch?v=2haNNRU1Gxs&list=PLOU2XLYxmsIKArZgMoMHQCSzQm2imLKy4)，里面有主题演讲和一系列来自 Google 的技术会议。

# ADB 播客片段

自从上一个*在 Android* 中发布以来，已经有几集 Android 开发人员在后台发布了。点击下面的链接，或者在你最喜欢的播客客户端查看它们:

[](http://androidbackstage.blogspot.com/2020/03/episode-133-power-play.html) [## 第 133 集:权力游戏

### 在这一集里，Chet 与来自框架团队的 Amith Yamasani、Makoto Onuki 和 Kweku Adams 讨论了关于电源的问题…

androidbackstage.blogspot.com](http://androidbackstage.blogspot.com/2020/03/episode-133-power-play.html) 

其中我与来自框架团队的 Amith Yamasani、Makoto Onuki 和 Kweku Adams 讨论了电源管理。我们对系统用来终止任务的试探法、瞌睡模式以及系统如何试图节省电池、TrimMemory 请求、job scheduler(work manager 使用的底层平台工具)、AppStandby buckets 等等充满了诗意。

[](http://androidbackstage.blogspot.com/2020/03/episode-134-all-work-no-play.html) [## 第 134 集:只工作不玩耍

### 在这一集中，Chet 与来自 Android Toolkit 团队的 Sumir Kataria 和 Rahul Ravikumar 讨论了工作管理器…

androidbackstage.blogspot.com](http://androidbackstage.blogspot.com/2020/03/episode-134-all-work-no-play.html) 

我和 Android Toolkit 团队的 Sumir Kataria 和 T2 Rahul ravi Kumar 谈到了 Work Manager，这是一个用于可推迟后台工作的 AndroidX 库。我们讨论了最近的变化，比如按需初始化、新的 lint 检查等等。

# 那么现在…

这次到此为止。所以去了解一下 [Android 11 DP2](https://android-developers.googleblog.com/2020/03/android-11-developer-preview-2.html) ！检查一下 [CameraX beta 版本](/androiddevelopers/androids-camerax-jetpack-library-is-now-in-beta-bf4cf0cc3ea6)和其他 [AndroidX 版本](https://developer.android.com/jetpack/androidx/versions)！在 [Android 开发者出版物](https://medium.com/androiddevelopers)和 [YouTube 频道](https://www.youtube.com/androiddevelopers)上找到大量新的技术文章和视频！看看[安卓游戏开发](https://android-developers.googleblog.com/2020/03/google-for-games-developer-summit-march.html)的世界在发生什么！查看最新的 [ADB 播客片段](http://androidbackstage.blogspot.com)！请尽快回到这里，收听 Android 开发者世界的下一次更新。