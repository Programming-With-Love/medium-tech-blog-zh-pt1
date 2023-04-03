# 开始使用 CameraX 在 Android 上构建相机应用程序

> 原文：<https://blog.kotlin-academy.com/getting-started-with-camerax-in-building-camera-apps-on-android-ec5416a66c9?source=collection_archive---------0----------------------->

![](img/0c39192ef6e0fc8486c5d056aadbc190.png)

Photo by [Julius Drost](https://unsplash.com/@juliusdrost?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

对于 Android 开发人员来说，在 Android 上开发一个使用 Instagram 或 Snapchat 等相机功能的应用程序一直是一个麻烦，部分原因是设备制造商众多，设备制造商范围广泛。

如果你的兴趣不是控制相机，你只是需要拍照，最简单的做法是启动相机意图拍照。尽管如此，如果你需要完全控制相机，那么 CameraX 是你 Android 开发的首选库。CameraX 让你免去了为众多 Android 设备的相机与不同制造商打交道的烦恼，并专注于开发相机应用程序。

## 早期剧透警报

在我们进一步讨论之前，我想提醒您注意，CameraX 的大部分开发都是围绕构建器模式进行的。光是这种理解就能帮你想出很多东西。使用 ***构建器模式*** ，复杂对象将其创建委托给特定的**构建器类**而不是其自身。一旦创建(或构建)了对象，就不能对其进行更改。因此，大多数时候，您会看到类似下面这样的语句。

Builder pattern calls

像[改型](https://square.github.io/retrofit/)这样的库使用这种模式。

## 返回 CameraX 步骤

当你想使用相机时，需要的步骤是启动相机应用程序(可能首先授予相机权限)，预览图像帧，然后拍摄照片或录制视频。我将向您解释这些相同的步骤，但首先，您必须在 Android Studio 中创建一个**空活动**项目，并在 app-level **build.gradle** 文件的 dependencies 块中添加必要的 CameraX 依赖项。

CameraX dependencies

整个相机-*是为了相机功能和未来需要我们使用协程[**wait**](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-guava/kotlinx.coroutines.guava/await.html)on[**ListenableFuture**](https://developer.android.com/guide/background/listenablefuture#kts)**。**

## 将预览视图添加到 MainActivity

为了让我们预览图像帧，我们需要在 **activity_main.xml** 中的 PreviewView 视图，并在 **MainActivity.kt** 中引用它。我们还将添加一个拍照按钮。

PreviewView in activity_main.xml

## 声明用例并初始化预览视图

在 **MainActivity.kt** 中，我们的兴趣是预览图像帧，并拍照，因此我们需要两个用例，

**预览**用例以及**图像捕捉**用例。我们也将在这里引用来自**activity _ main . XML**的预览视图。

我们要做的下一件事是创建预览用例，并将 PreviewView 绑定到 **Preview** 用例。本质上，一个**预览**需要一个表面，在这个表面上预览将被渲染，因此我们将预览视图的表面设置为这个表面。为此，让我们创建一个名为 i **nitPreview()** 的简单方法来做到这一点。

注意，正如我前面解释的，在创建预览时使用了构建器模式。这将是一个相同的模式。在这一点上，你期望如果你在 **onCreate** 方法中调用 **initPreview** ，当你指向一个相机时，你应该看到图像帧，但是不要太快，我们必须请求相机许可才能看到预览。为此，我们将首先在 **MainActivity.kt** 中创建另一个名为**requestCameraPermission()**的方法。在 onCreate 中，我们会在启动相机之前请求许可。这样，我们将需要另一个叫做 **startCamera()** 的方法。

## 选择要使用的摄像机

在我们的 **startCamera()** 方法中，我们必须像上面在 **initPreview()** 方法中一样初始化预览用例，然后选择一个要使用的相机(创建 CameraSelector 用例)，然后通过调用*processcameraprovider . getinstance(this @ main activity)创建一个相机提供者。await()* 并最终将相机提供者绑定到一个**生命周期所有者**。相机状态为打开、启动或关闭，用例仅在相机启动时接收相机数据。

这里你需要再次注意的是 **CameraSelector** 仍然是按照构建器模式构建的，并且 **cameraProvider** 的创建发生在 **lifecycleScope.launch** 中，因为**等待**是一个挂起函数。我们只是决定使用 CameraSelector 的 Builder**requireLensFacing()**方法默认使用后置摄像头。然后我们继续解除所有用例的绑定，如果有绑定的话，然后调用方法**camera provider . bindtolifecycle()**。 **bindToLifeCycle** 方法返回一个 **Camera** 对象，并接受一个生命周期所有者、CameraSelector 和最后一个参数作为各种用例，对于当前用例，我们将只传递预览用例。有了所有这些设置，开始检查我们是否有相机许可，我们启动相机，如果我们没有，我们请求它。我们也不会忘记在 MainActivity 中覆盖**onRequestPermissionResult**方法，以便在授权结果不为空并且权限被授予的情况下启动摄像机。

onRequestPermissionResult.

完成所有这些设置后，调用 **onCreate** ()中的**requestCameraPermission**方法，在你的设备或模拟器上运行该应用程序后，当你接受相机权限时，你应该会看到一个预览。不过，我更希望你使用真正的设备。

## 捕获图像并在 ImageView 中显示

你已经能够看到图像的框架，但我们更感兴趣的是用相机来拍照。我们通过构造一个 **ImageCapture** 用例并调用它的 **takePicture()** 方法来实现这一点。为此，我们将创建一个方法 **initImageCapture** ，我们将在 startCamera 方法的 **initPreview** 中的行之后调用这个方法。对于我们的例子，takePicture 方法有两个参数，即一个执行器和一个 **ImageCapture。OnImageCapturedCallback** 。回调有两个方法，分别是 **onCaptureSuccess** 和 **onError** 。当拍照成功时调用 **onCaptureSuccess** 方法，否则调用 onError。在我们做任何事情之前，我们将首先向 XML 布局文件添加一个 ImageView。当捕获成功时，我们将显示它(使它可见)。

ImageView in activity_main.xml

图像捕获的拍照实现和生命周期绑定如下所示。

在 initImageCapture 中，我们构建了 ImageCapture 用例，并将图片质量设置为 100%。接下来我们要担心的是扩展函数 convertImageProxyToBitmap(这里我就不解释了)

正如您所看到的，startCamera 方法没有太多变化，但值得注意的是 ImageCapture 的初始化和对 bindLifecycleOwner 的调用有一个额外的 **imageCapture** 用例。如前所述，我们可以传递可变数量的用例。

convert image proxy to bitmap

当我们在 **onCaptureSuccess** 中将图像代理转换为位图时，我们将位图设置为 imageView。请注意，有一个不同版本的**拍照**可以拍摄图像。FileOutputOptions、Executor 和一个 an **OnImageSavedCallback** 。您可能有兴趣将拍摄的照片保存到您的磁盘上，因此您可能需要添加存储权限以在磁盘上创建图像文件(我将让您研究这一点)。

我们感兴趣的下一个用例是**图像分析**和**视频捕捉**。使用 ImageAnalysis，您可能希望检测预览中所有面部的微笑，并检测面部何时哭泣或其他面部表情。这当然需要某种机器学习或人工智能。我们不会处理这两个用例，但我们宁愿使用 CameraX 扩展。

## 主要通话

因此，为了总结使用 CameraX 捕获图像所需的主要调用，主要调用如下所示；

CameraX main calls

## 用肖像、夜间模式和 HDR 扩展 CameraX。

有些手机支持人像模式、夜间模式和 HDR 等效果。在肖像模式下，要拍摄的主要对象具有焦点，而背景中的其他对象则模糊不清。夜间模式使照片在低光照强度下更清晰，而 HDR 代表高清晰度分辨率。当然，我们可以通过 cameraX 使用一个**扩展管理器**和我们创建的摄像机选择器和摄像机提供器来访问它。我们将调用**extensions manager . getinstance async(this @ main activity，cameraProvider)。await()** 恰好也是一个挂起函数，因为**extensions manager . getinstance async(this @ main activity，cameraProvider)** 返回一个 ListenableFuture，就像**processcameraprovider . getinstance(this @ main activity)**一样。

接下来，我们将检查特定的摄像机是否支持我们希望使用的效果，如果支持，我们将创建该效果的 cameraSelector 并使用它来代替我们传统的 CameraSelector。因此，我们使用肖像模式或散景效果的方法如下。

对**extension manager . isextensionavailable**的调用使用我们之前创建的基本摄像机选择器，我们将效果指定为第二个参数。如果该扩展在该设备上受支持，我们使用**extensions manager . getextensionenabledcameraselector**创建一个散景相机选择器，将基本相机选择器和模式作为第二个参数传递。我们将使用 bokehCameraSelector，而不是使用基本相机选择器。设置完整源代码后，设置**浮动操作按钮**调用 MainActivity 中的拍照。完整的源代码可以在 这里找到 [**。**](https://github.com/ngengesenior/MyCameraApp)

有了这个基本功能，你就可以扩展你的 cameraX 应用程序，让它做的不仅仅是拍照。你可能想跳到 [CameraX 文档](https://developer.android.com/training/camerax)去了解更多。

## 谢谢你

如果你读到这里，谢谢你，不要忘记分享，你可以订阅我的简讯，也许你会想点击关注按钮，给我一些大拇指。

[![](img/68557afd7e8ae2a75dec26b78d8ec016.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)