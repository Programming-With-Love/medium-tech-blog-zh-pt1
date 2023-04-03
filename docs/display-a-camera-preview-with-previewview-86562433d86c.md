# 用预览视图显示相机预览

> 原文：<https://medium.com/androiddevelopers/display-a-camera-preview-with-previewview-86562433d86c?source=collection_archive---------0----------------------->

![](img/c664f94a21456ad37f578a38e60da765.png)

## Android Jetpack CameraX:预览视图

任何相机应用程序的一个常见用例是显示相机的预览。到目前为止，这很难做到，主要是因为 camera2 API 边缘案例和各种设备行为的复杂性。`PreviewView`是 [CameraX Jetpack 库](https://developer.android.com/training/camerax)的一部分，通过在各种 Android 设备上提供开发者友好、一致和稳定的 API，使开发者更容易显示相机预览。

# **介绍预览视图**

`PreviewView`是一个自定义的`View`，可以显示一个摄像头的输入。它的建立是为了减轻设置和处理相机使用的预览表面的负担。

如果您需要在应用程序中显示基本的相机预览，推荐使用`PreviewView`方式，因为它是:

*   **更容易使用** : `PreviewView`是一个`View`。它通过管理`[Preview](https://developer.android.com/reference/kotlin/androidx/camera/core/Preview.html)`用例使用的`Surface`来实现所有必要的工作，以显示摄像机在你的布局中看到的内容。
*   **轻量级** : `PreviewView`只关注预览。它所有的内部资源都是为了在相机使用时设置相机预览和管理预览界面。这种关注点的分离允许`PreviewView`代码保持干净。
*   **详尽的** : `PreviewView`处理在屏幕上显示摄像机画面最困难的部分，包括宽高比、缩放和旋转。它还包含兼容性修复和解决方法，以便在多种设备、屏幕尺寸、摄像头硬件支持级别和显示配置(如分屏模式、锁定方向和自由形式多窗口)上提供无缝体验。

# **预览视图—实现方式**

`PreviewView`是`FrameLayout`的子类。为了显示相机馈送，它使用一个`[SurfaceView](https://developer.android.com/reference/android/view/SurfaceView)`或`[TextureView](https://developer.android.com/reference/android/view/TextureView)`，当它准备好的时候给相机提供一个预览表面，只要相机正在使用它就试图保持它有效，当过早释放时，如果相机还在使用，提供一个新的表面。

当涉及到某些关键指标，包括功率和延迟时，`SurfaceView`通常比`TextureView`好，这就是为什么默认情况下`PreviewView`试图使用`SurfaceView`。但是，一些设备(主要是[遗留设备](https://source.android.com/devices/camera/versioning#camera_api2))在预览界面过早发布时会崩溃。不幸的是，使用`SurfaceView`无法控制何时释放表面，因为这是由`View`层级控制的。在这些设备上，`PreviewView`退回到使用`TextureView`来代替。在需要预览旋转、透明或动画的情况下，你也应该强制`PreviewView`使用`TextureView`。

您可以通过调用`[PreviewView.setPreferredImplementationMode(ImplementationMode)](https://developer.android.com/reference/androidx/camera/view/PreviewView#setPreferredImplementationMode(androidx.camera.view.PreviewView.ImplementationMode))`来显式设置您希望`PreviewView`使用的实现，其中`ImplementationMode`可以是`SURFACE_VIEW`或`TEXTURE_VIEW`。`PreviewView`当首选模式为`SURFACE_VIEW`时，尝试尊重您的选择，当首选模式为`TEXTURE_VIEW`时，确保您的选择。

⚠️在开始预览之前，通过调用`[Preview.setSurfaceProvider(PreviewView.createSurfaceProvider())](https://developer.android.com/reference/androidx/camera/core/Preview#setSurfaceProvider(java.util.concurrent.Executor,%20androidx.camera.core.Preview.SurfaceProvider))`确保设置好你的首选实现模式。

下面显示了如何设置`PreviewView`的首选实现模式。

# **预览视图—预览**

`PreviewView`处理创建`Preview`用例所需的`[SurfaceProvider](https://developer.android.com/reference/androidx/camera/core/Preview.SurfaceProvider)`的细节，以启动预览流。`SurfaceProvider`准备将提供给摄像机的表面，以便显示摄像机预览流，并在必要时负责重新创建`Surface`。`[PreviewView.createSurfaceProvider(CameraInfo)](https://developer.android.com/reference/androidx/camera/view/PreviewView#createSurfaceProvider(androidx.camera.core.CameraInfo))`接受一个可空的`[CameraInfo](https://developer.android.com/reference/androidx/camera/core/CameraInfo)`实例。`PreviewView`使用它，以及您首选的实现模式和摄像机的功能，来确定在内部使用的实现。如果你传入一个`null` `CameraInfo`，`PreviewView`使用一个`TextureView`实现，因为它不知道所选的摄像机是否能与`SurfaceView`一起工作。

一旦您构建了`Preview`用例以及您需要的任何其他[用例](https://developer.android.com/training/camerax#ease-of-use)，将它们绑定到一个`[LifecycleOwner](https://developer.android.com/reference/androidx/lifecycle/LifecycleOwner)`，使用来自绑定相机的`CameraInfo`创建一个`SurfaceProvider`，然后将其附加到`Preview`用例，通过调用`Preview.setSurfaceProvider(SurfaceProvider)`来启动预览流。

下面显示了如何将`PreviewView`附加到`Preview`以启动预览流。

# **预览视图—刻度类型**

`PreviewView`提供了一个 API，允许你控制预览的外观以及它在容器中的位置。

*   *如何*定义预览应该适合(**适合**)还是填充(**填充**)其容器。
*   *其中*定义了预览应该在左上角(**开始**)、居中(**居中**)还是右下角(**结束**)或其容器。

由“ *how* ”和“ *where* ”创建的组合代表`PreviewView`支持的可用刻度类型值:`FIT_START`、`FIT_CENTER`、`FIT_END`、`FILL_START`、`FILL_CENTER`和`FILL_END`。最常用的是`FIT_CENTER`，它翻译成一个信箱预览，以及`FILL_CENTER`，它在其容器中居中裁剪预览。

可以通过以下两种方式之一设置比例类型:

*   在 XML 布局中使用`PreviewView`的`[scaleType](https://developer.android.com/reference/androidx/camera/view/PreviewView.ScaleType)`属性，如下例所示。

*   通过调用如下所示的`[PreviewView.setScaleType(ScaleType)](https://developer.android.com/reference/androidx/camera/view/PreviewView#setScaleType(androidx.camera.view.PreviewView.ScaleType))`进行编程。

要检索一个`PreviewView`正在使用的当前秤类型，调用`[PreviewView.getScaleType()](https://developer.android.com/reference/androidx/camera/view/PreviewView#getScaleType())`。

# **预览视图—摄像机控制**

根据相机传感器方向、设备旋转、显示模式和预览比例类型，`PreviewView`可以缩放、旋转和平移从相机接收的预览帧，以便在 UI 中正确显示预览流。这就是为什么能够将 UI 坐标转换为相机传感器坐标非常重要。在 CameraX 中，这种转换是由一个`[MeteringPointFactory](https://developer.android.com/reference/androidx/camera/core/MeteringPointFactory)`完成的。`PreviewView`提供了一个 API 来创建一个:`[PreviewView.createMeteringPointFactory(cameraSelector)](https://developer.android.com/reference/androidx/camera/view/PreviewView#createMeteringPointFactory(androidx.camera.core.CameraSelector))`，其中`[CameraSelector](https://developer.android.com/reference/kotlin/androidx/camera/core/CameraSelector)`描绘了相机流预览。

`PreviewView`的`MeteringPointFactory`在你需要实现点击对焦的时候就派上用场了。即使相机预览默认启用自动对焦(当相机支持时)，您也可以在点击`PreviewView`时控制对焦目标。`MeteringPointFactory`会将聚焦目标的坐标转换为摄像机传感器坐标，然后使摄像机聚焦在该区域。

以下示例显示了如何使用设置在`PreviewView`上的[触摸监听器](https://developer.android.com/reference/android/view/View.OnTouchListener)实现点击聚焦。

另一个常见的相机预览功能是捏合缩放，这使得相机可以在您捏合预览时放大和缩小。为了用`PreviewView`实现它，在`PreviewView`上添加一个[触摸监听器](https://developer.android.com/reference/android/view/View.OnTouchListener)并将其连接到一个[刻度手势监听器](https://developer.android.com/reference/android/view/ScaleGestureDetector.SimpleOnScaleGestureListener)。这将拦截捏手势，并相应地更新相机的变焦比。

下面的例子展示了如何用`PreviewView`实现缩放。

# **预览视图—如何测试**

`PreviewView`在各种 Android 设备上提供一致的相机行为。这要归功于 CameraX 在测试`PreviewView`和它在[自动化测试实验室](https://developer.android.com/training/camerax/devices)的其他 API 上的投资。这些测试分为两大类:

*   **单元测试**验证`PreviewView`在其实现模式、规模类型和`MeteringPointFactory`方面的行为。他们还确保`PreviewView`在应该调整预览的时候正确地调整预览，比如当它的容器大小改变时，当显示布局更新时，以及当它被附加或重新附加到`[Window](https://developer.android.com/reference/android/view/Window)`时。
*   **集成测试**，确保`PreviewView`作为应用程序的一部分时行为正确，并相应地显示或停止预览流。这些测试包括在应用程序运行时，在应用程序关闭并重新打开多次后，在相机镜头来回切换后，以及在应用程序的生命周期被破坏并重新创建后，验证预览状态。目前，这些测试主要覆盖`PreviewView`的`TextureView`实现，因为从`SurfaceView`获得预览何时开始和停止的信号被证明是具有挑战性的。

# **结论**

总而言之:

*   `PreviewView`是一个自定义的`View`，它使得显示相机预览更加容易。
*   默认情况下,`PreviewView`使用`SurfaceView`处理预览图面，但在需要或请求时可能会使用`TextureView`。
*   将您的其他用例，如`ImageCapture`和`ImageAnalysis`绑定到一个`LifecycleOwner`，然后通过将`PreviewView`中的`SurfaceProvider`附加到绑定的`Preview`用例来启动相机预览。
*   通过定义`PreviewView`的缩放类型来控制预览的显示方式。
*   通过从`PreviewView`获取一个`MeteringPointFactory`来实现点击对焦预览。
*   通过在`PreviewView`上设置手势监听器，在预览上实现缩放。

想要更多相机的好处？检查:

*   [官方 CameraX 文档](https://developer.android.com/training/camerax)
*   [Guided CameraX 简介 codelab](https://codelabs.developers.google.com/codelabs/camerax-getting-started)
*   [CameraX 开发者社区](https://groups.google.com/a/android.com/g/camerax-developers)
*   Android 的 CameraX Jetpack 库现在处于测试阶段！
*   [CameraX 的新功能](/androiddevelopers/whats-new-in-camerax-fb8568d6ddc)
*   [使用 CameraX 构建的示例相机应用](https://github.com/android/camera-samples/tree/master/CameraXBasic)

关于`PreviewView`或预览的任何问题？留下评论。感谢阅读！