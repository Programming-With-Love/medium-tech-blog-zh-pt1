# 现在在 Android #38 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-38-588b043094?source=collection_archive---------5----------------------->

![](img/5999a393c05bb57aaa275375aab6d029.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## Google I/O 2021、RenderScript 弃用、#MADSkills 导航剧集、文章和播客剧集

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 视频和播客形式的 NiA38

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

# Android 12:开发者预览版 3

![](img/fb0a1d5f81cf4fc4b6b1949545fa21d2.png)

Android 12 的第三个[开发者预览版现已发布。](https://developer.android.com/about/versions/12)

阅读[的博客](https://android-developers.googleblog.com/2021/04/android-12-developer-preview-3.html)，了解开发者版本中的新内容，从一致和可定制的启动体验到触觉效果和 API，到相机功能，等等。

# 谷歌输入/输出 2021:免费，为每个人

![](img/06d2897d94880815d2eedf99c20dc315.png)

Google I/O 又回来了，只不过这次完全不同。整个活动都是在线的，每个人都可以参加。不要再刷新网站来看你是否能在票卖完之前买到票！就[注册](https://events.google.com/io/?lng=en&step=0)加入我们吧！

# RenderScript @已弃用

我们放弃 RenderScript APIs，转而支持跨平台的 GPU 计算解决方案，如 Vulkan 和 OpenGL，并鼓励希望利用 GPU 计算的开发人员转向这些其他 API。

由于 RenderScript 最常用于图像处理的内在特性，例如模糊，我们提供了一个具有 RenderScript 大部分内在功能的[开源库](https://github.com/android/renderscript-intrinsics-replacement-toolkit)。

查看 [Daniel Galpin](https://medium.com/u/2e0fc9a4a8c2?source=post_page-----588b043094--------------------------------) 的[文章](https://android-developers.googleblog.com/2021/04/android-gpu-compute-going-forward.html)了解更多细节，以及关于将从 RenderScript 迁移到 Vulkan 的[文档。](https://developer.android.com/guide/topics/renderscript/migrate)

[](https://android-developers.googleblog.com/2021/04/android-gpu-compute-going-forward.html) [## Android GPU 计算向前发展

### 我们在 Android 3.0 中引入了 RenderScript，作为应用程序在 CPU 或…上运行计算密集型代码的一种方式

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/04/android-gpu-compute-going-forward.html) 

# 疯狂技能:导航

![](img/bfaf0586245c9646970e76ed816d47c7.png)

[MAD Skills](https://developer.android.com/series/mad-skills) 系列继续滚动，关于现代 Android 开发的技术内容。

[Murat Yener](https://medium.com/u/e947fef0dfe0?source=post_page-----588b043094--------------------------------) 已经开始了[新系列上的导航部件](https://www.youtube.com/watch?v=4efQJ0vcHWA&list=PLWz5rJ2EKKc-vin7SvgoaR6wu24sAw-sE):

该系列建立在[之前的导航系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9VpBMZUS9geQtc5RJ2RsUd)之上。最初的系列使用甜甜圈跟踪应用程序来展示如何使用一些导航功能，如对话框目的地、安全参数和深层链接。第二个系列扩展了原来的示例，增加了跟踪咖啡数据的能力(因为，没有咖啡，你怎么能吃油炸圈饼呢？)

第一集讲述了在应用程序 UI 的不同元素之间导航，如操作栏、抽屉或底部标签，所有这些都通过导航组件和菜单 id 变得更容易。

或者对于喜欢阅读他们的内容的人，这里是以文章的形式:

[](/androiddevelopers/navigationui-d21fd4f5c318) [## 导航 UI

### 这是关于导航的第二篇 MAD 技巧系列文章。在本文中，我们将了解另一个使用案例，其中…

medium.com](/androiddevelopers/navigationui-d21fd4f5c318) 

随着 Murat 继续探索导航 API 的其他领域，请继续关注更多的导航内容。

## 但是等等，还有呢！

对于正在进行的内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。

# 文章

## 包装可见性

Android 11 中与隐私相关的变化之一是包的可见性。具体来说，默认情况下，应用程序不再可能获得其他已安装应用程序的信息。相反，应用程序需要在其清单中声明它们想要访问哪些应用程序。或者，应用程序总是可以使用意图启动来完成任务，而不是调用碰巧安装的特定应用程序。

关于这如何工作以及如何在 Android 11 和更高版本中迁移到这一要求的更多细节，请查看 [Meghan Mehta](https://medium.com/u/401951cd4c3e?source=post_page-----588b043094--------------------------------) 的博客:

[](/androiddevelopers/working-with-package-visibility-dc252829de2d) [## 使用包可见性

### 在 Android 中，我们正在进行更改，以增强用户隐私和平台安全性，为我们的用户提供更安全的…

medium.com](/androiddevelopers/working-with-package-visibility-dc252829de2d) 

## 数据存储

[DataStore](https://developer.android.com/topic/libraries/architecture/datastore) 是一个新的 AndroidX 库，目前在 alpha 中，旨在取代共享偏好作为一种轻量级存储机制。默认情况下，数据存储使用[协议缓冲区](https://developers.google.com/protocol-buffers/docs/overview)来序列化存储的数据。Rohit 的文章展示了如何使用不可变的 Kotlin 数据类和序列化。

[](/androiddevelopers/using-datastore-with-kotlin-serialization-6552502c5345) [## 通过 Kotlin 序列化使用数据存储

### 到目前为止，我们已经分享了如何使用 Protos 或 Preferences 的数据存储。两个数据存储版本都使用…

medium.com](/androiddevelopers/using-datastore-with-kotlin-serialization-6552502c5345) 

## 迁移空间

[弗洛里纳·蒙特内斯库](https://medium.com/u/d5885adb1ddf?source=post_page-----588b043094--------------------------------)发表了一篇关于最新版本的 Room`[2.4.0-alpha01](https://developer.android.com/jetpack/androidx/releases/room#2.4.0-alpha01)`中新的*自动迁移*功能的文章。使用这个特性可以让您更容易地在不同版本之间迁移数据库，让 Room 为您处理细节。仍然存在自动迁移不可行的情况，因此您可能仍然需要现有的机制和 API 来进行手动迁移；详情请看这篇文章。

[](/androiddevelopers/room-auto-migrations-d5370b0ca6eb) [## 房间自动迁移

### 轻松在房间间移动桌子

medium.com](/androiddevelopers/room-auto-migrations-d5370b0ca6eb) 

# 播客剧集

![](img/e18c62ad869312fdedcdaad8891a68be.png)

# 亚洲开发银行 160:艺术史

[Romain Guy](https://medium.com/u/c967b7e51f8b?source=post_page-----588b043094--------------------------------) 、 [Tor Norbye](https://medium.com/u/8251a5f98c9d?source=post_page-----588b043094--------------------------------) 和我与艺术团队的 Brian Carlstrom 和 Nicolas Geoffray 讨论了艺术的早期原型和制作、制作准备以及最近的运行时开发。

# 那么现在…

这次到此为止。所以去看看[新的导航疯狂技能系列](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc-vin7SvgoaR6wu24sAw-sE)！阅读关于 [RenderScript 弃用](https://android-developers.googleblog.com/2021/04/android-gpu-compute-going-forward.html)和检查新的[图像处理库](https://github.com/android/renderscript-intrinsics-replacement-toolkit)！在导航组件上观看[最新#MADSkills 系列](https://www.youtube.com/watch?v=4efQJ0vcHWA&list=PLWz5rJ2EKKc-vin7SvgoaR6wu24sAw-sE)！阅读 Android 11+中的[包可见性](/androiddevelopers/working-with-package-visibility-dc252829de2d)、使用 Kotlin 序列化的[序列化数据存储内容](/androiddevelopers/using-datastore-with-kotlin-serialization-6552502c5345)以及[房间自动迁移](/androiddevelopers/room-auto-migrations-d5370b0ca6eb)！请收听最新的 [ADB 播客](http://adbackstage.libsyn.com/)，并尽快回到这里收听来自 Android 开发者世界的下一次更新。