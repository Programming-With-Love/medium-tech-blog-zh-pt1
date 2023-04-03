# 使用 AppCompat 进行主题化

> 原文：<https://medium.com/androiddevelopers/theming-with-appcompat-1a292b754b35?source=collection_archive---------2----------------------->

我喜欢标准组件——处理通用功能和可访问性的基本构件。我更喜欢设计一致的标准组件——这是 [AppCompat](https://www.youtube.com/watch?v=5Be2mJzP-Uw?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 提供的现成组件。但我最喜欢的是什么？标准组件看起来很棒，并为我的应用程序带来个性。这就是当你使用 AppCompat 提供的主题时所得到的结果。

现在你可以添加一些颜色到你的主题中，比如 *colorPrimary* 、 *colorPrimaryDark* 和 *colorAccent* ，你可以影响你的应用程序的大部分，而无需单独定制组件。

# 物质(设计)世界中的主题化

说到主题化，棒棒糖随着材料设计的引入有了很大的改变。人们不再期望每个按钮都完全相同或完全定制。用于生成[彩色组件](http://android-holo-colors.com?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)或[定制彩色动作条](http://jgilfelt.github.io/android-actionbarstylegenerator?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)的工具从必要的变成了超出实际需要的。相反，你可以定制你的主题的[调色板](http://developer.android.com/training/material/theme.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog#ColorPalette)来自动给你的大部分应用着色。强大的东西。

但不应该只有棒棒糖和更高的东西才是酷的东西，对吗？因此 [AppCompat v21](http://android-developers.blogspot.com/2014/10/appcompat-v21-material-design-for-pre.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 的一大重点是将这一新主题向后移植到所有 API 7+设备，并且从那时起支持才开始增长(随着色调感知小部件在 [AppCompat 22.1](http://android-developers.blogspot.com/2015/04/android-support-library-221.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 中公开以及更多功能在 [AppCompat 23.1](http://android-developers.blogspot.com/2015/10/android-support-library-231.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 中添加)。这意味着一个简单的主题可能看起来像:

```
<resources>
 <!-- inherit from the AppCompat theme -->
 <style name="AppTheme" parent="Theme.AppCompat"> <!-- your app branding color for the app bar -->
  <item name="**colorPrimary"**>@color/primary</item> <!-- darker variant for the status bar and contextual app bars -->
  <item name="**colorPrimaryDark"**>@color/primary_dark</item> <!-- theme UI controls like checkboxes and text fields -->
  <item name="**colorAccent"**>@color/accent</item> </style>
</resources>
```

与棒棒糖之前你可能看到的非常不同(谢天谢地！).

# 原色

你要添加的第一个颜色是 ***colorPrimary*** 。这种颜色应该代表您的主要品牌颜色，用户应该将它与您的应用程序相关联(因此它是“主要”名称)。显然，首先要用 colorPrimary 着色的应该是你的[应用栏](http://www.google.com/design/spec/layout/structure.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog#structure-app-bar)。

![](img/93605de47599f0fcd6536efd61a6f357.png)

使用*主题的基本主题时。AppCompat* 和 AppCompat 提供的应用程序栏，你会发现应用程序栏的背景自动使用你的 *colorPrimary* 。然而，如果你使用一个 [*工具栏*](http://developer.android.com/reference/android/support/v7/widget/Toolbar.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) (以及一个*主题。AppCompat.NoActionBar* 主题，很有可能)，你需要手动设置背景颜色。

```
<android.support.v7.widget.Toolbar
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  **android:background="?attr/colorPrimary**" />
```

正如我们在[之前关于应用程序栏](https://plus.google.com/+AndroidDevelopers/posts/AV2ooBWY1iy?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)着色的提示中提到的，*？attr/* 格式是你告诉 Android 这个值应该从你的主题中解析，而不是直接从一个资源中解析。通常当选择*原色*时，考虑 500 值左右的[材料调色板](https://www.google.com/design/spec/style/color.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog#color-color-palette)中的颜色。

# 彩色原色深色

正如您可能从名称中所料，***colorPrimaryDark***是您的原色的一种更暗的变体，它被用作修饰屏幕顶部的状态栏的背景色。这种颜色的差异让用户在系统控制的状态栏和你的应用程序之间有一个清晰的区分。与 *colorPrimary* 相比，这应该是同一色调的[调色板](https://www.google.com/design/spec/style/color.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog#color-color-palette)中的 700 色。

虽然 AppCompat 可以反向移植很多东西，但正确地给状态栏着色依赖于 Lollipop 中引入的新 API(即[*windowdrawsystembarbackgrounds*](http://developer.android.com/reference/android/R.attr.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog#windowDrawsSystemBarBackgrounds))，所以不要指望在旧平台版本上看到 *colorPrimaryDark* 状态栏。

# 色彩强调

当 *colorPrimary* 和 *colorPrimaryDark* 更适合作为用户界面部分的背景色时，大多数时候你会看到 ***colorAccent*** 是少量的，它们提供了一种对比色来引起对某些组件的注意。

事实上，许多内置组件已经使用了 *colorAccent* :

*   选中的复选框
*   单选按钮
*   [switch compat](http://developer.android.com/reference/android/support/v7/widget/SwitchCompat.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)
*   *编辑文本*的聚焦下划线和光标
*   [*TextInputLayout*](http://developer.android.com/reference/android/support/design/widget/TextInputLayout.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 的浮动标签
*   [*talayout*](http://developer.android.com/reference/android/support/design/widget/TabLayout.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)的当前页签指示器
*   选中 [*导航视图*](http://developer.android.com/reference/android/support/design/widget/NavigationView.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 项
*   [*浮动操作按钮*](http://developer.android.com/reference/android/support/design/widget/FloatingActionButton.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 的背景

虽然你可能会注意到相当多的应用程序专注于 *colorPrimary* 和 *colorPrimaryDark* (他们毕竟是初级的)，但 *colorAccent* 可以是一种引导用户注意力、提供背景和激发信心的简单方法(如果你关注用户研究，所有这些都很重要！).

# 更多颜色

如果这三种主色还不够，你还不需要为特定的组件着色:调色板中还有一些颜色可以帮助你微调某些东西:

*   ***colorControlNormal***控制组件的“正常”状态，如未选中的编辑文本、未选中的复选框和单选按钮
*   ***colorControlActivated***覆盖 *colorAccent* 作为复选框和单选按钮的激活或选中状态
*   ***颜色控制高亮*** 控制波纹着色

# 在主题叠加的帮助下对视图进行主题化

上面的示例主题有一个父主题*主题。AppCompat* 。如果你曾经看过[基本主题的源代码](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/master/v7/appcompat/res/values/themes_base.xml?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)，你会看到主题设置了几乎所有可能的东西，就像你对活动主题的期望一样。然而，从[版本 22.1](http://android-developers.blogspot.com/2015/04/android-support-library-221.html?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog) 开始，Lollipop 和 AppCompat 使得**将主题应用到单个视图**成为可能。

正如我们在[之前的提示](https://plus.google.com/+AndroidDevelopers/posts/JXHKyhsWHAH?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)中提到的， **ThemeOverlay** 是一种独立类型的父主题，**只改变需要的内容**——例如， *ThemeOverlay。AppCompat.Light* 改变背景颜色、文本颜色和高亮颜色，就好像它是一个灯光主题和*主题覆盖图。AppCompat.Dark* 同样可以切换到黑暗主题。

这在工具栏中尤为普遍，以至于有单独的*主题覆盖。AppCompat.ActionBar* 和 *ThemeOverlay。AppCompat.Dark.ActionBar，*也将 *colorControlNormal* 更改为*Android:textColorPrimary*并按照这些组件的预期设置正确的 *SearchView* 样式:

```
<!-- Ensure text is readable on a dark background by using a 
     Dark ThemeOverlay -->
<android.support.v7.widget.Toolbar
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:background="?attr/colorPrimary"
  android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" />
```

当然，这里真正的强大之处在于构建你自己的自定义主题叠加，就像任何其他主题一样——只要确保你使用的是*主题叠加的变体。AppCompat* 作为父主题:

```
<style name="ThemeOverlay.AccentSecondary"
       parent="ThemeOverlay.AppCompat">
  <item name=”colorAccent”>@color/accent_secondary</item>
</style>
```

因为是那些 *ThemeOverlay。AppCompat* 父主题自动将 AppCompat *colorPrimary* 转换为 Lollipop+framework*Android:color primary*属性——如果您忽略父主题，您会发现事情并不完全如您所愿！

# 主题:对每个应用都有用

因此，无论你有一个设计团队，一个设计师，还是自己设计你的应用程序，使用你可用的主题颜色是重要的第一步，并确保你最大限度地利用标准组件，而不是求助于几乎不同灰度的应用程序。

# BuildBetterApps

加入关于 [Google+帖子](https://plus.google.com/u/0/+AndroidDevelopers/posts/dait7RcbFaM?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)的讨论，关注 [Android 开发模式集](https://plus.google.com/collection/sLR0p?utm_campaign=android_series_themingappcompat_012116&utm_source=medium&utm_medium=blog)了解更多！

![](img/ede78edee0069962aa0daa7cc8c85f02.png)