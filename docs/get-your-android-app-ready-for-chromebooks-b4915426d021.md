# 为 Chromebooks 准备好您的 Android 应用

> 原文：<https://medium.com/androiddevelopers/get-your-android-app-ready-for-chromebooks-b4915426d021?source=collection_archive---------0----------------------->

随着新谷歌 Pixelbook 的发布，Chrome OS 上的谷歌 Play 商店正式退出测试，以及 Chromebooks 的整体使用量在上升，是时候开始考虑你的 Android 应用程序在 Chrome OS 上的表现了。

Chromebook 用户已经可以使用大多数 Android 应用程序，并且无需进行任何更改即可运行。但是，有几个方面需要注意和优化。我们将在本帖中讨论其中之一，输入兼容性。

# 输入兼容性

在 Chromebook 上使用应用程序时，最明显的区别是设备自带键盘、触控板，有时还有触控笔。许多(但不是全部)都有触摸屏，所以你的应用需要理解这些输入。用户希望在使用 Chromebook 时，某些应用程序能够像在任何其他笔记本电脑上一样运行，因此，当你在 Chrome OS 上测试你的应用程序时，请考虑你希望在这种外形上使用的功能。

首先，如果你想让你的应用程序在 Chrome OS 上使用，你应该确保你的清单中不需要触摸屏或多点触摸。触控板模拟假触摸事件，因此如果您需要触摸屏，您的应用程序将无法在某些 Chrome OS 设备上使用。

如果你真的认为你的应用需要一个真正的触摸屏或多点触摸，而不是基于这些来限制，但想想你如何通过操作系统的[模拟触摸手势](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#touchscreen-hw-features)或处理相同行为的不同方式来提供出色的体验。通过不需要触摸屏并将其添加到清单中，您可以确保您的应用程序在所有 Chrome OS 设备上都可用。

```
<uses-feature android:name="android.hardware.touchscreen"
                  android:required="false" />
```

# 键盘

拥有一个物理键盘可以让你的应用程序拥有多种导航方式。作为用户，我使用 tab 和箭头键在列表、表单和导航选项卡中导航。这种体验让你的用户可以更快地浏览你的应用程序并获得他们想要的信息，而不必使用触控板或触摸屏，这两者可能会让用户感到不舒服。如果你已经把可访问性作为首要任务，那么你已经走在了前面，因为这些努力有助于通过除触摸之外的其他方式来创建可导航的 UI。

通过使用布局文件中的`android:nextFocusForward`属性，您可以使用 Tab 键轻松添加导航。使用箭头键导航也很容易添加到布局文件中，使用`android:nextFocusDown`属性(用于向下箭头)和每个导航箭头各自的属性。点击查看更多关于添加此功能的信息[。](https://developer.android.com/training/keyboard-input/navigation.html)

键盘快捷键是您可以为使用键盘的用户添加的一些最佳功能，因为它在使用您的应用程序时带来了便利和熟悉程度。根据应用程序的性质，考虑用户期望的快捷方式，比如保存、打印、复制、粘贴、撤销和重做。大多数都很容易实现，因为有一个基于 Ctrl 的快捷键的 API，`dispatchKeyShortcutEvent`。

```
[@Override](http://twitter.com/Override)
public boolean dispatchKeyShortcutEvent(KeyEvent event) {
 if (event.hasModifiers(KeyEvent.***META_CTRL_ON***) &&
                     event.getKeyCode() == KeyEvent.**KEYCODE_S**) {
   //save action
   return true;
 }
 return super.dispatchKeyShortcutEvent(event);
}
```

如果你正在实现一个非基于 Ctrl 的快捷键，你必须使用`dispatchKeyEvent` API 来做更多的工作，并跟踪当前按下的键。请记住，通过工具提示或其他 UI 元素让用户知道快捷方式功能的存在是一个好主意。

# 鼠标和触控板

许多 Chrome OS 用户在浏览你的应用程序时不会使用触摸屏。像长时间点击或滑动这样的事情变得不直观，所以确保你的功能没有被锁定在触摸界面上。

处理长点击问题的最简单的方法是支持右键点击！使用`View.onContextClickListener`给你一个简单的方法来检测右击事件。

```
rightClickItem.setOnContextClickListener(
        new View.OnContextClickListener() {
  [@Override](http://twitter.com/Override)
  public boolean onContextClick(View v) {
    // right click action
    return true;
 }
});
```

为了解决缺乏易用滑动功能的问题，我建议提供一个悬停功能，允许用户执行相同的动作。悬停时出现的关闭按钮可以删除或关闭项目，这有助于使您的界面更易于使用鼠标。您可以使用一个`onHoverListener`来添加这个行为。确保用户界面有明显的变化，让用户知道他们处于悬停状态。

使用鼠标，滚动视图和列表是不同的。确保你的应用程序在使用鼠标时能够正确滚动，并找到你可能需要自己构建滚动功能的地方。您可以使用`View.OnGenericMotionListener`并检测它是否是滚动事件，然后在方法体中执行您需要的任何行为。

```
scrollableView.setOnGenericMotionListener(
        new View.OnGenericMotionListener() {
  [@Override](http://twitter.com/Override)
  public boolean onGenericMotion(View v, MotionEvent event) {
    if (event.getAction() == MotionEvent.ACTION_SCROLL) {
      //scroll event
      return true;
    }
    return false;
  }
});
```

对于其他鼠标兼容性策略，您应该在这里看到示例。

默认情况下，你的应用可以像在手机上一样在 ChromeOS 设备上运行。兼容模式将帮助您的应用程序使用广泛使用的电话事件，而不是许多应用程序通常不处理的特殊事件。举个例子——许多应用程序并不测试鼠标滚轮——但是每个应用程序都关注触摸滚动事件。这样，兼容模式将把滚轮事件转换成触摸滚动事件。如果您想充分利用这些不同的事件，可以通过在清单中声明以下内容让系统知道这一点:

```
<uses-feature
    android:name="android.hardware.type.pc"
    android:required="false" />
```

添加这意味着您已经针对通常使用键盘、鼠标和触控板的 PC 设备优化了您的应用。它禁用兼容模式，并允许您处理这些不同的事件。

# 唱针

一些 Chrome OS 设备和手机附带了手写笔，因此您可以考虑为此构建支持。一些应用程序更倾向于使用手写笔，所以在考虑应用程序时要考虑到这一点。您可以使用许多手写笔 API 来构建更高级的支持。使用`View.onTouchEvent()`你可以获得许多信息，例如倾斜和方向、压力，以及这种触摸是来自手写笔、手指还是手写笔橡皮擦。

当使用手写笔时，Chrome OS 试图拒绝其他触摸，如手掌放在屏幕上。有时，在 Chrome OS 将触摸识别为“手掌”触摸之前，会向应用程序报告这些触摸。一个`ACTION_CANCEL`事件将被发送，告诉应用程序摆脱这些触摸。要处理这种情况，请确保在显示器上连续呈现触摸系列，但如果该事件通过系统传到您的应用程序，请准备好删除该行。更多信息可以在[这里](https://developer.android.com/topic/arc/input-compatibility.html#palm_rejection)找到。

对于笔记应用程序，请确保在清单中包含笔记意图。当用户请求一个笔记应用时，Chrome OS 会显示所有包含此意图的应用。

```
<intent-filter>
    <action android:name="org.chromium.arc.intent.action.CREATE_NOTE" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```

利用 Chrome OS 设备上可用的新技术，关注更新的手写笔 API！

更多手写笔支持信息，包括如何在没有手写笔设备的情况下进行测试，可以在[这里](https://developer.android.com/topic/arc/input-compatibility.html#stylus)找到。

随着 Play Store 进入越来越多的 Chrome OS 设备，这些输入介质和行为将开始变得越来越常用和突出。作为开发人员，了解如何在这些令人兴奋的新外形上让您的应用程序变得更好非常重要。

有关更多信息，请参见 Chromebooks 的[输入兼容性](https://developer.android.com/topic/arc/input-compatibility.html)。