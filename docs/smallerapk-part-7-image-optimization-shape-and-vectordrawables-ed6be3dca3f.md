# #SmallerAPK，第 7 部分:图像优化、形状和矢量绘图

> 原文：<https://medium.com/androiddevelopers/smallerapk-part-7-image-optimization-shape-and-vectordrawables-ed6be3dca3f?source=collection_archive---------7----------------------->

![](img/16742a27007b85054b45771a39fbd376.png)

[**更新 1**](#2807) : VectorDrawableCompat 现已发布！

像 [JPEG、PNG 和 WebP](/@wkalicinski/smallerapk-part-6-image-optimization-zopfli-webp-4c462955647d) 这样的光栅图像格式对于某些图像类型来说是非常好的，有时甚至是必要的，但是它们有两个主要缺点——它们的大小以及你需要在你的项目中以不同的版本保存它们以适应不同的屏幕密度，这使得问题更加复杂。但是有些图像类型，尤其是图标和 UI 元素，可以使用 XML 和路径信息，用更简洁的表示方式轻松地重新创建。

# 可拉伸的形状

这是一种在 XML 文件中定义的通用 drawable，从一开始就可以在 Android 中使用，并且可以在任何平台版本下使用。它只能支持非常基本的形状，如矩形、椭圆形、线条和环形，但有时对于简单的背景或装饰来说就足够了。通过它的属性，你还可以创建渐变、圆角和轮廓效果。

这里的是一个关于拉延成形的完整指南。你可以在你的布局中使用位图或其他类型的 drawables 时使用它们，例如在 *android:background* 或 *android:src* 中。

# 矢量绘图

从 API level 21 (Lollipop)开始，Android 支持一种新的表示矢量图形的 drawable 类型(非常适合图标！)，称为[*VectorDrawable*](http://developer.android.com/reference/android/graphics/drawable/VectorDrawable.html)。

*VectorDrawables* 与密度无关(一个文件适用于所有屏幕)，缩放时保持完整质量，并且通常尺寸非常小。**使用矢量绘图工具可以对你的 APK 瘦身产生巨大的影响。**

该格式与 SVG 相似，使用相同的路径格式，但是 *VectorDrawables* 有自己的 XML 模式。这意味着在使用 SVG 之前需要对其进行转换，而且并不是所有的 SVG 特性都受支持。您可以使用 Android Studio 中的 [Vector Asset Studio](http://developer.android.com/tools/help/vector-asset-studio.html) 进行转换。

如果你想保持你的应用程序向后兼容，我们的 Gradle Android 插件也可以提供帮助。将以下几行添加到您的构建中，选择将从 *drawable/* 文件夹中的 *VectorDrawables* 生成 png 的密度:

## build.gradle

```
android {
    ...
    defaultConfig {
        //if you're using Android Gradle plugin < 2.0.0
        //omit the vectorDrawables block
        vectorDrawables {
            generatedDensities = ["mdpi", "hdpi", "xhdpi"]
        }
    }
}
```

> **注意**:如果不想生成 png，只需将 generatedDensities 设置为空列表:generatedDensities = []。

如果您使用多 APK 来只为棒棒糖之前的构建生成图像，您可以将 *vectorDrawables* 块放在[产品风味定义中。](/@wkalicinski/smallerapk-part-5-multi-apk-through-product-flavors-e069759f19cd)

# VectorDrawableCompat

AppCompat 版本 23.2 现在包括 VectorDrawables 的向后兼容实现。使用这种方法，只需对布局和代码做最小的改动，您就可以放弃在所有版本的应用程序中包含生成的 png(在 API 7+上)。

Chris Banes 在他的文章中解释了[如何使用 VectorDrawableCompat，或者你可以在这里](/@chrisbanes/appcompat-v23-2-age-of-the-vectors-91cbafa87c88)查看新支持库[中的这个和其他特性。](http://android-developers.blogspot.co.uk/2016/02/android-support-library-232.html)

继续阅读第 8 部分:本地图书馆，从 APK 打开。