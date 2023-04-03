# 在 Android 应用中使用矢量资源

> 原文：<https://medium.com/androiddevelopers/using-vector-assets-in-android-apps-4318fd662eb9?source=collection_archive---------2----------------------->

![](img/e6dbe285a1bdcfb9d85208c8c046e2af.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

在之前的帖子中，我们已经了解了 Android 的`VectorDrawable`图像格式及其功能:

[](/androiddevelopers/understanding-androids-vector-image-format-vectordrawable-ab09e41d5c68) [## 理解 Android 的矢量图像格式:VectorDrawable

### Android 设备有各种尺寸、形状和屏幕密度。这就是为什么我非常喜欢使用分辨率…

medium.com](/androiddevelopers/understanding-androids-vector-image-format-vectordrawable-ab09e41d5c68) [](/androiddevelopers/draw-a-path-rendering-android-vectordrawables-89a33b5e5ebf) [## 绘制路径:渲染 Android VectorDrawables

### 在上一篇文章中，我们研究了 Android 的 VectorDrawable 格式，探讨了它的优点和功能。

medium.com](/androiddevelopers/draw-a-path-rendering-android-vectordrawables-89a33b5e5ebf) 

在这篇文章中，我们将深入探讨如何在你的应用中使用它们。`VectorDrawable`是在 Lollipop (API 21)中引入的，也可以在 AndroidX(如`VectorDrawableCompat`)中获得，支持一直追溯到 API 14(超过 [99%的设备](https://developer.android.com/about/dashboards/))。这篇文章将概述在你的应用中实际使用`VectorDrawable`的建议。

# AndroidX 优先

从 Lollipop 开始，你可以在任何使用其他 drawable 类型的地方使用`VectorDrawable`(使用标准的`@drawable/foo`语法来引用它们),但是我会*而不是*建议**总是**使用 AndroidX 实现。这显然增加了您可以使用它们的平台范围，但不仅如此，它还支持将功能和错误修复移植到旧平台。例如，使用 AndroidX 的`VectorDrawableCompat`可以:

*   `nonZero`和`evenOdd`路径`fillTypes`—[定义形状](https://www.sitepoint.com/understanding-svg-fill-rule-property/)的内部的*的两种常见方式，常用于 SVG 中(`evenOdd`在 API 24 中添加到平台实现)*
*   渐变& `ColorStateList`填充/描边(在 API 24 中添加到平台实现)
*   错误修复

事实上，AndroidX 将使用 compat 实现，甚至在一些存在本机实现的平台上([目前为 API 21–23](https://android.googlesource.com/platform/frameworks/support/+/androidx-master-dev/appcompat/src/main/java/androidx/appcompat/widget/AppCompatDrawableManager.java#100))来提供上述好处。否则，它将委托给平台实现，因此仍然可以获得新版本的任何改进(例如，为了提高性能，在 API 24 中用 C 重新实现了`VectorDrawable`)。

基于这些原因，你应该**总是**使用 AndroidX，即使你足够幸运有一个`minSdkVersion`24。如果/当 `VectorDrawable`在未来被扩展了新的功能*和*它们也被添加到 AndroidX，那么它们将直接可用，而无需重新访问您的代码。

[亚历克斯·洛克伍德](https://medium.com/u/f61fb1c467cd?source=post_page-----4318fd662eb9--------------------------------) *如愿以偿*:

(VDC == `VectorDrawableCompat`)

# 怎么会？

要使用 AndroidX 矢量支持，您需要做 *2 件事*:

## 1.启用支持

你需要在你的应用程序的`build.gradle`中选择 AndroidX 矢量支持:

```
android {
    defaultConfig {
        vectorDrawables.useSupportLibrary = true
    }
}
```

如果你的`minSdkVersion`是< 21，这个标志防止 Android Gradle 插件[生成你的矢量资产的 PNG 版本](https://developer.android.com/studio/write/vector-asset-studio#apilevel)——我们不需要这个，因为我们将使用 AndroidX 库。

它还被传递给构建工具链。默认情况下 [AAPT](https://developer.android.com/studio/command-line/aapt2) (安卓资产打包工具)*版本*资源。这意味着如果你在`res/drawable/`中声明一个`VectorDrawable`，它会把它移到`res/drawable-v21/`，因为它知道这是`VectorDrawable`类被引入的时间。

> 这可以防止属性 ID 冲突——您在`VectorDrawable`中使用的属性(`android:pathData`、`android:fillColor`等)都有一个与之相关联的整数 ID，这是在 API 21 中添加的。在旧版本的 Android 上，没有任何东西阻止 OEM 使用任何“无人认领”的 id，这使得在旧平台上使用新属性变得不安全。

这种版本控制会阻止在较旧的平台上访问资源，使反向移植成为不可能 gradle 标志禁用了矢量绘图的这种版本控制。这就是为什么你在向量中使用`**android**:pathData`等，而不是像其他后端口功能一样切换到`**app**:pathData`等。

## 2.装载安卓系统

当加载 drawables 时，你需要使用来自 AndroidX 的方法，它提供了反向矢量支持。这样做的切入点是**总是**用`[AppCompatResources.getDrawable](https://developer.android.com/reference/androidx/appcompat/content/res/AppCompatResources.html#getDrawable(android.content.Context,%20int))`加载可提取的内容。虽然 [方式](https://developer.android.com/reference/android/content/Context#getDrawable(int))中有一个[号](https://developer.android.com/reference/android/content/Context#getDrawable(int)) [来加载 drawables(因为原因)，但是如果你想使用 compat 向量，你**必须**使用`AppCompatResources`。如果你做不到这一点，那么你就不能进入 AndroidX 代码路径，当你试图使用任何你运行的平台不支持的特性时，你的应用可能会崩溃。](https://developer.android.com/reference/android/support/v4/content/res/ResourcesCompat.html#getDrawable(android.content.res.Resources,%20int,%20android.content.res.Resources.Theme))

> `*VectorDrawableCompat*`也提供了一种`*create*`的方法。我建议总是使用`*AppCompatResources*`，因为这增加了一层缓存。

如果你想声明性地设置 drawables(即在你的布局中),那么`appcompat`提供了许多`*Compat`属性，你应该使用标准平台的**而不是**:

`ImageView`，`ImageButton`:

*   不要:`android:src`
*   做:`app:srcCompat`

`CheckBox`，`RadioButton`:

*   不要:`android:button`
*   Do: `app:buttonCompat`

`TextView` ( [截至](https://developer.android.com/jetpack/androidx/androidx-rn#2018-dec-03-appcompat) `[appcompat:1.1.0](https://developer.android.com/jetpack/androidx/androidx-rn#2018-dec-03-appcompat)`):

*   `android:drawableStart`不要:`android:drawableTop`等。
*   Do: `app:drawableStartCompat` `app:drawableTopCompat`等。

因为这些属性是`appcompat`库的一部分，所以一定要使用`app:`名称空间。在内部，这些`AppCompat*`视图使用`AppCompatResources`本身来启用加载向量。

> 如果你想了解`appcompat`如何将你声明的`TextView`等换成启用该功能的`AppCompatTextView`，那么看看这篇文章:[https://helw.net/2018/08/06/appcompat-view-inflation/](https://helw.net/2018/08/06/appcompat-view-inflation/)

# 在实践中

这些要求会影响您创建布局或访问资源的方式。以下是一些实际的考虑因素。

## 没有兼容性属性的视图

不幸的是，有许多地方你可能想在不提供 compat 属性的视图上指定 drawables(例如，`ProgressBar` s 没有`indeterminateDrawableCompat`属性)，即上面没有列出的任何东西。仍然可以使用 AndroidX vectors，但是您需要从代码中完成:

如果您正在使用[数据绑定](https://developer.android.com/topic/libraries/data-binding/)，那么这可以通过使用自定义绑定适配器来完成:

注意，我们**不希望**让数据绑定为我们加载 drawable(因为它不使用`AppCompatResources`加载 drawable[*当前是*](https://issuetracker.google.com/issues/111345022) )所以不能像`@{@drawable/foo}`那样直接引用 drawable。相反，我们希望将 drawable **id** 传递给绑定适配器，因此需要导入`R`类来引用它:

## 嵌套 d `rawable` s

一些可绘制类型是可嵌套的，例如`StateListDrawable`、`InsetDrawable`或`LayerDrawable`包含其他子可绘制类型。AndroidX 支持通过明确知道如何膨胀`<vector>`元素(还有`animated-vector`和`animated-selector`元素，但我们今天将重点关注静态向量)来工作。当您调用`AppCompatResources.getDrawable`时，它会用给定的`id`查看资源，如果它是一个 vector(即根元素是`<vector>`)，它会为您手动膨胀它。否则，它会将它交给平台进行充气——当这样做时，AndroidX 没有办法将*自身重新插入*的过程中。这意味着，如果你有一个包含向量的`InsetDrawable`并请求`AppCompatResources`为你加载它，它会看到`<inset>`标签，耸耸肩，并把它交给平台来加载。因此，它将没有机会加载嵌套的`<vector>`，因此这将失败(在 API < 21 上)或者只是退回到平台支持。

要解决这个问题，您可以在代码中创建 drawables 即使用`AppCompatResources`膨胀矢量，然后手动创建`InsetDrawable` drawable。

一个例外是最近添加到 AndroidX(`[appcompat:1.0.0](https://developer.android.com/jetpack/androidx/androidx-rn#1.0.0-new)`[)的后向移植](https://developer.android.com/jetpack/androidx/androidx-rn#1.0.0-new)`[AnimatedStateListDrawable](https://developer.android.com/reference/androidx/appcompat/graphics/drawable/AnimatedStateListDrawableCompat)` s，这是一个带有状态间动画转换的`StateListDrawable`版本(以`AnimatedVectorDrawables`的形式)。但是没有什么要求你去声明转变。因此，如果你只需要一个能够使用 AndroidX 膨胀子向量的`StateListDrawable`，那么你可以使用这个:

Credit for this genius hack: [https://twitter.com/alexjlockwood/status/1029088247131996160](https://twitter.com/alexjlockwood/status/1029088247131996160)

> 有一种方法可以使用`[*AppCompatDelegate#setCompatVectorFromResourcesEnabled*](https://developer.android.com/reference/android/support/v7/app/AppCompatDelegate.html#setCompatVectorFromResourcesEnabled(boolean))`在嵌套的 drawables 中启用向量，但是它有很多缺点。请务必仔细阅读 javadoc。

## 进程外加载

有时你需要在你不能控制*的地方提供 drawables，当*或*如何加载*时。例如:通知、主屏幕窗口小部件或者你的主题中指定的一些资产(例如设置`android:windowBackground`，它是在创建预览窗口时由平台加载的)。在这些情况下，你不负责加载 drawable，所以没有机会集成 AndroidX 支持，你不能使用 vectors API 21 之前的版本😞。

你当然可以在 API 21+上使用 vectors，但是要注意你可能不会喜欢 AndroidX 提供的特性/错误修复。例如，虽然 AndroidX 反向端口`fillType="evenOdd"`很好，但是在 API 21–23 设备上使用 AndroidX 支持之外的*的向量不会理解这个属性。对于这个具体的例子，我将在下一篇文章中介绍如何在设计时转换`fillType`。否则，您可能需要为不同的 API 级别提供替代资源:*

```
res/
  drawable-xxhdpi/
    foo.png             <-- raster
  drawable-anydpi-v21/
    foo.xml             <-- vector
  drawable-anydpi-v24/
    foo.xml             <-- vector with fancy features
```

注意，除了 api 级别限定符之外，我们还需要包括`anydpi`资源限定符。这是由于[资源限定符优先级](https://developer.android.com/guide/topics/resources/providing-resources#BestMatch)的工作方式；`drawable-<whatever>dpi`中的任何资产都将被视为比仅`drawable-v21`中的资产更好的匹配。

# x 标记了地点

希望这篇文章强调了使用 AndroidX vector 支持的好处以及您需要了解的一些限制。使用 AndroidX 支持不仅可以在更多平台版本上启用 vectors 和 backports 功能，而且*还可以*设置您接收任何*未来的*更新。

既然我们已经了解了*为什么*和*如何*应该*使用*向量，下一篇文章将深入探讨如何*创建*它们。

*即将推出:为 Android 创建矢量资产
即将推出:剖析 Android* `*VectorDrawable*` *s*