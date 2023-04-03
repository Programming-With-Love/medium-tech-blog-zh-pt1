# 现在在 Android #51 中——广告概述第 2 部分

> 原文：<https://medium.com/androiddevelopers/now-in-android-51-ads-recap-part-2-188cb7e62e15?source=collection_archive---------6----------------------->

![](img/93a52456dbdd1f168c2700acfbdd204c.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [Jetpack 作曲](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T)、[料友](https://m3.material.io/)、[狂技能](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-W96eOIFpda-rvYCRnoGpJ)、[安卓电视](https://www.youtube.com/watch?v=XvGLD6fLrIU&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4&index=3)、[安卓汽车/汽车](https://www.youtube.com/watch?v=ELeNmFrm4vM&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4&index=2)、 [WearOS](https://www.youtube.com/watch?v=B7D3G6tC9n0&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4&index=1) 、[游戏开发](https://www.youtube.com/watch?v=U1WIy7kJG3M&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=3)、 [Google Play](https://www.youtube.com/watch?v=QUwPzUCjqO8&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=1) 。

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。本周是我们报道 2021 年安卓开发者峰会(ADS)的系列报道的第二部分。

The video version of this blog post. See if you can find all of the differences!

# 喷气背包撰写和材料你🎶

我们有很多以 Jetpack 撰写为中心的会议。

ADS 上最大的公告之一是将[素材 You](https://m3.material.io/) 引入 [Jetpack Compose](https://developer.android.com/jetpack/compose?gclid=CjwKCAiA1aiMBhAUEiwACw25MQwKHc7VNfGzu8Q9WJd74Y5eJvfoO_75HeW-UTrnzQmUimkMUOVlZBoCTxwQAvD_BwE) ，所以从 [Nick Rout](https://medium.com/u/37290b859aca?source=post_page-----188cb7e62e15--------------------------------) 的[实现素材 You using Jetpack Compose](https://www.youtube.com/watch?v=jrfuHyMlehc&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=1) 开始是一个很好的地方。它涵盖了广泛的主题，如新的和更新的[材质主题和组件](https://developer.android.com/jetpack/androidx/releases/compose-material3)、[动态颜色](https://developer.android.com/jetpack/compose/themes/material#m3-dynamic-color)、系统 UI 更改、与 Android 视图中的材质主题的互操作性等，并在新的`[androidx.compose.material3](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary)` [Jetpack](https://developer.android.com/jetpack) 库的帮助下实现它们。另外，请查看[Jetpack Compose with Material You # askan droid | LIVE](https://www.youtube.com/watch?v=5t40x-5vVl0&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=9)问答环节，了解更多信息。

Implementing Material You in Jetpack Compose

说到 Material You， [Rody Davis](https://medium.com/u/8787a27faf21?source=post_page-----188cb7e62e15--------------------------------) 和 [Ivy Knight](https://medium.com/u/4e20f1a59447?source=post_page-----188cb7e62e15--------------------------------) 在“ [Material You:将动态色彩应用于你的应用和品牌](https://www.youtube.com/watch?v=4bguZJwHqsQ&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=6)”中深入了解和应用动态色彩它涵盖了新的[色调调色板](https://m3.material.io/styles/color/the-color-system/key-colors-tones#a828e350-1551-45e5-8430-eb643e6a7713)，如何使用参考标记，并探索如何在您的应用程序中使用独立[和](https://material-foundation.github.io/material-theme-builder/) [Figma](https://www.figma.com/community/plugin/1034969338659738588/Material-Theme-Builder) 版本的材质主题生成器，以及[材质 3 设计套件](https://www.figma.com/community/file/1035203688168086460)。

Material You: Applying dynamic color to your app and brand

[Nick Butcher](https://medium.com/u/22c02a30ae04?source=post_page-----188cb7e62e15--------------------------------) 和 [George Mount](https://medium.com/u/d61fe6bd7eba?source=post_page-----188cb7e62e15--------------------------------) 然后带你进行一次"[深入探究 Jetpack Compose 布局](https://www.youtube.com/watch?v=zMKMwh9gZuI&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=2)"帮助你理解 Compose 的布局模型，用更高性能的代码准确构建你的应用所需的布局。它们研究了布局模型的工作原理及其功能，捆绑的布局和修改器是如何构建的，以及如何轻松地创建自定义布局和修改器。

Deep dive into Jetpack Compose layouts

然后，Kris Giesing 谈到了[设计到代码:将移交变成击掌](https://www.youtube.com/watch?v=CMC8X1qVPEw&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=5)分享我们如何解决设计师和开发人员之间 UI 混乱移交的一瞥。材料设计团队正在与 [Figma](http://figma.com) 合作，创建一个从设计到编码的工作流程，允许团队在 Figma 中创建 UI 组件，并将它们导出到 UI 包中。这个包可以在 Figma 中编辑，直接在 Android 应用程序的 Jetpack Compose 项目中使用，并且可以直接在代码中更新。有一个早期访问计划。

Design to code: Turning handoffs into high-fives

[Yuichi Araki](https://medium.com/u/d20d14d550f?source=post_page-----188cb7e62e15--------------------------------) 和[刘朵朵](https://medium.com/u/c6093e8a8d5b?source=post_page-----188cb7e62e15--------------------------------)然后讲述了我们[为 Jetpack 创作的 Android 动画](https://www.youtube.com/watch?v=Z_T1bVjhMLk&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=4)的方式，包括涵盖常见用例的基于状态的动画，以及更复杂场景的基于协程的动画。

Reimagining Android animations for Jetpack Compose

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----188cb7e62e15--------------------------------) 讲解了如何使用 [Jetpack Compose 的自动状态观察](https://www.youtube.com/watch?v=rmv2ug-wW4U&list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T&index=3)，包括 Compose 的状态持有者的基础知识，何时提升状态，以及使用 [Android ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel) 作为状态持有者。

Compose state of mind

最后， [Takeshi Hagikura](https://medium.com/u/4c602366fd32?source=post_page-----188cb7e62e15--------------------------------) 介绍了 Android 12 中对应用程序小部件的[更改，而](https://developer.android.com/about/versions/12/features/widgets) [Marcel Pintó](https://medium.com/u/55362b009039?source=post_page-----188cb7e62e15--------------------------------) 则预告了即将发布的 Glance APIs，它将允许你使用 Jetpack Compose 风格的语法来定义应用程序小部件。

Modern Android app widgets

# 疯狂技能@广告💡

![](img/23a856b806ae8531ab7d715c882273ab.png)

《疯狂技能》系列是广告的一大部分，其中有许多涵盖现代 Android 开发的新技术内容(T2)。

首先， [Tor Norbye](https://medium.com/u/8251a5f98c9d?source=post_page-----188cb7e62e15--------------------------------) 和 Jamal Eason 涵盖了“[Android Studio 中的新功能](https://www.youtube.com/watch?v=PwE5NqeeFk0)”，包括 Android 11+中的帧生命周期支持，对 Studio 中嵌入式仿真器的改进，以及带有设备类的可视林挺，以更好地支持大屏幕。Tor 还演示了新的和即将推出的 Jetpack Compose 相关功能，如支持动画拖动的交互式预览、实时文字预览支持和实时编辑。

What’s new in Android Studio

elif Bilgin[深入探讨了房间](https://www.youtube.com/watch?v=i5coKoVy1g4)中的新内容，涵盖了[自动迁移](https://developer.android.com/reference/kotlin/androidx/room/AutoMigration)，包括需要 [AutoMigrationSpec](https://developer.android.com/reference/kotlin/androidx/room/migration/AutoMigrationSpec) 的更复杂的迁移、关系查询方法、枚举类型转换器、查询回调、对[分页 3.0](https://developer.android.com/topic/libraries/architecture/paging/v3-overview)API 和 RxJava3 的支持，以及对 [Kotlin 符号处理](https://github.com/google/ksp)的实验性支持，以获得更快的编译速度。

What’s new in Room 2.4.0

[Manuel Vivo](https://medium.com/u/3b5622dd813c?source=post_page-----188cb7e62e15--------------------------------) 和 [Jose Alcérreca](https://medium.com/u/e0a4c9469bb5?source=post_page-----188cb7e62e15--------------------------------) [讲述了](https://www.youtube.com/watch?v=fSB6_KE95bU)如何将数据加载到 [Kotlin 流](https://developer.android.com/kotlin/flow)中，转换数据，在您的 UI 中向用户展示数据，以及如何测试数据，所有这些都在反应式编程的背景下进行。它们涵盖了来自`[lifecycle-runtime-ktx](https://developer.android.com/kotlin/ktx#lifecycle)` 2.4.0 的生命周期感知`[repeatOnLifecycle](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=id#(androidx.lifecycle.Lifecycle).repeatOnLifecycle(androidx.lifecycle.Lifecycle.State,%20kotlin.coroutines.SuspendFunction1))`和`[flowWithLifecycle](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=id#(kotlinx.coroutines.flow.Flow).flowWithLifecycle(androidx.lifecycle.Lifecycle,%20androidx.lifecycle.Lifecycle.State))`的使用。

Kotlin Flows in practice

James Fung [讨论了 CameraX 1.1](https://www.youtube.com/watch?v=n_oIEyiYLLk) ，包括新的[视频捕获 API](https://developer.android.com/training/camerax/video-capture) ，轻松的 YUV 到 RGB 转换，[扩展 Beta API](https://developer.android.com/training/camerax/vendor-extensions)，等等。

New features in CameraX 1.1, including video and more

[拉胡尔·拉维库马尔](https://medium.com/u/d16f91f488af?source=post_page-----188cb7e62e15--------------------------------)和[本·维斯](https://medium.com/u/65fe4f480b1c?source=post_page-----188cb7e62e15--------------------------------) [涵盖了工作管理器](https://www.youtube.com/watch?v=spdNAwsC1GQ)，包括如何请求和取消长时间运行的持久任务的基础知识，何时以及如何使用加急工作 API，如何使用[工作管理器](https://developer.android.com/topic/libraries/architecture/workmanager)编写稳定、高性能的多进程应用，以及如何在 Android Studio 中使用[后台任务检查器](https://developer.android.com/studio/inspect/task)。

WorkManager: Back to the foreground

阿曼达·亚历山大和肖恩·麦克奎蓝解释了表情符号的重要性，为什么支持最新表情符号至关重要，以及为什么你应该更新到 1.4 版本。接下来的演讲将介绍它是如何工作的，以及我们优化表情 2 的方式。

Displaying ALL the emojis in your app (and why it matters)

[Ivan Gavrilovic](https://medium.com/u/1acd3f1c7d0e?source=post_page-----188cb7e62e15--------------------------------) 和 [Scott Pollom](https://medium.com/u/ae8bcb425286?source=post_page-----188cb7e62e15--------------------------------) 讨论了如何从 Android Gradle 插件 7.0 版本的变化中受益，包括性能增强功能，如 KSP 支持、迁移到不可传递的资源类，以及构建分析器更新支持的安全配置缓存。它们涵盖了如何让你的定制 Gradle 任务与配置缓存兼容，以及如何用它的新 API 扩展 Android Gradle 插件。

Make your build faster and more robust with the latest Android Gradle plugin

唐·特纳和安德鲁·路易[推出了](https://android-developers.googleblog.com/2021/10/jetpack-media3.html) [Jetpack Media3](https://developer.android.com/jetpack/androidx/releases/media3) 的 alpha 版本，这是一个媒体播放支持库的集合，包括 [ExoPlayer](https://developer.android.com/guide/topics/media/exoplayer) 。他们解释了我们创建 Media3 的原因、它包含的内容、它提供的优势，以及如何开始在您的应用中使用它。

What’s next for AndroidX Media and ExoPlayer

最后，[现代 Android 开发#AskAndroid | LIVE](https://www.youtube.com/watch?v=QreLkok3Euk) 会议回答了关于架构组件、Kotlin、Android Studio 和性能的问题。

Modern Android Development #AskAndroid | LIVE

# 跨屏幕构建

除了手机、平板电脑和 Chrome OS 笔记本电脑之外，Android 还可以在许多外形规格上使用，包括[手表](https://developer.android.com/wear)、[电视](https://developer.android.com/tv)和[汽车](https://developer.android.com/cars)，使您能够利用您的 Android 开发技能将您的内容带到各种设备上。

首先，Maia Conrado 和 [Jeremy Walker](https://medium.com/u/73335236659e?source=post_page-----188cb7e62e15--------------------------------) 介绍了新的 [Compose for Wear OS](https://developer.android.com/training/wearables/compose-setup) 开发者预览版，展示了与移动版 Jetpack Compose 的相似之处、不同之处以及附加功能，因此您可以利用自己的 Compose 技能，用更少的代码快速开发自己漂亮的 Wear OS 应用程序。

From mobile to Wear OS: Learn how to create a Compose app for the wrist

然后 Jay Yoo 和 Stav Raviv 解释了如何使用[汽车应用程序库](https://developer.android.com/training/cars/apps)将您的导航、停车或充电应用程序带到 1 亿多辆运行 Android Auto 的汽车上，以及越来越多支持 Android Automotive OS 的制造商。

Bringing your apps to cars

最后，[万由里·金瓦萨拉](https://medium.com/u/678c18c97ff4?source=post_page-----188cb7e62e15--------------------------------)和布莱恩·林达尔谈到了通过[观看下一个 API](https://developer.android.com/training/tv/discovery/watch-next-add-programs) 在[安卓电视](https://developer.android.com/tv)和谷歌电视上呈现用户未完成的和后续的内容，以及如何使用安卓的 API 来播放最高质量的音频和视频。

Create engagement and high quality playback on Android TV & Google TV

# Android 游戏开发🎮

[我的会议](https://www.youtube.com/watch?v=U1WIy7kJG3M&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=3)涵盖了我们为简化游戏开发所做的工作，包括 [Android 游戏开发套件或 AGDK](https://developer.android.com/games/agdk) 。它强调了 Android Studio 为 [NDK 开发](https://developer.android.com/ndk)带来的基础知识，使用 Visual Studio 通过 [Android 游戏开发扩展](https://developer.android.com/games/agde)开发 Android 游戏，并使用 AGDK C/C++库进行开发，如 [GameActivity](https://developer.android.com/games/agdk/integrate-game-activity) 、[gamtext input](https://developer.android.com/games/agdk/add-support-for-text-input)和[游戏控制器库](https://developer.android.com/games/sdk/game-controller)。我还讲述了将游戏带到更多屏幕上的基础知识，比如电视、Chromebooks、手机和平板电脑。最后，您将了解如何利用[游戏资产交付](https://developer.android.com/guide/playcore/asset-delivery)、 [Android 性能调优器](https://developer.android.com/games/sdk/performance-tuner)、 [Reach 和设备](https://android-developers.googleblog.com/2021/07/plan-for-success-on-google-play-with.html)、 [Android GPU 检查器](https://developer.android.com/agi)和[游戏服务](https://developers.google.com/games/services)。

[Streamlined game development that reaches more screens](https://www.youtube.com/watch?v=U1WIy7kJG3M&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=3)

# Google Play🏬

最后，我们介绍了我们在 [Google Play](https://developer.android.com/distribute) 中所做的事情，以帮助将您的应用或游戏带给最终用户，以及您需要了解的变化。

在“[Google Play](https://www.youtube.com/watch?v=QUwPzUCjqO8&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=1)的新功能”环节中，Lucy Hughes 介绍了 Google Play 的新功能和更新功能，如 [Android 的重要功能](https://developer.android.com/topic/performance/vitals)。[评级和评论](https://support.google.com/googleplay/android-developer/answer/138230)，Reach 和设备，以及 [Play 付费订阅的应用内消息](https://developer.android.com/google/play/billing/subscriptions)。她还谈到了我们在信任和安全方面的改进，游戏的更新，以及新的[商店列表证书课程](https://play.google.com/academy/certificate/)。

What’s new in Google Play

[Dom Elliott](https://medium.com/u/ff0b1fb4e6b6?source=post_page-----188cb7e62e15--------------------------------) 和 soléne matre 介绍了即将推出的 [Play Integrity API](https://developer.android.com/google/play/integrity) 。它允许您确定您的后端服务是否正在与 Google Play 安装在正版 Android 设备上的正版二进制文件进行交互，以帮助您处理欺骗、篡改、欺诈、盗窃、未经授权的使用等问题。

Introducing Play Integrity API: Protect your apps and games

# 那么现在…

这就是我们这次 [Android 开发者峰会的第二部分](https://developer.android.com/events/dev-summit)更新来自 [Jetpack Compose](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9-BJh8Os6W6iQIp1gtOh2T) 、 [Material You](https://m3.material.io/) 、 [MAD Skills](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-W96eOIFpda-rvYCRnoGpJ) 、 [Android TV](https://www.youtube.com/watch?v=XvGLD6fLrIU&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4&index=3) 、 [Android Auto/Automotive](https://www.youtube.com/watch?v=ELeNmFrm4vM&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4&index=2) 、 [WearOS](https://www.youtube.com/watch?v=B7D3G6tC9n0&list=PLWz5rJ2EKKc_ACsRPeIsFA1EUGFrqjxL4&index=1) 、[游戏开发](https://www.youtube.com/watch?v=U1WIy7kJG3M&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=3)和 [Google Play](https://www.youtube.com/watch?v=QUwPzUCjqO8&list=PLWz5rJ2EKKc8y4ROiZ3ASodSCKrj-yHhY&index=1) 。请尽快回到这里，等待 Android 开发者世界的下一次更新。