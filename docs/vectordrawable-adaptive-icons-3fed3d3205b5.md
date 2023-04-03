# 可矢量绘制的自适应图标

> 原文：<https://medium.com/androiddevelopers/vectordrawable-adaptive-icons-3fed3d3205b5?source=collection_archive---------3----------------------->

所以[Android O API 是最终的](https://android-developers.googleblog.com/2017/06/android-o-apis-are-final-get-your-apps.html)——API 26 在这里！这意味着你现在就可以开始用 API 26 进行编译(而且你真的应该[总是用最新的 SDK](/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd) 进行编译)并且朝着 API 26 的目标努力。

你应该考虑的第一件事是提供一个[自适应图标](https://developer.android.com/preview/features/adaptive-icons.html)——一个由单独的背景和前景层组成的图标。这种新格式为设备上的所有图标提供了一致的形状，还允许启动器通过分别设置背景和前景的动画来添加视觉效果。这真的是你如何给使用 Android O 设备的用户留下良好的第一印象。

随着系统处理外部边缘形状及其阴影，自适应图标让您有机会重新评估您如何构建您的应用程序图标。如果你能够将你的应用程序图标构建成 SVG/vector 图像，考虑避免使用更多 png 作为背景和前景来膨胀你的应用程序，并利用 [VectorDrawables](https://developer.android.com/guide/topics/graphics/vector-drawable-resources.html) 作为你的自适应图标。

> **注意:**有**个**有效案例，png 是您应用程序的正确选择。在试图把所有东西都做成矢量绘图之前，先和你的设计师谈谈。

![](img/199517f3396517211852c7dae1960ae0.png)

VectorDrawable Adaptive Icons for [Muzei](https://play.google.com/store/apps/details?id=net.nurik.roman.muzei)

我将带你看一下如何将 [Muzei](https://play.google.com/store/apps/details?id=net.nurik.roman.muzei) 的当前图标转换成自适应图标。

> **注意:**您不需要**而**将 API 26 作为目标来提供一个自适应图标——只需要针对它进行编译。用户会从你的工作中受益，即使你经历了其他的[行为改变。](https://developer.android.com/preview/behavior-changes.html)

# 自适应图标的基础

让我们假设您的 Android 清单的`<application>`标签有`android:icon="@mipmap/ic_launcher"`。要添加一个自适应图标来替换 API 26+设备上的那些 png，您将添加一个`res/mipmap-anydpi-v26/ic_launcher.xml`文件，如下所示:

```
<adaptive-icon
    xmlns:android="http://schemas.android.com/apk/res/android">
  <background android:drawable="@drawable/ic_launcher_background"/>
  <foreground android:drawable="@drawable/ic_launcher_foreground"/>
</adaptive-icon>
```

通过将它放在`mipmap-anydpi-v26`文件夹中，资源系统将优先使用它，而不是其他`dpi`文件夹中的任何文件(这正是您想要的，因为这个文件将替换所有文件)，并且它应该只在 API 26+设备中使用。

你会注意到 drawables 在`drawable`目录中。这是因为我使用矢量绘图。如果您使用的是 PNGs，这应该最有可能出现在`mipmap`中。另一个选择是为背景使用颜色资源:

```
<adaptive-icon
    xmlns:android="http://schemas.android.com/apk/res/android">
  <background android:drawable="@color/boring_white"/>
  <foreground android:drawable="@drawable/ic_launcher_foreground"/>
</adaptive-icon>
```

## 圆形图标

对于那些拥有`android:roundIcon`的人来说，你必须保持这个属性来继续支持 API 25 设备。带有圆形遮罩的设备将使用您的自定义`roundIcon`(如果可用的话),但是强烈建议对这些设备也使用单个自适应图标(这可以确保您获得标准阴影，支持视觉效果等)。

我发现最简单的方法是创建一个[别名资源](https://developer.android.com/guide/topics/resources/providing-resources.html#AliasResources)。您可以将您的`ic_launcher_round`图像保存在 API 25 设备的`res/mipmap`目录中，但是在您的`values-anydpi-v26`文件夹中添加一个文件:

```
<resources>
     <mipmap name="ic_launcher_round">@mipmap/ic_launcher</mipmap>
</resources>
```

# 构建可矢量绘制的背景

背景是一个很好的起点。作为一张 108 x 108 dp 的全出血图像，这张并没有太多特别之处:

```
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="108dp"
        android:height="108dp"
        android:viewportWidth="48.0"
        android:viewportHeight="48.0">
  <!-- Your background vector -->
</vector>
```

请记住，由于图像的掩蔽，用户通常只会看到中间的 72 x 72 dp。

# 构建可矢量绘制的前景

另一方面，前景图像提出了一个独特的挑战。

首先，你必须处理创建前景形状本身。它仍然在同一个 108 x 108 dp 画板上，但是您知道将要显示的“安全区”只是一个半径为 33 dp 的中间圆——不要在该部分之外放置任何关键内容！

我最初陷入困境的地方是当谈到前景也需要包括传统的 45 材质投射阴影。通常情况下，这将是你必须打破 PNGs 得到完美的梯度。但是**渐变支持被添加到 VectorDrawables 回到 API 24！**

然而，要让渐变起作用，需要做一些研究。`[VectorDrawable](https://developer.android.com/reference/android/graphics/drawable/VectorDrawable.html)` [文档](https://developer.android.com/reference/android/graphics/drawable/VectorDrawable.html)指出了使用`[GradientColor](https://developer.android.com/reference/android/R.styleable.html#GradientColor)`的能力，但是哪里可以找到好的例子呢？[当然是在 StackOverflow](https://stackoverflow.com/a/40927551/1676363) 上。

现在， [Roman Nurik](https://medium.com/u/90c74515fd18?source=post_page-----3fed3d3205b5--------------------------------) ，最初的 Muzei 图标的制造者，已经制作了[一个奇异的 SVG 版本的启动器图标](https://github.com/romannurik/muzei/blob/master/art/ic_launcher_material-noeffects.svg)，包括 SVG 中的`<linearGradient>`元素的阴影。我不会假装我知道他是如何得出正确的价值观的——与你当地的设计师交谈或者阅读几百遍[产品图标的材料设计指南](https://material.io/guidelines/style/icons.html#icons-product-icons)。

但是它变成了一个`res/color/ic_launcher_shadow.xml`文件，看起来像这样:

```
<gradient xmlns:android="http://schemas.android.com/apk/res/android"
          android:endColor="#0000"
          android:endX="36.814"
          android:endY="41.655"
          android:startColor="#F000"
          android:startX="18.691"
          android:startY="13.748"
          android:type="linear"/>
```

你会注意到来自`[GradientColor](https://developer.android.com/reference/android/R.styleable.html#GradientColor)` [文档](https://developer.android.com/reference/android/R.styleable.html#GradientColor)的所有属性，这些属性可用来定制这个属性。

> **注意:**从 Android Studio 3.0 Canary 3 开始，Android Studio 将`gradient`标记为错误(‘必须声明元素渐变’)。它仍然有效。

现在，我们可以像你引用颜色一样引用我们的渐变颜色:在`VectorDrawable`中使用`@color/ic_launcher_shadow`。在 Muzei 的例子中，这意味着前景背景由两条路径组成:阴影和它下面的形状:

```
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:height="108dp"
        android:viewportHeight="48.0"
        android:viewportWidth="48.0"
        android:width="108dp">
    <path
        **android:fillColor="@color/ic_launcher_shadow"**
        android:pathData="<your shadow's path>"/>
    <!-- This is your shape drawn on top of your shadow -->
    <path 
        android:pathData="<your path>"/>
</vector>
```

> **注意**:这也是你如何给你的图标添加一个完成层——在你的形状上添加一个单独的渐变，如[材料设计指南](https://material.io/guidelines/style/icons.html#icons-product-icons)中所解释的。

# 所有的好处，没有 APK 膨胀

通过利用 API 24 向 VectorDrawables 添加渐变的优势，我们可以构建设计良好的 VectorDrawable 自适应图标，这些图标在前景元素上具有正确的材质阴影，而无需在应用程序中添加更多的 png。

如果你想看到它的运行，加入 [**Muzei 的公测**](https://play.google.com/apps/testing/net.nurik.roman.muzei) ，然后在 Android O Dev Preview 3 设备上下载 [Muzei](https://play.google.com/store/apps/details?id=net.nurik.roman.muzei) ，并检查添加了自适应图标的[提交。(尽管除非你有超人的能力去想象像 Nick Butcher 这样的向量图，你可能会发现完整的提交并没有给我们已经讨论过的代码增加太多。)](https://github.com/romannurik/muzei/pull/419/commits/44873329384482c455fbbb2de0f4d88cd7ab88d4)

要了解更多信息，你可能想看看 Nick Butcher 关于自适应图标的优秀系列文章。

[](/google-developers/implementing-adaptive-icons-1e4d1795470e) [## 实现自适应图标

### Android O 引入了一种新的应用程序图标格式，称为自适应图标，旨在使设备上的所有图标更加…

medium.com](/google-developers/implementing-adaptive-icons-1e4d1795470e) 

## 应用程序快捷方式自适应图标

正如在[自适应图标页面](https://developer.android.com/preview/features/adaptive-icons.html#xml)中提到的，你也应该转换你的[应用快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)来使用自适应图标。同样的规则也适用于此:创建一个新的 v26 目录，并将你的快捷方式分为背景和前景(见 [Muzei 的提交](https://github.com/romannurik/muzei/pull/422/commits/ef12bafa09540a7bb191068b689d050452fb1c59)正是这么做的)。

如果您正在使用位图构建动态快捷方式，您可能会发现支持库 26.0.0-beta2 的`[IconCompat.createWithAdaptiveBitmap()](https://developer.android.com/reference/android/support/v4/graphics/drawable/IconCompat.html#createWithAdaptiveBitmap(android.graphics.Bitmap))`在确保位图被正确屏蔽以匹配其他自适应图标方面非常有用。