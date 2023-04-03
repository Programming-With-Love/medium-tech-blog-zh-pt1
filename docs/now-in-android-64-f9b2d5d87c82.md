# 现在在 Android #64 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-64-f9b2d5d87c82?source=collection_archive---------6----------------------->

![](img/186382e479ac2e43c3090052b6547473.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [CTS-D](https://android-developers.googleblog.com/2022/06/developer-powered-cts-cts-d.html) 、[构成库版本](https://android-developers.googleblog.com/2022/06/independent-versioning-of-Jetpack-Compose-libraries.html)、[稳定性](/androiddevelopers/jetpack-compose-stability-explained-79c10db270c8?source=collection_home---4------2-----------------------)和[动画内容](/androiddevelopers/customizing-animatedcontent-in-jetpack-compose-629c67b45894)、[协程测试迁移](/androiddevelopers/migrating-to-the-new-coroutines-1-6-test-apis-b99f7fc47774)、[意图过滤器](/androiddevelopers/making-sense-of-intent-filters-in-android-13-8f6656903dde)、 [Google Play](https://android-developers.googleblog.com/2022/06/notes-from-google-play-making-play-work.html) 和 [AndroidX](https://developer.android.com/jetpack/androidx/versions)

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

**第 64 集视频和播客**

*现在在 Android 中*也以视频和播客的形式提供。

The less reading-intensive version of this content…

# [开发人员驱动的 CTS (CTS-D)](https://android-developers.googleblog.com/2022/06/developer-powered-cts-cts-d.html) 🧪

[兼容性测试套件(CTS)](https://source.android.com/compatibility/cts) 是 [Android 兼容性计划](https://source.android.com/compatibility/overview)的关键部分，有助于确保设备为您的应用程序提供稳定、一致的环境。为了增强 CTS，我们添加了一个名为 CTS-D 的[新测试套件，由像您这样的开发人员构建和运行。您可以构建并](https://source.android.com/compatibility/cts/develop-cts-d)[向 CTS-D 贡献测试用例](https://android-review.googlesource.com/c/platform/cts/+/1890987)来帮助捕捉您在该领域看到的痛点——设备行为与 [Android 公共 API](https://developer.android.com/reference/packages)和 [Android 兼容性定义文档(CDD)](https://source.android.com/compatibility/12/android-12-cdd) 不匹配的地方。任何人都可以运行 CTS-D 套件来验证兼容性，因为它是开源的，可以在 [AOSP](https://android.googlesource.com/platform/cts/) 上获得。如果你有兴趣了解更多，我们有关于贡献和利用 CTS-D 的教程。

[](https://android-developers.googleblog.com/2022/06/developer-powered-cts-cts-d.html) [## 开发人员驱动的 CTS (CTS-D)

### CTS-D 是一个新的 CTS 模块，由应用程序开发人员提供支持，重点关注他们在……

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/06/developer-powered-cts-cts-d.html) 

# [独立版本的 Jetpack 组合库](https://android-developers.googleblog.com/2022/06/independent-versioning-of-Jetpack-Compose-libraries.html) ✒️

我们宣布，各种 Jetpack Compose 库将转向独立的版本控制方案，从而更容易增量升级您的应用程序，并保持最新的 Compose 特性。

第一个具有独立版本的库是 Compose 编译器，它与 Kotlin 版本紧密耦合。 [Compose 编译器 1.2.0](https://developer.android.com/jetpack/androidx/releases/compose-compiler) 带来了对 [Kotlin 1.7.0](https://kotlinlang.org/docs/whatsnew17.html) 的支持，同时向后和向前兼容 Compose UI 库和 Compose 运行时库；您可以将您的 Compose 编译器升级到 1.2.0 稳定版，并使用 Kotlin 1.7.0，同时保留其他 Compose 库的当前版本。

将编译器库移动到不同的版本控制方案是分离不同组合库组的版本控制的第一步。准备好你的版本，开始使用最新的 [Compose 编译器](https://developer.android.com/jetpack/androidx/releases/compose-compiler)和 [Kotlin](https://kotlinlang.org/docs/whatsnew17.html) 版本吧！

[](https://android-developers.googleblog.com/2022/06/independent-versioning-of-Jetpack-Compose-libraries.html) [## Jetpack 组合库的独立版本控制

### 从今天开始，Android 开发者关系工程师 Jolanda Verhoef 发布了各种 Jetpack 组合库…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/06/independent-versioning-of-Jetpack-Compose-libraries.html) 

# [来自 Google Play 的提示:让游戏为每个人服务](https://android-developers.googleblog.com/2022/06/notes-from-google-play-making-play-work.html) ▶️

在最新版的 Google Play Notes 中，我们提到了 [Play Integrity API](https://developer.android.com/google/play/integrity) 、[数据安全部分](https://support.google.com/googleplay/android-developer/answer/10787469?hl=en)、Android 上的[隐私沙盒](https://blog.google/products/android/introducing-privacy-sandbox-android/)以及新推出的 [Google Play SDK 索引](https://developer.android.com/distribute/sdk-index)，该索引提供了关于 100 多个最广泛使用的商业 SDK 的数据和见解。我们介绍了新的[订阅功能](https://android-developers.googleblog.com/2022/05/new-ways-to-sell-subscriptions-on-google-play_0530335598.html)，允许您为每个订阅创建多个基本计划和优惠，以及降低价格的选项，起价相当于 5 美分，以适应当地的购买力。

我们分享了 OLIO 的故事，这是一个社区驱动的应用程序，正在努力减少食物浪费，与 Jimjum studios 开始了 [Google Play 咖啡休息](https://youtu.be/6cKCFYzBuwY)系列，Jim jum studios 是去年[独立游戏加速器和独立游戏节](https://developersonair.withgoogle.com/events/playindies)计划的决赛入围者之一，并启动了 [#WeArePlay](https://g.co/play/weareplay) 活动，庆祝应用程序和游戏背后的全球社区。

[](https://android-developers.googleblog.com/2022/06/notes-from-google-play-making-play-work.html) [## Google Play 笔记:让游戏为每个人服务

### 作为 Google Play 的应用程序合作伙伴，我有机会与许多公司会面，他们分享…

android-developers.googleblog.com](https://android-developers.googleblog.com/2022/06/notes-from-google-play-making-play-work.html)  [## # WeArePlay | Google Play 控制台

### 庆祝创建应用和游戏的全球社区。各种规模的团队——由程序员创建，自…

g.co](https://g.co/play/weareplay) 

# [预发布报告中的黑暗主题测试](https://support.google.com/googleplay/thread/170731936/dark-theme-test-now-included-in-pre-launch-report) 🕶️

在您将测试 Android 应用捆绑包上传并发布到 Google Play 后，我们会将其安装在一组 Android 设备上，启动并抓取您的应用几分钟，并将您的结果编辑到预发布报告中。[我们在](https://support.google.com/googleplay/thread/170731936/dark-theme-test-now-included-in-pre-launch-report)[预发布报告](https://support.google.com/googleplay/android-developer/answer/9842757#zippy=%2Ctest-your-app)中引入了一个新的测试，在切换到黑暗主题的设备上运行可访问性检查；这可以检测颜色对比度问题，从而难以将文本和图标与背景区分开来。您还可以使用测试过程中生成的截图来检查 UI 中的所有元素是否正确应用了深色主题样式，或者您是否错过了任何元素的样式。

# 录像📹

实现卓越应用的[性能提示](https://www.youtube.com/watch?v=VJItLXb7_V8)视频涵盖了五个应用性能问题，以及 Android Studio 和 Google Play 控制台提供的帮助您诊断和解决这些问题的工具。

# 文章📰

在本周的文章中， [Todd](https://medium.com/u/b75659eb5438?source=post_page-----f9b2d5d87c82--------------------------------) 讨论了 Android 13 中意图过滤器的[行为变化，Android 13](/androiddevelopers/making-sense-of-intent-filters-in-android-13-8f6656903dde?source=collection_home---4------0-----------------------)现在提供了指定动作的意图，并且当且仅当该意图与其声明的<意图过滤器>元素匹配时，才从外部应用程序向导出的组件发出。

[](/androiddevelopers/making-sense-of-intent-filters-in-android-13-8f6656903dde) [## 理解 Android 13 中的意图过滤器

### 在 Android 13 之前，当一个应用程序在其清单中注册了一个导出的组件并添加了一个<intent-filter>时…</intent-filter>

medium.com](/androiddevelopers/making-sense-of-intent-filters-in-android-13-8f6656903dde) 

[Rebecca](https://medium.com/u/3f9b9c30bec7?source=post_page-----f9b2d5d87c82--------------------------------) 介绍了如何使用动画内容在不同的组件之间进行更平滑、更定制的过渡效果。即使是动画内容的默认行为也可以对你的应用程序的外观和感觉产生很大的影响，而不需要太多的努力。

[](/androiddevelopers/customizing-animatedcontent-in-jetpack-compose-629c67b45894) [## 在 Jetpack Compose 中自定义动画内容🌟

### 了解如何使用 AnimatedContent 在不同类型的内容之间进行更多的自定义过渡

medium.com](/androiddevelopers/customizing-animatedcontent-in-jetpack-compose-629c67b45894) 

[Ben](https://medium.com/u/84718b19bc40?source=post_page-----f9b2d5d87c82--------------------------------) 做了一个[式的详细探索【Compose 如何确定你的组件的每个参数的稳定性，看看在重组期间可以跳过什么，包括使用编译器报告来确定关于你的类推断出什么稳定性。例如，因为像 List、Set 和 Map 这样的集合类不能保证是不可变的，所以使用 Kotlinx 不可变集合或者将类注释为@Immutable 或@Stable 可以允许 compose 跳过重新编译。邮件里还有很多。](/androiddevelopers/jetpack-compose-stability-explained-79c10db270c8?source=collection_home---4------2-----------------------)

[](/androiddevelopers/jetpack-compose-stability-explained-79c10db270c8) [## 喷气背包撰写稳定性解释

### 你有没有测量过你的可组合程序的性能，发现它重新组合的代码比你预期的要多…

medium.com](/androiddevelopers/jetpack-compose-stability-explained-79c10db270c8) 

kotlinx.coroutines 1.6 引入了一组新的测试 API，以前的测试 API 现在已经过时了。 [Marton](https://medium.com/u/ec2087b3c81f?source=post_page-----f9b2d5d87c82--------------------------------) [谈到了](/androiddevelopers/migrating-to-the-new-coroutines-1-6-test-apis-b99f7fc47774?source=collection_home---4------3-----------------------)我们如何将自己的一些样本迁移到新的 API，涵盖了大多数 Android 项目的一些必要工作。

[](/androiddevelopers/migrating-to-the-new-coroutines-1-6-test-apis-b99f7fc47774) [## 迁移到新的协程 1.6 测试 API

### 查看我们将示例迁移到新 API 的步骤，然后开始迁移您自己的项目！

medium.com](/androiddevelopers/migrating-to-the-new-coroutines-1-6-test-apis-b99f7fc47774) 

# AndroidX 释放🚀

自从 Android 版 Now 的最后一集以来， [AndroidX](https://developer.android.com/jetpack/androidx/versions) 发布了一堆有趣的东西。有了这么多，我将只涵盖新稳定的:

[Activity 版本 1.5.0](https://developer.android.com/jetpack/androidx/releases/activity#1.5.0)
增加了 ComponentDialog，它是 Dialog 的子类，包含 OnBackPressedDispatcher。ComponentActivity 中的模块化回调接口，ComponentActivity 现在可以提供无状态的 ViewModelProvider。通过[生命周期 2.5.0](https://developer.android.com/jetpack/androidx/releases/lifecycle#2.5.0) 的 CreationExtras 工厂。

[Camera Version 1.1.0](https://developer.android.com/jetpack/androidx/releases/camera#1.1.0)
通过官方的 camera-video 库、YUV 到 RGB 转换、旋转、多窗口模式支持、CameraState API、启用扩展时 OnImageCapturedCallback 的 JPEG 输出、ExperimentalCameraFilter 和曝光补偿 API 等增加了视频捕捉用例。

[Compose 编译器 1.2.0 版](https://developer.android.com/jetpack/androidx/releases/compose-compiler#1.2.0)支持 Kotlin 1.7 和独立的 Compose 库版本控制。

[片段版本 1.5.0](https://developer.android.com/jetpack/androidx/releases/fragment#1.5.0)
增加 CreationExtras 集成，增加组件对话框集成，重构保存的实例状态。

[生命周期版本 2.5.0](https://developer.android.com/jetpack/androidx/releases/lifecycle#2.5.0)
向 SavedStateHandle 添加 getStateFlow() API，返回 StateFlow 用于监控值的变化，向 ViewModelProvider 添加 CreationExtras。工厂子类，通过 create: create(Class < T >，CreationExtras)的新重载来简化对应用程序或 SavedStateHandle 等的访问。

[导航 2.5.0 版](https://developer.android.com/jetpack/androidx/releases/navigation#2.5.0)
提供了一个无状态的 ViewModelProvider。Factory via Lifecycle 2.5.0 的 CreationExtras，Safe Args 升级了 Android Gradle 插件依赖关系，使其依赖于 7.0.4，并增加了对命名空间 build.gradle 属性的支持，以代替 applicationId。

[SavedState 版本 1 . 2 . 0](https://developer.android.com/jetpack/androidx/releases/savedstate#1.2.0)
savedstaregistrycontroller 允许通过 performAttach()提前附加 savedstaregistry，您现在可以通过 getSavedStateProvider()从 savedstaregistry 中检索以前注册的 SavedStateProvider，库已在 Kotlin 中重写，这导致了一些源代码不兼容的更改。

# 那么现在…👋

这次就到此为止，带着 [CTS-D](https://android-developers.googleblog.com/2022/06/developer-powered-cts-cts-d.html) 、[独立版本的 Compose 库](https://android-developers.googleblog.com/2022/06/independent-versioning-of-Jetpack-Compose-libraries.html)、 [Compose stability](/androiddevelopers/jetpack-compose-stability-explained-79c10db270c8?source=collection_home---4------2-----------------------) 、custom[Compose animated content](/androiddevelopers/customizing-animatedcontent-in-jetpack-compose-629c67b45894)、[协程测试迁移](/androiddevelopers/migrating-to-the-new-coroutines-1-6-test-apis-b99f7fc47774)、 [Android 13 意图过滤器](/androiddevelopers/making-sense-of-intent-filters-in-android-13-8f6656903dde)，还有一堆来自 [Play](https://android-developers.googleblog.com/2022/06/notes-from-google-play-making-play-work.html) 和 [AndroidX](https://developer.android.com/jetpack/androidx/versions) 的东西，很快回来这里等待 Android 开发者宇宙的下一次更新。