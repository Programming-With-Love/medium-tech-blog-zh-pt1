# 为播放功能交付配置您的应用

> 原文：<https://medium.com/androiddevelopers/configuring-your-app-for-play-feature-delivery-f15a93856a0d?source=collection_archive---------7----------------------->

![](img/78243efcf455993e6100439f3f7a559a.png)

> **本文以视频形式提供，并在帖子末尾有链接。**

[Android 应用捆绑包](https://developer.android.com/platform/technology/app-bundle)是在[现代 Android 开发](http://d.android.com/mad)世界中 Android 应用的发布格式。

与通用 apk 相比，使用它的应用程序在用户设备上平均节省了 15 %的文件大小。您可以从这些节省和简化的版本中受益，而不必对您的应用程序代码进行任何更改，只需切换到 Android 应用程序捆绑包即可。

> 要了解更多关于如何构建你的第一个 Android 应用捆绑包的信息，请阅读本系列的前一篇文章[。](/androiddevelopers/building-your-first-app-bundle-bbcd228bf631)

但是你可以把事情掌握在你自己的手中，使用 [Play Feature Delivery](https://developer.android.com/guide/app-bundle/play-feature-delivery) 进一步模块化和优化安装。

# 为什么要在意一个模块化的 app，玩功能交付？

模块化的应用程序将在应用程序的不同部分之间建立清晰的界限。这在多个层面上都有好处。

在许多情况下，你只需要重新构建你的应用程序的一部分。这使得构建时间减少。随着构建时间的减少和模块间界限的清晰，工程速度可能会提高。

此外，在谷歌 Play 商店，我们已经看到

> **将应用程序的初始下载大小减少 3 MB 可以导致安装量增加约 1 %。**

在本帖中，您将了解 Android 应用捆绑包的功能，这些功能支持 Play 功能交付。这些功能使你能够进一步缩小应用程序的大小。我还将向您介绍可以用来有条件地或按需交付特性的 API，以及多种配置选项。

您可以使用 Android Studio 带您浏览“新模块”流程。但是，当您浏览流程时，我们将深入了解一下，您稍后可以如何更改配置。

# 建立基线

当开始使用功能模块模块化应用程序时，你的基线是**安装时间模块**。在这里，你已经能够获得好处，如提高建造速度和工程速度。

安装时模块的基本配置如下所示:

最重要的部分是发行版名称空间、`xmlns:dist="[http://schemas.android.com/apk/distribution](http://schemas.android.com/apk/distribution)"`和要设置为`install-time`的`delivery`配置属性。

当请求初始安装时，这样配置的模块将被安装在设备上。

默认情况下，每个`install-time`模块将被融合到基本模块中，这使得它们*不可移除*。如果您希望以后能够删除一个安装时模块，您所要做的就是将`removable`属性的值设置为`true`。

卸载模块对于教程、注册流程或其他具有较大内存占用量的模块非常有用，您希望这些模块在初始安装时可用，但一旦流程完成，这些模块可能就不再有用了。

我们提供了 [PlayCore API](https://developer.android.com/guide/playcore) 来请求模块的安装和卸载，我将在这篇文章的后面介绍它。

## Android Lollipop 5.0 之前的设备注意事项

启用功能模块安装的机制要求设备运行 Android Lollipop 5.0 或更高版本。对于旧版本的 Android，功能模块的内容可以放在基础 apk 中。要显式启用这一功能，您需要在模块标记内将 fusing 的 include 值设置为 true。

```
<dist:fusing dist:include=”true”>
```

# 有条件交货设置

除了安装时间交付，有条件交付是请求功能模块的另一种方式。安装可能取决于设备的 API 级别、用户的国家或设备的功能。

这是一个完全配置的 AndroidManifest 的样子。

并非所有这些条件都必须设置，也不太可能在一个模块中使用所有这些条件。让我们一步一步来看。

要设置有条件交付，请添加`dist:conditions`标签。

然后，您可以通过使用`min-api`和`max-api`来声明 API 级别的上限和下限。

当您拥有特定 API 级别的特定模块时，这些是很有帮助的。

此外，AndroidManifest 中的每个 [*uses-feature* 元素](https://developer.android.com/guide/topics/manifest/uses-feature-element)都可以用作安装条件。使用`device-feature`属性，您可以确保只有当用户的设备具备使用某个功能的技术能力时，该功能才会交付给用户。

虽然默认情况下，每个用户都可以在任何有应用程序的地方下载所有功能，但您可以选择仅在某些国家提供某些功能。这是本地化你的应用程序的好方法。
为此，添加`user-countries`标签，然后添加给定国家的两个字母的国家代码。

当您希望某项功能**在特定国家/地区**不可用时，请确保设置`dist:exclude=”false”`。如果您希望某个特性**仅在**可用，则将该值设置为`true`。

# 没有代码的模块

有时，您所拥有的只是您想要交付给用户的大资产，例如 TensorFlow 模型。如果没有与特性模块相关联的代码，确保在模块的 AndroidManifest 文件中将`hasCode`设置为`false`。

```
<application android:hasCode="false" />
```

这将让编译器知道**不应该创建任何 dex 文件**。

> 当模块中没有代码时，忽略将 hasCode 设置为 false 将导致运行时异常。

# 按需配置

要掌握准确的安装时间，您可以使用*按需安装*。这意味着，在应用程序下载并安装到用户设备上之后，您可以调用 API 来安装模块*。
使用按需安装可节省初始下载时间和大小。*

在 AndroidManifest 中，您必须将交付选项设置为`on-demand`。然后你可以使用 [PlayCore API](https://developer.android.com/guide/playcore) 在你的应用流程中下载、安装和卸载模块。

我建议你看看 GitHub 上的 [PlayCoreKtx 示例，以及下面链接的视频，了解 Play Feature Delivery 的点播交付部分的详细介绍。](https://github.com/android/app-bundle-samples/tree/main/PlayCoreKtx)

快乐模块化！

# 这是这篇文章的视频版本