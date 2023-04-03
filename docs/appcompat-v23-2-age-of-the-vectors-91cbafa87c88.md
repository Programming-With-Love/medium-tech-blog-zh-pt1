# AppCompat —载体的年龄

> 原文：<https://medium.com/androiddevelopers/appcompat-v23-2-age-of-the-vectors-91cbafa87c88?source=collection_archive---------0----------------------->

![](img/30b9021cb9af21a976a36cb0646c9ccb.png)

[rulers](https://flic.kr/p/mKQ2JR) by Dean Hochman

正如你在支持库 23.2.0 [博客文章](http://android-developers.blogspot.co.uk/2016/02/android-support-library-232.html)中看到的，我们现在在支持库中有兼容的矢量可绘制实现:VectorDrawableCompat 和动画 VectorDrawableCompat。

这些是作为独立的功能块实现的。正如我们所知，开发人员希望从参考资料中使用它们，我们已经在 AppCompat 中直接添加了对矢量绘图的支持。

这种整合有多种原因，包括:

*   允许开发者在所有运行 Android 2.1 及以上版本的设备上轻松使用 <vector>drawables。</vector>
*   允许 AppCompat 自己使用矢量绘图。这本身就从 AppCompat 的 AAR 中削减了约 70KB(约 9%)。这听起来并不多，但对于所有设备上使用 AppCompat 的所有应用程序来说，节省是复合的。储存和运输成本的节省很快就会累积起来。

**第二点很重要。即使您自己不使用 compat vector 功能，您也需要像使用它一样关注这篇文章，因为 AppCompat 需要它。**

## 重要的事情先来

VectorDrawableCompat 依赖于 aapt 中的一些功能，这些功能告诉它保留<vector>使用的任何最近添加的属性 id，以便它们可以在 v21 之前被引用。这是通过 aapt 标志开启的(下面会提到)。</vector>

如果未启用此标志，在运行 KitKat 或更低版本的设备上运行应用程序时，您会看到以下错误(或类似错误):

```
Caused by: android.content.res.Resources$NotFoundException: File res/drawable-v19/abc_ic_ab_back_material.xml from drawable resource ID #0x7f020016
 at android.content.res.Resources.loadDrawable(Resources.java:2097)
 at android.content.res.Resources.getDrawable(Resources.java:700)
 ...
```

## 启用标志

我猜你们大多数人都会使用 Gradle，所以让我们快速浏览一下。

如果你使用的是 Gradle 插件的 2.0 或更高版本，我们有一个方便的快捷方式来启用一切:

```
android {
  defaultConfig {
    **vectorDrawables.useSupportLibrary = true**
  }
}
```

如果您尚未更新，并且正在使用 Gradle 插件的 1.5.0 或更低版本，您需要将以下内容添加到您的应用程序的 build.gradle 中:

```
android {
  defaultConfig {
    // Stops the Gradle plugin’s automatic rasterization of vectors
    generatedDensities = []
  } // Flag to tell aapt to keep the attribute ids around
  aaptOptions {
    additionalParameters "**--no-version-vectors**"
  }
}
```

## 如何在我的应用程序中使用我自己的矢量资源？

在我们开始之前，有一些事情需要注意。使用 AppCompat 时，VectorDrawableCompat 仅在 API 20 及以下版本上使用。这意味着当在 API 21 和更高版本上运行时，您将使用框架的 VectorDrawable 类。这与 VectorDrawableCompat 上的 create()API 略有不同，后者仍然使用 API v21+上的框架实现，但通过一个代理对象。

对，所以你想在你的应用中使用矢量资源，你的 minSdkVersion < 21。太好了！首先检查资产是否在 API 21+设备上工作，就像完整性检查一样。

## 好的，它工作了，那么我如何使用它呢？

有两种方法可以在 AppCompat 中使用矢量资源，这可以追溯到 API 7+:

## AppCompatImageView

所以你可能知道 AppCompat“注入”了它自己的小部件来代替许多框架小部件。这允许它执行着色和其他后台的事情。我们还通过新的 **app:srcCompat** 属性添加了对 VectorDrawableCompat 的支持。它旨在取代 **android:src** ，你也可以安全地将它用于非矢量资产。

这里有一个我们在 AppCompat 中实际使用的矢量资产示例:

**RES/draw able/IC _ search . XML**

```
<vector xmlns:android="..."
        android:width="24dp"
        android:height="24dp"
        android:viewportWidth="24.0"
        android:viewportHeight="24.0"
        android:tint="?attr/colorControlNormal"> <path
        android:pathData="..."
        android:fillColor="@android:color/white"/></vector>
```

使用此 drawable，ImageView 声明示例如下:

```
<ImageView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    **app:srcCompat**="@drawable/ic_search"/>
```

您也可以在运行时设置它:

```
ImageView iv = (ImageView) findViewById(...);
iv.setImageResource(R.drawable.ic_search);
```

相同的属性和调用也适用于 ImageButton。

## “神奇”的方式

> **首先，这个功能最初是在 23.2.0 中发布的，但后来我们发现了一些内存使用和配置更新问题，所以我们在 23.3.0 中删除了它。在 23.4.0** (技术上是一个修复版本)**中，我们重新添加了相同的功能，但是在一个标志后面，您需要手动启用它。**

如果您想重新启用此功能，只需将它放在您活动的顶部:

```
static {
    AppCompatDelegate.setCompatVectorFromResourcesEnabled(true);
}
```

剩下的这个岗位*可能*接着工作。

—

AppCompat 可以从框架中截取*一些*可提取的膨胀。这使得您可以使用矢量资产的所有标准属性，一切都可以正常工作。

所以让我告诉你什么可能有效:

DrawableContainers 引用其他只包含一个向量资源的 drawables 资源。

例如，引用其他包含向量的文件的 StateListDrawable。

**RES/drawable/state _ list _ icon . XML**

```
<selector xmlns:android="...">
    <item android:state_checked="true"   
            android:drawable="@drawable/checked_icon" />
    <item android:drawable="@drawable/icon" />
</selector>
```

**RES/drawable/checked _ icon . XML**

```
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="32dp"
    android:viewportWidth="32"
    android:height="32dp"
    android:viewportHeight="32">

    ...

</vector>
```

然后，您可以在大多数地方使用可绘制的状态列表:

作为 TextView 的复合 drawable:

```
<TextView
    android:drawableLeft="@drawable/state_list_icon" />
```

作为单选按钮的按钮:

```
<RadioButton
    android:button="@drawable/state_list_icon" />
```

作为 ImageView 的 src:

```
<ImageView
    android:src="@drawable/state_list_icon" />
```

不一定要用 StateListDrawable。它还可以处理 InsetDrawable、LayerDrawable、LevelListDrawable 和 RotateDrawable 容器。唯一的规则是向量需要在一个单独的文件中。

## 动画向量呢？

到目前为止，我们只讨论了“静态”矢量绘图。所以我们来谈谈动画向量。它们的工作方式非常相似，但是它们只在 API v11+上可用。如果您试图在运行 API 10 或更低版本的设备上加载<animated-vector>,那么系统将返回“null ”,并且不会显示任何内容。</animated-vector>

在< API 21 的平台上运行时，动画向量可以做的事情也有一些限制。以下是目前在这些平台上无法运行的内容:

*   路径变形(路径类型评估器)。这用于将一条路径变形为另一条路径。
*   路径插值。这用于定义灵活的插值器(表示为路径),而不是系统定义的线性插值器。
*   沿着路径移动。这个很少用。几何体对象可以沿着任意路径四处移动。

总之，基于你的应用将要运行的平台，动画师声明必须是有效的和功能性的。

## “魔法之路”是如何运作的？

如果您对这是如何实现的不感兴趣，或者胃口不好，您可以跳过这一步。😷

目前在 Android 平台中还没有办法从资源中使用定制的可绘制实现。因此，下面的方法不起作用:

**RES/drawable/my _ awesome _ drawable . XML**

```
<my.package.SuperAwesomeDrawable xmlns:app="..."
    app:customAttr1="32dp"
    app:customAttr2="32dp">

    ...

</my.package.SuperAwesomeDrawable>
```

重复一下:之前的代码**现在不能用了。**

所以你可能会问我们是如何让自定义的 drawables 在 AppCompat 中工作的？在 API 19 及以下版本的 Android drawable 系统中有一个鲜为人知的漏洞，该漏洞已被妥善保存以备不时之需。

不过，首先，有一点背景*(我真的应该做一个关于这个的演讲，自我提醒)*。当你在一个资源上设置属性时，通过一个对获取样式属性()的调用，它表现为实现视图的一个[类型数组](https://developer.android.com/reference/android/content/res/TypedArray.html)实例。那个通话链被安全锁定，没有办法挂钩进去(相信我，我试过)。TypedArray 是 final，所以我们不能从它扩展。

我前面提到的漏洞(实际上是 API 21 中修复的一个 bug)是这样一个事实:大多数 DrawableContainer 实现从对 Resources.getDrawable()的调用中获取它们的子 Drawable，而不是直接从 TypedArray 中获取。

例如，以下是 kitkat-release 中 [InsetDrawable](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/kitkat-release/graphics/java/android/graphics/drawable/InsetDrawable.java) 的摘录:

```
Resources r = ...;int drawableRes =
     a.getResourceId(android.R.styleable.InsetDrawable_drawable, 0); if (drawableRes != 0) {
    dr = r.getDrawable(drawableRes);
}
```

幸运的是，Resources **是**我们可以挂钩的东西，但是实际实现起来有点笨拙。

首先，我们必须创建一个覆盖 getDrawable()调用的资源包装器。为了这篇博文的目的，我们称它为 ResourcesWrapper。它的 getDrawable()首先检查 AppCompat 自己的内部 Drawable 管理器是否有任何自定义的 drawable 处理。如果 AppCompat 不想处理，就交给框架处理。

所以我们有了资源包装器，但是我们现在需要一种方法让视图使用它。我们通过 ContextWrapper 来实现这一点，context wrapper 从 getResources()返回我们的 ResourcesWrapper 实例之一。然后我们需要让所有的视图都使用我们的新上下文。我们通过两种方式做到这一点:

1.  每个 AppCompat 小部件(AppCompatImageView 等)都在构造函数中包装了它所提供的上下文。
2.  AppCompat 的内部视图膨胀器为每个膨胀的视图提供一个 ContextWrappers。

好玩吧？正如我所说的，这些都是实现细节，所以如果你不理解也不用担心。我知道你们中的一些人喜欢知道这类事情。