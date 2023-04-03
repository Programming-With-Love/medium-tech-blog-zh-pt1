# 使用活动场景步入活动测试

> 原文：<https://medium.com/google-developer-experts/stepping-into-activity-tests-with-activityscenarios-5db98d5311e6?source=collection_archive---------1----------------------->

![](img/641b6522a948a8410e765dc272cb7f18.png)

Photo by [monicore](https://www.pexels.com/@monicore?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [Pexels](https://www.pexels.com/photo/mountain-and-lake-at-sunset-135157/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

不久前，谷歌的测试团队推出了 [Android X 测试库](https://developer.android.com/training/testing/set-up-project)，其中我们可以找到`[ActivityScenario](https://developer.android.com/guide/components/activities/testing)`，一种测试`Activities`及其相关代码的新方法。这些 API 在几周前发布了稳定版本，日常使用完全安全。

这个工具的主要思想是驱动`Activity`的状态，这样我们可以模拟应用程序通过`onCreate()`、`onResume()`和其他生命周期方法；这有助于开发人员测试这些状态之间的流程。

同一个套件中的`ActivityScenario`和其他 API 的一个非常特殊的方面是可以在 JVM ( [使用 Robolectric](https://developer.android.com/training/testing/fundamentals#robolectric) )或设备上运行测试。这种方法的唯一警告是，需要根据我们希望它们运行的位置，将所有需要的测试文件移动到`test`或`androidTest`文件夹中。

使用 Robolectric 在 JVM 上运行测试将极大地改善执行时间。相反，当我们在设备或仿真器上运行测试时，`Activity`在设备上启动，使得测试与现实生活场景更加一致。

> **注意:**对于本文的目的来说，在哪里运行测试并不重要。我们不会展示如何包含在特定环境中运行测试的库，相反可以在这里找到。

`ActivityScenario`和`[ActivityTestRule](https://developer.android.com/reference/android/support/test/rule/ActivityTestRule)`的主要区别在于，我们对目标`Activity.` `ActivityScenario`的`Lifecycle`的控制可以轻松驱动组件的每个状态，甚至决定重启它并测试在那种情况下会发生什么。

## 介绍场景

假设我们有一个`Lifecycle`感知组件，它是闪屏的一部分。超时后，如果用户没有登录，该组件会将用户重定向到登录屏幕，或者重定向到应用程序的主屏幕。

如果用户在倒计时完成前决定退出应用程序，我们使用`Lifecycle`来停止我们可能采取的所有行动。因为我们使用这个 API，所以我们需要一个`LifecycleOwner`，我们可以在其中添加组件作为观察者，而一个`AppCompatActivity`是实现这个的最简单的方法。

## 组件代码

组件本身非常小，这一点我们可以从它的源代码中看出:

在`SplashScreenManager`的构造函数中，我们传递目标`Activity boundActivity`、`IntentFactory`和以毫秒为单位的持续时间。然后，我们从`boundActivity`中获取`Lifecycle`，并将我们的组件添加为观察者。

当我们到达`Activity`的`onStart()`状态时，我们使用一个协程来产生一个异步操作，该操作在给定的时间内暂停，然后将用户转到下一个步骤，在该步骤之后立即关闭`boundActivity`。

如果我们在倒计时结束前到达`onStop`，我们将取消它，用户不会进入下一步。

对于我们想要编写的测试来说,`IntentFactory`并不重要，唯一重要的是它以这样的方式工作:

*   它加载令牌，为了方便起见保存在`SharedPreferences`中；
*   如果该令牌不可用，它会将用户转到登录屏幕；
*   如果它可用，它将打开应用程序的主屏幕。

## 编写测试用例

现在，我们知道我们期望这个组件如何工作，我们想围绕它编写测试，这样我们可以确保它按预期工作。

例如，我们将编写一个测试来检查未登录的场景，因此我们希望调用登录屏幕:

我们做的第一件事是从 [Espresso Intent](https://developer.android.com/training/testing/espresso/intents) 库中调用`Intents.init()`。这样，我们可以捕获任何`Intent`并在以后检查它们的内容。

然后我们创建测试`Activity`的`ActivityScenario`，创建它是为了能够真实地驱动`Lifecycle`。

此时，这样的`Activity` [应该处于](https://developer.android.com/reference/androidx/test/core/app/ActivityScenario.html#launch(java.lang.Class%3CA%3E)) `[onResume](https://developer.android.com/reference/androidx/test/core/app/ActivityScenario.html#launch(java.lang.Class%3CA%3E))` [状态](https://developer.android.com/reference/androidx/test/core/app/ActivityScenario.html#launch(java.lang.Class%3CA%3E))，我们在它的主线程上调用一个 lambda，创建我们需要的对象，包括`SplashScreenManager`，使用`Activity`本身，它作为`it`参数传递给 lambda。

> 注意:我们确保删除了令牌，所以我们将处于需要登录的情况。

下一步是将活动转移到`STARTED`状态，这样我们的协程就会运行并启动新的屏幕。我们将测试的等待时间设置为 0，以避免由于计时造成的任何延迟或剥落。

一旦我们的异步代码运行，我们就使用 Espresso Intent 库中的另一个函数，`[intended()](https://developer.android.com/reference/android/support/test/espresso/intent/Intents.html#intended(org.hamcrest.Matcher%3Candroid.content.Intent%3E,%20android.support.test.espresso.intent.VerificationMode))`这是一种理解`Intent`内容的方式，或多或少类似于`[onView()](https://developer.android.com/reference/android/support/test/espresso/Espresso.html#onView(org.hamcrest.Matcher%3Candroid.view.View%3E))`对`View`的作用。在这种情况下，我们检查`Intent`是否意味着启动登录屏幕。

最后但同样重要的是，我们需要调用`Intents.release()`来停止记录`Intent`和`ActivityScenario`上的`close()`，以便我们释放所有的资源，并将我们的测试恢复到它们的初始状态。如果没有这些步骤，我们的测试用例将会处于一个不可预料的(并且潜在地不可复制的)状态，这违背了拥有稳定测试套件的想法。

> **注:**组件的完整测试套件可以在[这里](https://gist.github.com/tiwiz/989c74f5d63e8340fee67df7a5fac100)找到。

## 为什么要用？

编写小而快速的单元测试无疑是验证我们编写的代码的最简单和最可靠的方式，但是，当我们构建一个功能或一个屏幕时，我们需要验证这些小部分是如何在 Android 系统中组合在一起的。大多数情况下，我们可以使用集成测试来确保不同的单元很好地配合在一起，但是在某些时候，我们发现自己处于这样一种情况，我们需要确保我们编写的所有代码都按照平台的预期工作。在这种情况下，能够驱动每个组件的`Lifecycle`并一步一步地验证，无疑是编写测试套件的最好和最不痛苦的方式之一，这也是`ActivityScenario`成为一个必要工具的地方。

*特别感谢我的朋友* [*科里*](https://twitter.com/@johnson_cor)*[*丹尼尔*](https://twitter.com/danybony_)*[*法比奥*](https://twitter.com/fabioCollini) *，以及* [*沃尔特*](https://twitter.com/wdziemia) *对这篇博文进行校对。***