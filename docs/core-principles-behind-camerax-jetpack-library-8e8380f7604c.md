# CameraX Jetpack 库背后的核心原则

> 原文：<https://medium.com/androiddevelopers/core-principles-behind-camerax-jetpack-library-8e8380f7604c?source=collection_archive---------0----------------------->

在这篇博文中，我们将介绍 CameraX Jetpack 库背后的基本原则，以及该库自[在 Google I/O 2019 上发布](https://www.youtube.com/watch?v=kuv8uK-5CLY)以来的一些变化。要获得最新资源，请查看[文档](http://developer.android.com/camerax)，查看[官方样本](http://g.co/camerax/sample)，并加入我们的[在线开发者社区](http://g.co/camerax/developers)。

![](img/7fda3c3f31ea3ce165109c95f3cfa978.png)

# 生命周期意识

与许多其他 Jetpack 库一样，CameraX 的核心特性之一是生命周期感知。只要我们提供一个生命周期所有者，CameraX 就可以代替我们管理相机设备和会话的打开和关闭。当生命周期开始时，相机也开始了；当生命周期因为我们的应用、活动或片段的消失而停止时，我们也不必担心关闭，因为 CameraX 为我们做了这些。不错！

# 用例驱动的方法

CameraX Jetpack 库的另一个核心原则是它的**用例驱动方法**。CameraX 试图在非常低级的抽象(如 Camera2 的[捕获请求模板](https://developer.android.com/reference/android/hardware/camera2/CameraDevice#constants_2))和过于简单的 API(如已废弃的[相机 API](https://developer.android.com/reference/android/hardware/Camera.html) )之间找到平衡点。

CameraX 提供的主要用例有:

*   [**预览**](https://developer.android.com/training/camerax/preview) :用于显示相机正对的取景器。
*   [**图像分析**](https://developer.android.com/training/camerax/analyze) :用于解析来自摄像头的信息，例如检测画面中的人脸。
*   [**ImageCapture**](https://developer.android.com/training/camerax/take-photo) :用于拍摄高质量照片，可根据应用要求以高分辨率或低延迟完成。

用例是使用配置对象构建的。API 在各种用例中都是一致的，开发人员可以通过一个简单的三步流程来实现这一点:

注意，每个用例都是完全独立的。这意味着，理论上，如果我们愿意，可以为我们的每个活动案例设置不同的分辨率、纵横比，甚至生命周期所有者。在我们的[官方示例](https://github.com/android/camera/tree/master/CameraXBasic)中，我们保持简单，对所有用例使用相同的参数。

# 优雅的退化

尽管从技术上来说，我们可以为不同的用例拥有不同的生命周期所有者，但是建议我们在一次调用中通过 CameraX 将我们的用例绑定到生命周期，并且使用相同的生命周期所有者:

背后的原因是，如果我们要求的不可行，CameraX 将尝试执行**优雅降级**并提供最接近的可能配置。通过一次提供所有的用例，我们给 CameraX 一个机会来找到一个折衷方案，这样所有的用例都可以运行。当摄像机会话已经处于活动状态时，提供用例会逐渐增加约束，可能需要重新启动会话，这可能会导致正在进行的摄像机流出现故障。

特定配置不可行的原因有很多，这取决于 Camera2 API 可以提供什么，而这在不同的设备之间是不同的。其中一个主要的制约因素与相机流的限制有关，我们[在之前的帖子](/androiddevelopers/using-multiple-camera-streams-simultaneously-bf9488a29482)中解释过。不幸的是，在 CameraX 中，目前没有好的方法可以知道一个配置组合是否会成功，而不需要一定量的尝试和错误。

# 和睦相处

谈论 Android 上的**兼容性**通常指的是 API 级别。幸运的是，CameraX 所基于的 Camera2 APIs 自其在 API 级别 21 中引入以来一直相当稳定，CameraX 支持 21 及以上版本的设备——这代表了大约 90%的 Android 设备[(截至 2019 年 8 月)。](https://developer.android.com/about/dashboards/index.html)

然而，即使在同一个 API 级别中，不同设备之间也有很多不同之处；例如 [HAL 级别](https://source.android.com/devices/camera/camera3)和对不同[像素格式](https://developer.android.com/reference/android/graphics/ImageFormat)的不同支持会对性能产生很大影响。

为了解决这个问题，CameraX 团队在自动化测试方面投入了大量资金，包括建立一个拥有一系列设备的专用测试实验室。所有的目标是，当我们选择一个经过测试的用例配置组合时，它对我们所有的用户都有效。

![](img/1b59cd409c9c95f00d834c1f27b3c2b8.png)

Automated CameraX test lab

# 扩展ˌ扩张

让 CameraX 在许多不同的设备上工作不是一件容易的事情。为了帮助解决这个问题，Google 与许多 OEM 合作伙伴合作开发上述核心用例的扩展。文档最好地描述了:

> CameraX 提供了一个 API 来访问特定于设备的供应商效果，如散景、HDR 和附加功能。API 使您能够查询特定的扩展在当前设备上是否可用，并优先启用该扩展。也就是说，如果扩展在该设备上可用，它将被启用，如果不可用，它将正常降级。

启用一个用例的扩展，比如图像捕获，可能会修改所有其他活动的用例。换句话说，启用 HDR 扩展可能会修改预览和图像分析用例，而不仅仅是图像捕获用例。注意，目前(从扩展模块的 alpha01 版本开始)，这是通过确保所有活动用例都启用了扩展来实现的。我们可以使用一个 [ExtensionsErrorListener](https://developer.android.com/reference/androidx/camera/extensions/ExtensionsErrorListener.html) 来设置一个错误监听器，尽管这个设计将来可能会改变。

![](img/e729c6c5bced11f04edc262f05cfa502.png)

Showcase of the different extensions currently available

# 最近的 API 变化

自从 Google I/O 发布以来，已经发布了一些新的 API，其他一些也有所改变:

*   [**厂商扩展**](https://developer.android.com/training/camerax/vendor-extensions) 现已可用。为了在我们的应用程序中包含工件，我们将以下内容添加到我们的 Gradle dependencies: `implementation "androidx.camera:camera-extensions:1.0.0-alpha01"`(或更高)
*   用例现在接受 [**执行者**](https://developer.android.com/reference/java/util/concurrent/Executor) 而不是处理者。简单地调用用例配置对象上的`setBackgroundExecutor`。
*   图像分析现在允许 [**非阻塞**](https://developer.android.com/reference/androidx/camera/core/ImageAnalysis.ImageReaderMode) 操作，无需在应用程序级别进行节流以避免资源匮乏。
*   **相机属性**将通过一个新的 CameraInfo 对象可用。这特别有帮助，因为备选方案是退回到 Camera2 APIs 和*猜测*哪个相机设备对应于所选的镜头朝向属性。
*   **相机控件**，比如变焦和对焦，包括一些非常方便的点击对焦工具，都可以通过 [**CameraControl**](https://developer.android.com/reference/androidx/camera/core/CameraControl.html) 类获得。

# 了解更多信息

要了解更多关于 CameraX 的信息，请查看[文档](http://developer.android.com/camerax)和[官方样本](http://g.co/camerax/sample)，或者加入我们的[在线开发者社区](http://g.co/camerax/developers)。请继续关注未来的博客帖子和更新，因为 CameraX 正在从 alpha 走向生产就绪库。