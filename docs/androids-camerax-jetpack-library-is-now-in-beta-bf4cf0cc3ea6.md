# Android 的 CameraX Jetpack 库现在处于测试阶段！

> 原文：<https://medium.com/androiddevelopers/androids-camerax-jetpack-library-is-now-in-beta-bf4cf0cc3ea6?source=collection_archive---------0----------------------->

![](img/49b58502ca76b981f9f359307204e020.png)

我们非常兴奋地与大家分享，CameraX 现已推出测试版！我们要感谢让这一切成为可能的整个开发者社区。在这个版本中，你会发现一个相机 API 基础，它将保持不变，并通过 bug 和设备兼容性修复随着时间的推移而变得更好。

除了扩展测试之外，我们有许多在生产中使用 CameraX 的应用程序，这让我们有信心推荐开发人员开始试验，并(如果合适的话)在 Play Store 上推出 CameraX。我们预计会有一些错误，并致力于确保您的应用程序拥有最佳的相机体验。如果您需要帮助，请通过[讨论邮件列表](http://g.co/camerax/developers)或[打开问题](http://issuetracker.google.com)联系我们寻求支持。

在这篇博客文章中，我们将简要介绍 CameraX Jetpack 库，并分享 Beta 版中的新功能。达到 beta 状态意味着 Jetpack 库被认为可以投入生产使用，但可能仍然包含一些非关键的错误。

要获得最新资源，请查看[文档](http://developer.android.com/camerax)，查看[官方样本](http://g.co/camerax/sample)，并加入我们的[在线开发者社区](http://g.co/camerax/developers)。

![](img/8ef25fbad5aa78241d183712d8cbcfb2.png)

CameraX logo

# CameraX 摘要

首先，让我们快速回顾一下 CameraX 是什么。如果你已经熟悉 CameraX，你可以跳过这一节。你也可以看看其他资源比如[本次 Android 开发者峰会](https://www.youtube.com/watch?v=49lU1NtYE5g)或者其[相应的博文](/androiddevelopers/whats-new-in-camerax-fb8568d6ddc)。

## 设备兼容性

作为一个 Jetpack 库，CameraX 不仅兼容运行 API level 21 及以上的 Android 设备，还兼容各种硬件设备，无论是外形、相机配置还是设备实现细节。

## 生命周期意识

CameraX 的一个核心特性是[生命周期感知](https://developer.android.com/topic/libraries/architecture/lifecycle)。我们只需将相机用例绑定到生命周期所有者，而不必手动打开和关闭相机。当相关的生命周期所有者(活动、片段等。)启动或停止，相机也是如此。

## CameraX 用例

作为测试版的一部分，CameraX 提供的主要用例有:

*   [**预览**](https://developer.android.com/training/camerax/preview) :用于显示相机指向的取景器。
*   [**ImageAnalysis**](https://developer.android.com/training/camerax/analyze) :用于解析来自摄像机流的信息。
*   [](https://developer.android.com/training/camerax/take-photo)**:用于拍摄高质量照片。**

**![](img/c8b41f07c474b1884714aa44f008b987.png)**

**Portrait mode**

# **Beta 版有什么变化？**

**自从我们上次讨论 CameraX 以来，一些事情已经发生了变化，这些变化可以在[文档](http://developer.android.com/camerax)、[官方样本](http://g.co/camerax/sample)或我们的[在线开发者社区](http://g.co/camerax/developers)中找到。**

## **初始化**

**CameraX 必须由开发人员通过 [ProcessCameraProvider](https://developer.android.com/reference/androidx/camera/lifecycle/ProcessCameraProvider) 显式初始化，看起来像这样:**

```
val cameraProviderFuture: ListenableFuture<ProcessCameraProvider> =
    ProcessCameraProvider.getInstance(context)// The listener will be triggered once the future’s value is ready
cameraProviderFuture.addListener(Runnable { // Camera provider is now guaranteed to be available
    val cameraProvider = cameraProviderFuture.get()
    …
}, executor)
```

## **挑选相机**

**通过[相机选择器](https://developer.android.com/reference/androidx/camera/core/CameraSelector)来选择哪台相机将用于我们的用例。这是通过创建一个带有一组可选约束的`CameraSelector`对象来实现的，然后 CameraX 将挑选最适合这些要求的可用摄像机:**

```
val cameraSelector = CameraSelector.Builder()
    .requireLensFacing(CameraSelector.LENS_FACING_BACK)
    .build()
```

**如果你想要一个简单的选择器来选择正面或背面，你可以使用静态字段 [CameraSelector。DEFAULT_BACK_CAMERA](https://developer.android.com/reference/androidx/camera/core/CameraSelector#DEFAULT_BACK_CAMERA) 或[CAMERA select。默认 _ 前置 _ 摄像头](https://developer.android.com/reference/androidx/camera/core/CameraSelector#DEFAULT_FRONT_CAMERA)。**

## **相机预览**

**尽管它在技术上不是测试版的一部分，但视图模块现在在 alpha08 中，并且[预览视图](https://developer.android.com/reference/androidx/camera/view/PreviewView)是实现相机预览的推荐方式。首先，将它添加到 XML 布局中:**

```
<androidx.camera.view.PreviewView
    android:layout_width=”match_parent”
    android:layout_height=”match_parent”/>
```

**然后，确保您的预览用例使用来自预览视图的表面:**

```
val preview: Preview = …
val viewFinder: PreviewView = … // for example findViewById()
preview.setSurfaceProvider(viewFinder.previewSurfaceProvider)
```

**如果你想使用你自己的表面(例如，从`TextureView`开始)，那么你必须[实现你自己的表面提供者](https://developer.android.com/training/camerax/preview#manual)，同时确保你适当地处理尺寸和方向，这可能很棘手。**

## **相机控制**

**您可以使用由[camera process provider . bindtolifecycle()](https://developer.android.com/reference/androidx/camera/lifecycle/ProcessCameraProvider#bindToLifecycle(androidx.lifecycle.LifecycleOwner,%20androidx.camera.core.CameraSelector,%20androidx.camera.core.UseCase...))返回的 camera 对象来查询和修改相机的某些方面，如聚焦、变焦和闪光灯。例如，你可以通过`CameraInfo`对象得到一个带有最新摄像机状态的`LiveData`对象，如下所示:**

```
val camera = cameraProvider.bindToLifecycle(…)
val zoomState: LiveData<ZoomState> = camera.cameraInfo.zoomState
val torchState: LiveData<Int> = camera.cameraInfo.torchState
```

**相反，您可以使用相应的`CameraControl`对象来控制这些值:**

```
val camera = cameraProvider.bindToLifecycle(…)
camera.cameraControl.enableTorch(true)
camera.cameraControl.setLinearZoom(0.5f)
camera.cameraControl.startFocusAndMetering(…)
```

# **下一步是什么？**

**我们承认相机开发是困难的，并致力于通过以下方式使 CameraX 更容易使用:( a)继续投资 CameraX 测试套件;( b)向我们的自动化测试场添加新设备;( c)处理内部和外部的错误报告。我们鼓励您开始使用 CameraX 测试版，并加入我们，为我们所有的用户改进 Android 上的相机。**

**要了解更多关于 CameraX 的信息，请查看[文档](http://developer.android.com/camerax)和[官方样本](http://g.co/camerax/sample)，或者加入我们的[在线开发者社区](http://g.co/camerax/developers)。敬请关注未来的博客帖子和更新！**