# 我为什么要安装系统窗口？

> 原文：<https://medium.com/androiddevelopers/why-would-i-want-to-fitssystemwindows-4e26d9ce1eec?source=collection_archive---------0----------------------->

![](img/ede78edee0069962aa0daa7cc8c85f02.png)

系统窗口是屏幕的一部分，系统在其中绘制非交互(对于状态栏)或交互(对于导航栏)内容。

大多数情况下，你的应用程序不需要在状态栏或导航栏下绘制，但是如果你这样做了:你需要确保交互元素(如按钮)不会隐藏在它们下面。这就是`[**android:fitsSystemWindows**](http://developer.android.com/reference/android/view/View.html#attr_android:fitsSystemWindows)**="true"**`属性的默认行为:它设置视图的填充以确保内容不会覆盖系统窗口。

有几件事要记住:

*   `**fitsSystemWindows**`
*   **插入总是相对于整个窗口** —插入甚至可以在布局发生之前应用，所以不要假设默认行为在应用填充时知道视图的位置
*   **您设置的任何其他填充将被覆盖** —您会注意到，如果您在同一视图上使用`android:fitsSystemWindows="true"`，则`paddingLeft` / `paddingTop` /etc 无效

而且，在很多情况下，比如全屏视频播放，这就足够了。你会有一个没有属性的全出血视图和另一个带有`android:fitsSystemWindows="true"`的全屏`ViewGroup`来显示你想要嵌入的控件。

或者你可能想让你的`[RecyclerView](http://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)`在一个透明的导航条下面滚动——通过结合使用`android:fitsSystemWindows="true"`和`[android:clipToPadding](http://developer.android.com/reference/android/view/ViewGroup.html#attr_android:clipToPadding)="false"`，你的滚动内容将在控件后面，但是当滚动到底部时，最后一个项目仍将被填充到导航条上面(而不是隐藏在下面！).

# 定制 *fitsSystemWindows*

但是这种默认行为仅仅是:默认。在 [KitKat](http://developer.android.com/about/versions/kitkat.html) 和更低版本中，你的自定义视图可以覆盖`[fitSystemWindows()](http://developer.android.com/reference/android/view/View.html#fitSystemWindows(android.graphics.Rect))`并提供你想要的任何功能——如果你已经使用了 insets，只需返回`true`或者如果你想给其他视图一个机会，返回 false。

然而，在 [Lollipop](http://developer.android.com/about/versions/lollipop.html) 和更高版本的设备上，我们提供了一些新的 API，使得定制这种行为更加容易，并且与视图的其他行为保持一致。取而代之的是覆盖`[onApplyWindowInsets()](http://developer.android.com/reference/android/view/View.html#onApplyWindowInsets(android.view.WindowInsets))`，它允许视图根据需要消耗尽可能多或尽可能少的 insets，并且能够根据需要在子视图上调用`[dispatchApplyWindowInsets()](http://developer.android.com/reference/android/view/View.html#dispatchApplyWindowInsets(android.view.WindowInsets))`。

更好的是，**如果你只需要 Lollipop 和更高的** — 上的自定义行为，你甚至不需要子类化你的视图，你可以使用`[ViewCompat.setOnApplyWindowInsetsListener()](http://developer.android.com/reference/android/support/v4/view/ViewCompat.html#setOnApplyWindowInsetsListener(android.view.View,%20android.support.v4.view.OnApplyWindowInsetsListener))`，它将比视图的`onApplyWindowInsets()`优先。`[ViewCompat](http://developer.android.com/reference/android/support/v4/view/ViewCompat.html)`还提供了不进行版本检查调用`[onApplyWindowInsets(](http://developer.android.com/reference/android/support/v4/view/ViewCompat.html#onApplyWindowInsets(android.view.View, android.support.v4.view.WindowInsetsCompat))`[*)*](http://developer.android.com/reference/android/support/v4/view/ViewCompat.html#onApplyWindowInsets(android.view.View, android.support.v4.view.WindowInsetsCompat))`[dispatchApplyWindowInsets()](http://developer.android.com/reference/android/support/v4/view/ViewCompat.html#dispatchApplyWindowInsets(android.view.View, android.support.v4.view.WindowInsetsCompat))`的 helper 方法。

# 自定义 fitsSystemWindows 的示例

虽然基本布局(`FrameLayout`、`LinearLayout`等)使用默认行为，但是有许多布局**已经**定制了它们对`fitsSystemWindows`的反应，以适应特定的用例。

一个例子是[导航抽屉](http://www.google.com/design/spec/patterns/navigation-drawer.html)，它需要跨越整个屏幕，出现在透明的状态栏下。

![](img/085cd731c72a7c34058a30967753b4a0.png)

在这里，`[DrawerLayout](http://developer.android.com/reference/android/support/v4/widget/DrawerLayout.html)`使用`fitsSystemWindows`作为它需要插入其子元素的标志(比如主内容视图——就像默认行为一样),但是仍然按照材质设计规范在那个空间中绘制状态栏背景(默认为你的主题的`colorPrimaryDark`)。

你会注意到`DrawerLayout`为 Lollipop 和更高版本上的每个孩子调用`dispatchApplyWindowInsets()`以允许孩子视图**也**接收`fitsSystemWindows`，这与默认设置不同(正常情况下，它只会消耗 insets，而孩子永远不会接收到`fitsSystemWindows`)。

`[CoordinatorLayout](http://developer.android.com/reference/android/support/design/widget/CoordinatorLayout.html)`还利用了覆盖它处理窗口插入的方式，允许在每个子视图上调用`dispatchApplyWindowInsets()`之前，在子视图上设置`[Behavior](http://developer.android.com/reference/android/support/design/widget/CoordinatorLayout.Behavior.html)`来拦截和改变视图对窗口插入的反应。它还使用`fitsSystemWindows`标志来知道是否需要绘制状态栏背景。

类似地，`[CollapsingToolbarLayout](http://developer.android.com/reference/android/support/design/widget/CollapsingToolbarLayout.html)`寻找`fitsSystemWindows`来确定何时何地绘制内容稀松布——当`CollapsingToolbarLayout`充分滚动离开屏幕时，一个全出血稀松布覆盖状态栏区域。

如果你有兴趣看一些伴随[设计库](http://android-developers.blogspot.com/2015/05/android-design-support-library.html)的常见案例，请查看 [cheesesquare 示例应用](https://github.com/chrisbanes/cheesesquare)。

# 利用系统，不要对抗它

需要记住的一点是，它不叫`fitsStatusBar`或`fitsNavigationBar`。系统窗口的构成、尺寸和位置肯定会随着平台版本的不同而变化——一个完美的例子，看看蜂巢和冰淇淋三明治之间的区别。

请放心，您从`fitsSystemWindows` **获得的插入内容将**在所有平台版本上都是正确的，以确保您的内容不会与系统提供的 UI 组件重叠——如果您自定义行为，请确保避免对它们的可用性或大小做出任何假设。

# BuildBetterApps

评论 [Google+帖子](https://plus.google.com/+AndroidDevelopers/posts/ZrHY4b6YKNF)并关注 [Android 开发模式集合](https://plus.google.com/collection/sLR0p)了解更多！