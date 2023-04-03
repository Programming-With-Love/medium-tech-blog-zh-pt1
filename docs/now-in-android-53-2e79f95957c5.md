# 现在在 Android #53 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-53-2e79f95957c5?source=collection_archive---------3----------------------->

![](img/0e3f44410a424bcf7a6e52ec65a58a4d.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。随着 2021 年接近尾声，让我们反思一下今年我们在 Android 中做的一些大事。

# 安卓 12

我们[发布了 Android 12](https://android-developers.googleblog.com/2021/10/android-12-is-live-in-aosp.html) 并推送到 [Android 开源项目](https://source.android.com/) (AOSP)。我们引入了一种新的设计语言，叫做[材料你](https://material.io/blog/announcing-material-you)。我们减少了核心系统服务使用的 CPU 时间，增加了[性能类设备功能](https://developer.android.com/about/versions/12/features/performance-class)，并增加了[新功能来提高性能](https://developer.android.com/about/versions/12/behavior-changes-12#performance)。通过[隐私仪表盘](https://developer.android.com/about/versions/12/features#privacy-dashboard)和其他新的安全和隐私[功能](https://developer.android.com/about/versions/12/behavior-changes-all#security-privacy)，用户可以更好地控制自己的隐私。我们通过一个用于丰富内容插入的[统一 API](https://developer.android.com/about/versions/12/features/unified-content-api)、[兼容媒体转码](https://developer.android.com/about/versions/12/features/compatible-media-transcoding)、更简单的[模糊和效果](https://developer.android.com/reference/android/graphics/RenderEffect)、 [AVIF 图像支持](https://developer.android.com/topic/performance/network-xfer#avif)、[增强的触觉](https://developer.android.com/about/versions/12/features#haptics)、新的[相机效果/功能](https://developer.android.com/about/versions/12/features#camera-sensor-support)、[改进的本机崩溃调试](https://developer.android.com/reference/kotlin/android/app/ActivityManager#gethistoricalprocessexitreasons)、[对圆角的支持](https://developer.android.com/about/versions/12/features#rounded_corner_apis)、[下载时播放](https://developer.android.com/games/distribute/play-as-you-download)，以及

[Android 12L](https://developer.android.com/about/versions/12/12L) 也在测试中，这让 Android 12 在大屏幕上更加出色。它包括使多任务处理更加直观的工具，包含对兼容性模式的改进等等！[今天就来试试](https://developer.android.com/about/versions/12/12L/get)。

# 构成

[Jetpack Compose](https://developer.android.com/jetpack/compose) ，Android 的现代原生 UI 工具包变得[稳定](https://android-developers.googleblog.com/2021/07/jetpack-compose-announcement.html)并准备好在生产中采用。它[与您现有的应用程序](https://developer.android.com/jetpack/compose/interop)互操作，与[现有的 Jetpack 库](https://www.youtube.com/watch?v=0z_dwBGQQWQ)集成，使用[直接的主题化](https://developer.android.com/jetpack/compose/themes)实现[材质设计](https://material.io/develop/android)，使用最少的样板文件支持[带有惰性组件的列表](https://developer.android.com/jetpack/compose/lists)，并且拥有一个强大的、可扩展的[动画系统](https://developer.android.com/jetpack/compose/animation)。您可以在 [Compose 学习路径](http://goo.gle/compose-pathway)中了解更多关于使用 Compose 的信息，并在 [Compose 路线图](http://goo.gle/compose-roadmap)中了解我们在未来的 Compose 版本中的发展方向。

# 培养

今年，Android 培训团队在 Kotlin 发布了 [Android 基础的最后四个新单元。](https://developer.android.com/courses/android-basics-kotlin/course)

*   第 3 单元讲述了[导航](https://developer.android.com/courses/android-basics-kotlin/unit-3)，在这里你可以学习如何在应用程序的不同屏幕之间来回导航。
*   单元 4 包括[连接互联网](https://developer.android.com/courses/android-basics-kotlin/unit-4)。您将为异步代码编写协程，了解 HTTP 和 REST 以从互联网获取数据，并使用 Coil 库在您的应用程序中显示图像。
*   第 5 单元讲述了[数据持久性](https://developer.android.com/courses/android-basics-kotlin/unit-5)，在这里您将学习如何在设备上存储数据，这是确保流畅一致的用户体验的一部分，即使在网络中断时也是如此..
*   第 6 单元讲述了[工作管理器](https://developer.android.com/courses/android-basics-kotlin/unit-6%5C)，你可以使用 Android Jetpack 的工作管理器 API 来安排后台工作，即使应用程序退出或设备重启，后台工作也会继续运行。

# 疯狂的技能

对于 MAD Skills，我们度过了令人惊叹的一年，发布了多个视频和博客系列，涵盖了许多重要主题:

*   [Kotlin 和 Jetpack](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc98e0f5ZbsgB63MdjZTFgsy) —了解 Jetpack KTX 库的基础知识，如何使用协程和流简化回调，以及如何使用和测试 Room/work manager API。

*   [Motion Layout](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_PEOEHNBEyy6tPX1EgtUw2) —了解如何使用 Motion Layout 及其设计工具来创建丰富的动画体验。

*   [工作管理器](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_J88-h0PhCO_aV0HIAs9Qk) —了解如何使用工作管理器安排关键后台工作:从基本使用、线程、定制配置等。

*   [导航](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9VpBMZUS9geQtc5RJ2RsUd) —了解导航组件的基础知识、工具的特定功能以及创建和导航到目的地的 API。

*   [性能](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-xjSI-rWn9SViXivBhQUnp) —了解如何使用系统跟踪和采样分析来调试应用中的性能问题。

*   [Hilt](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_9Qo-RBRYhVmME1iR4oeTK) —了解如何在您的 Android 应用中添加和使用 Hilt 进行依赖注入，使用 Hilt 进行测试的最佳实践，以及更多高级内容。

*   [分页](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9L-fmWJLhyXrdPi1YKmvqS) —学习分页的基础知识，从核心类型到将它们绑定到 UI 元素。

*   [Gradle](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8fyNmwKXYvA2CqxMhXqKXX) —了解如何配置您的构建，根据您的需求定制构建过程，以及如何编写自己的插件来进一步扩展您的构建。

要了解更多内容，请务必查看 YouTube 上的[疯狂技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# 事件

今年我们有 [Google I/O](https://io.google/2021/program/discover) 和 [Android Dev Summit](https://developer.android.com/events/dev-summit) ，两者都是虚拟的，所有人都可以免费参加！

在 I/O 上，我们发布了 [Jetpack](https://www.youtube.com/watch?v=xeQ9faYJktM) 、 [Compose](https://www.youtube.com/watch?v=qvDo0SKR8-k&t=1s) 、 [Android Studio tooling](https://www.youtube.com/watch?v=WRNWzhrl6-s) 、[大屏幕](https://www.youtube.com/watch?v=8rMPZlwvb5c&feature=youtu.be)、 [Wear OS](https://www.youtube.com/watch?v=kYIb9g1r4lw) 、[测试](https://www.youtube.com/watch?v=juEkViDyzF8)等等的更新！通过 I/O 观看所有的 [Android 视频！](https://www.youtube.com/watch?v=D_mVOAXcrtc&list=PLWz5rJ2EKKc_Q7bBAqjfP9o8WvrjgvgU8)

在 Android Dev 峰会上，我们发布了关于[隐私和安全](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_3tkP5FOQ9IgbcOxwLYMD5)、[大屏幕](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc99PA-mKk2rz0jYXshN94sM)、 [Android 12](https://www.youtube.com/watch?v=9EKQ0UC04S8&list=PLWz5rJ2EKKc9NzwBeLoHtKLLADjfGSkS8) 、 [Google Play &游戏](https://www.youtube.com/watch?v=QUwPzUCjqO8&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY)、[跨屏幕构建](https://www.youtube.com/watch?v=B7D3G6tC9n0&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4)、 [Jetpack Compose](https://www.youtube.com/watch?v=4R8-ggukUls&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T) 、[现代 Android 开发](https://www.youtube.com/watch?v=ENYorR3u6Eo&list=PLWz5rJ2EKKc-W96eOIFpda-rvYCRnoGpJ)等的更新。查看来自广告的所有[视频！](https://www.youtube.com/watch?v=WZgR5Yf1iq8&list=PLWz5rJ2EKKc_KamvEnBDJrBptAfQni7Ig)

# 甚至更多！

我们发布了新的[应用架构指南](https://android-developers.googleblog.com/2021/12/rebuilding-our-guide-to-app-architecture.html)， [Wear OS Jetpack 库](https://android-developers.googleblog.com/2021/09/wear-os-jetpack-libraries-now-in-stable.html)稳定，[手柄稳定](/androiddevelopers/hilt-is-stable-easier-dependency-injection-on-android-53aca3f38b9c)，我们发布了 [Android 游戏开发工具包](https://android-developers.googleblog.com/2021/07/introducing-android-game-development-kit.html)，我们创建了[辅助功能视频系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8OENfLdh3zM5T6IRdlVYKj)，发布了 [Android for Cars 应用库](https://developer.android.com/jetpack/androidx/releases/car-app#1.0.0-beta01)等等。

对于正在进行的 Android 内容，你可以查看我们的[博客](https://android-developers.googleblog.com/)、[媒体](https://medium.com/androiddevelopers)、 [YouTube 频道](https://www.youtube.com/c/AndroidDevelopers)和[播客](https://adbackstage.libsyn.com/)。

感谢您的关注、开发出色的应用、提供反馈并成为我们全球开发者社区的一员。我们将在 2022 年带着更多 Android 更新回来！😍😍😍