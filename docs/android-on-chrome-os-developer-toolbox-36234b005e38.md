# Chrome 操作系统上的 Android 开发者工具箱

> 原文：<https://medium.com/androiddevelopers/android-on-chrome-os-developer-toolbox-36234b005e38?source=collection_archive---------7----------------------->

## 为 Chrome OS 优化你的应用程序的一个方便的代码片段工具箱！

![](img/39e8d40e389370d5bb51f7f62424f265.png)

自推出以来，Chromebooks 已经打造了一个强大的身份，即轻便、易用、安全的基于浏览器的笔记本电脑。凭借运行 Android 的能力，一个庞大的应用生态系统和大量新功能现在可以在[支持的 chrome book](https://www.chromium.org/chromium-os/chrome-os-systems-supporting-android-apps)上使用。虽然这对开发人员来说是个好消息，但它也带来了两个重要的考虑因素:

1.  开发者对他们的用户和他们的品牌负有**责任**，让他们的应用在 Chrome 操作系统上运行良好。
2.  开发人员有一个**机会**来利用大尺寸屏幕、键盘/鼠标输入、手写笔功能和增强的处理能力，同时接触全新的教育、企业和家庭用户群体。

在大多数情况下，Chrome 操作系统的优化很容易，并在可用性和功能性方面为您的用户带来巨大回报。额外的好处是，确保你的应用程序可以正确处理键盘和鼠标输入，可以大大提高你的应用程序对那些难以使用典型智能手机界面的用户的易用性。

在这篇文章中，我给出了一些最常见的 Chrome 操作系统优化的简短代码片段，以使你的应用程序在 Chrome 操作系统上看起来很棒。在以后的帖子中，我将更详细地讨论这些优化，比如我在[上发表的实现拖放](/google-developers/android-on-chrome-os-implementing-drag-drop-2cc2bdcdc621)的帖子。关于项目设置和为 Chrome OS 开发 Android 的更多信息可以在官方[为 Chrome OS 构建应用](https://developer.android.com/chrome-os/intro.html)文档中找到。

这篇文章对你有用吗？Chrome 操作系统是否有我在这里遗漏的优化，或者您希望我更详细地介绍？请给我写信！

# 入门指南

*   在您的应用程序中将 [android:targetSdkVersion 设置为 26](https://developer.android.com/about/versions/oreo/android-8.0-migration.html)
*   [启动 ADB 并在 Chromebook 上运行](https://developer.android.com/topic/arc/index.html#setup)以便于测试
*   这里找到的所有代码都是在 [Apache 2.0 许可](https://www.apache.org/licenses/LICENSE-2.0)下授权的。这里没有任何谷歌官方产品的一部分。
*   开始了。

# 键盘输入

## 输入键

用户希望能够按下 **Enter** 键来提交表单或发送消息。参见[处理键盘动作](https://developer.android.com/training/keyboard-input/commands.html)文档。

## 箭头键和 Tab 键导航

用户希望能够使用箭头键来导航菜单和 UI 元素。下面的简单代码将为适当的视图增加可导航性。参见[焦点处理](https://developer.android.com/reference/android/view/View.html#FocusHandling)文档。

默认的箭头键导航映射通常按预期工作。如果需要手动指定，请使用:

对于 Tab 键，请使用:

## 更改选定项目的突出显示颜色

在某些情况下，选定项目的高亮颜色在键盘导航期间可能不明显。在某些界面中，这可能会使键盘导航变得困难。解决这个问题的一个简单方法是改变应用主题中的 **colorControlHighlight** 值。选择具有适当[对比度](https://developer.android.com/guide/topics/ui/accessibility/apps.html#color-contrast)的高亮颜色时，考虑[可及性](https://developer.android.com/guide/topics/ui/accessibility/index.html)。

在 **AppTheme** 下的 **res/values/styles.xml** 中

如果您正在使用 ImageViews，您可能不希望在焦点对准时选择整个图像。通过使用一个选择器，你可以创建一个只在一个项目被选中时才出现的边框。

创建**RES/drawable/box _ border . XML**

然后将这个 drawable 设置为视图的 **BackgroundResource**

## 基于 Ctrl 的快捷键

笔记本电脑用户希望像 Ctrl-z 这样常见的桌面快捷方式能够工作。参见[dispatchKeyShortcutEvent](https://developer.android.com/reference/android/view/Window.Callback.html#dispatchKeyShortcutEvent(android.view.KeyEvent))文档。

**dispatchKeyShortcutEvent**方法将适用于 **Ctrl-** 命令，并且在 Android O 和更高版本上，也适用于 **Alt-** 和 **Shift-** 命令。用你需要的键码替换下面例子中的**键码 _Z** 。

如果你想专门针对一个 **Ctrl-** 命令和 **not** 包含 **Shift-** 和 **Alt-** 命令的同一个键，添加一个额外的**元状态**检查。参见 [hasModifiers](https://developer.android.com/reference/android/view/KeyEvent.html#hasModifiers(int)) 文档。

那么一个**元键**组合呢？下面是一个 **Ctrl-Shift-z** 命令的样子。

# 鼠标/触摸板输入

## 右键单击

用户假设用鼠标右键单击**和在触控板上双击**会显示上下文菜单或其他上下文相关信息。在大多数情况下，最简单的方法是给适当的视图添加一个[上下文菜单](https://developer.android.com/reference/android/view/ContextMenu.html)。否则，您可以创建一个特定的 [ContextClickListener](https://developer.android.com/reference/android/view/View.OnContextClickListener.html) 。****

**上下文菜单:**参见[创建上下文菜单](https://developer.android.com/guide/topics/ui/menus.html#context-menu)文档。

**上下文点击监听器:**参见[右键支持](https://developer.android.com/topic/arc/input-compatibility.html#right-click_support)文档。

## 工具提示/悬停文本

在适当的地方添加**工具提示**来帮助用户理解你的 UI 是如何操作的。参见[工具提示兼容性](https://developer.android.com/reference/android/support/v7/widget/TooltipCompat.html)文档。

## 悬停效果

当定点设备悬停在某些视图上时，向这些视图添加**视觉反馈**效果会很有用。参见[视图。on over listener](https://developer.android.com/reference/android/view/View.OnHoverListener.html)和[运动事件](https://developer.android.com/reference/android/view/MotionEvent.html)文档。

## 鼠标滚轮动作

在 Chrome OS 上，很多视图会自动实现鼠标**滚轮**动作。对于其他视图，尤其是自定义视图，您可能需要自己处理。参见[运动事件](https://developer.android.com/reference/android/view/MotionEvent.html)文档。

## 拖放

在桌面环境中，**将**项拖放到你的应用程序中，尤其是从 Chrome OS 的文件管理器中。下面的代码片段显示了接收文件的基本拖放目标实现。注意:Chrome OS 文件 URIs 的 MIME 类型是**application/x-arc-uri-list**。

有关设置和实现拖放的另一半(拖动部分)的更多详细信息，请查看我在[实现拖放](/google-developers/android-on-chrome-os-implementing-drag-drop-2cc2bdcdc621)上的帖子。另请参见官方的[拖放](https://developer.android.com/guide/topics/ui/drag-drop.html)文档。

# 窗口/显示器

## 维护体系结构组件的状态

Android 应用应该使用[架构组件](https://developer.android.com/topic/libraries/architecture/index.html)在方向和配置改变期间处理**状态**。这在 Chrome 操作系统上尤其重要，因为**自由形式的窗口大小调整**以及笔记本电脑和平板电脑模式之间的轻松切换。 [ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel.html) 和 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata.html) 是健壮状态处理所需的主要组件。查看这篇博文，使用 ViewModel 和 onSavedInstanceState 更深入地了解[处理状态。](/google-developers/viewmodels-persistence-onsaveinstancestate-restoring-ui-state-and-loaders-fc7cc4a6c090)

下面的代码示例演示了如何存储和自动更新一个整数(按钮被点击的次数),但是同样的设置也可以用于复杂的数据结构、媒体控制器等等。

首先扩展[视图模型](https://developer.android.com/topic/libraries/architecture/viewmodel.html):

然后在 **onCreate** 的主活动中:

# 编码快乐！

感谢阅读！期待今年有更多 Android 在 Chrome OS 上的具体帖子。如果这是有帮助的，或者如果你有一个很棒的 Chrome 操作系统实现，我很乐意听到它！

> 这里找到的所有代码都是在 [Apache 2.0 许可证](https://www.apache.org/licenses/LICENSE-2.0)下授权的。这里没有任何谷歌官方产品的一部分。