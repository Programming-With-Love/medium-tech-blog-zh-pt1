# 日夜——给你的应用添加一个黑暗主题

> 原文：<https://medium.com/androiddevelopers/appcompat-v23-2-daynight-d10f90c83e94?source=collection_archive---------0----------------------->

![](img/42cd5f1ce82445556f8f0e30f8a8a95b.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

*本帖自首次发布以来已更新多次。内容正确截至 2019 年 4 月 26 日。*

AppCompat 中的日夜功能允许您的应用程序在深色⚫和浅色⚪主题之间轻松切换。这对您的用户有很多好处，从节省有机发光二极管显示器的电力，到增加弱视人士的可用性，等等。

# 我如何使用它？

您需要更改您的主题，从一个 DayNight 变体扩展，然后调用一个方法来启用该特性。这里有一个主题声明的例子:

```
<style name="MyTheme" parent="**Theme.AppCompat.DayNight**"> <!-- Blah blah --></style>
```

如果你正在使用材料设计组件(我建议你这样做)，那么你也可以使用他们 1.1.0 版本的`Theme.MaterialComponents.DayNight`主题。这篇文章的其余部分保持不变。

然后，您需要使用提供的一个 API 在您的应用程序中启用该功能。

## setDefaultNightMode

我们为此提供的第一个 API 是`AppCompatDelegate.[setDefaultNightMode()](https://developer.android.com/reference/androidx/appcompat/app/AppCompatDelegate#setDefaultNightMode(int))`，它采用以下值之一:

*   `[MODE_NIGHT_NO](https://developer.android.com/reference/androidx/appcompat/app/AppCompatDelegate.html#MODE_NIGHT_NO)`。总是使用白天(光)主题。
*   `[MODE_NIGHT_YES](https://developer.android.com/reference/androidx/appcompat/app/AppCompatDelegate.html#MODE_NIGHT_YES)`。总是使用夜晚(黑暗)主题。
*   `[MODE_NIGHT_FOLLOW_SYSTEM](https://developer.android.com/reference/androidx/appcompat/app/AppCompatDelegate.html#MODE_NIGHT_FOLLOW_SYSTEM)` *(默认)*。这个设置遵循系统的设置，在 Android Q 和更高版本上是一个系统设置(下面会详细介绍)。
*   `MODE_NIGHT_AUTO_BATTERY`。当设备启用了“电池节电”功能时，变为暗色，否则变为亮色。
    ✨**t14】v 1 . 1 . 0-alpha 03 中新增。**
*   `MODE_NIGHT_AUTO_TIME` & `[MODE_NIGHT_AUTO](https://developer.android.com/reference/androidx/appcompat/app/AppCompatDelegate.html#MODE_NIGHT_AUTO)`。基于一天中的时间在白天/夜晚之间变化。
    ⛔ ***在 v1.1.0-alpha03 中已弃用。***

该方法是静态的，因此您可以随时调用它。你设置的值是**而不是**持续到进程启动，因此你需要在每次你的应用程序进程启动时设置它。我建议在您的应用程序类(如果有)中设置它，如下所示:

```
public class MyApplication extends Application {

    public void onCreate() {
        super.onCreate(); AppCompatDelegate.**setDefaultNightMode**(
            AppCompatDelegate.MODE_NIGHT_YES);
    }
}
```

从 AppCompat v1.1.0-alpha05 开始，`setDefaultNightMode()`将自动将任何昼夜变化应用于任何“已开始”的活动。这意味着当您调用 API 时，您不再需要手动重新创建任何活动。

## setLocalNightMode

您还可以通过调用 AppCompatDelegate 的`[setLocalNightMode()](https://developer.android.com/reference/androidx/appcompat/app/AppCompatDelegate#setLocalNightMode(int))`来覆盖每个组件中的默认值。当您知道只有一些组件应该使用日夜功能时，或者对于开发来说，这样您就不必坐着等待夜幕降临来测试您的布局，这是很方便的。

在每个活动中使用这个方法现在是一个*反模式*，你应该转而使用`setDefaultNightMode()`。请参阅下一节了解原因的技术细节。

## 活动娱乐

如果需要更改[配置](https://developer.android.com/reference/android/content/res/Configuration.html)，上面提到的两种方法都将重新创建您的活动，以便可以应用新的主题。这是测试您的 Activity +片段是否正确保存它们的[实例状态](https://developer.android.com/reference/android/app/Activity#onSaveInstanceState(android.os.Bundle))的好机会。

## 如何检查我当前的配置？

您可以通过检查您的资源配置来做到这一点:

```
int currentNightMode = getResources().getConfiguration().uiMode
        & Configuration.UI_MODE_NIGHT_MASKswitch (currentNightMode) {
    case Configuration.[UI_MODE_NIGHT_NO](https://developer.android.com/reference/android/content/res/Configuration.html#UI_MODE_NIGHT_NO):
        // Night mode is not active, we're in day time
    case Configuration.[UI_MODE_NIGHT_](https://developer.android.com/reference/android/content/res/Configuration.html#UI_MODE_NIGHT_NO)YES:
        // Night mode is active, we're at night!
    case Configuration.[UI_MODE_NIGHT_](https://developer.android.com/reference/android/content/res/Configuration.html#UI_MODE_NIGHT_NO)UNDEFINED:
        // We don't know what mode we're in, assume notnight
}
```

## 网络视图

目前使用这一功能有一个很大的注意事项:WebViews。因为它们不能使用主题属性，而且你很少能控制任何网页内容的样式，所以你的网页浏览量很有可能与你的动态主题应用形成强烈对比。因此，确保你在两种模式下测试你的应用程序，以确保它不会让用户感到厌烦。

## 系统夜间模式

Android Q 以上有一个系统夜间模式，可以在设置应用中启用。Android Pie 也有一个系统夜间模式设置，但它只是在设备的“开发者选项”中浮出水面。为了方便起见，我建议像对待以前版本的 Android 一样对待 Android Pie。

# 应用内设置

建议为用户提供一种方法来覆盖应用程序中的默认主题。推荐的选项和字符串有:

*   ‘光’(`MODE_NIGHT_NO`)
*   ‘黑暗’(`MODE_NIGHT_YES`)
*   由电池保护设置'(`MODE_NIGHT_AUTO_BATTERY`)。 *只显示在安卓派及以下。显示时，这应该是你的应用程序的默认设置。*
*   使用系统默认'(`MODE_NIGHT_FOLLOW_SYSTEM`)。
    *只在安卓 Q 及以上显示。显示时，这应该是你的应用程序的默认设置。*

实现的一种常见方式是通过 [ListPreference](https://developer.android.com/guide/topics/ui/settings/components-and-attributes) ，当值改变时调用`setDefaultNightMode()`。

# 更新你的主题和风格

除了调用 AppCompat 之外，您可能还需要做一些工作来更新您的主题、样式和布局，以便它们可以无缝地跨暗主题和亮主题工作。

这些事情的经验法则是尽可能使用主题属性。以下是需要了解的最重要的内容:

*   `?android:attr/textColorPrimary`。通用文本颜色。浅色主题接近黑色，深色主题接近白色。包含禁用状态。
*   `?attr/colorControlNormal` *。*通用图标颜色。包含禁用状态。

使用材质设计组件也使这变得容易得多，因为它的属性(如`?attr/colorSurface`和`?attr/colorOnSurface`)为您提供了一个简单的通用主题颜色。这些属性当然可以在你的主题中定制。

## 使用你自己的黑暗/光明资源

简单地说，AppCompat 只是支持使用`night`和`notnight`资源限定符。这些实际上从 API 8 开始就在平台中可用，但是以前只在非常特殊的场景中使用。

引擎盖下`Theme.AppCompat.DayNight`是这样实现的:

**res/values/themes.xml**

```
<style name="Theme.AppCompat.DayNight" 
       parent="Theme.AppCompat.Light" />
```

**RES/values-night/themes . XML**

```
<style name="Theme.AppCompat.DayNight" 
       parent="Theme.AppCompat" />
```

这意味着你也可以为你的光明和黑暗用户界面提供替代资源。只需在您的资源文件夹中使用`-**night**`限定符:`drawable-night`、`values-night`等。

## 为什么我应该使用 setDefaultNightMode？

在 AppCompat v1.1.0 中，DayNight 进行了大规模的重写，以修复一些 bug。最大的错误是:

*   WebView 会在活动被加载到流程中后立即重置活动配置。这可能会导致后来使用错误的主题膨胀视图。

AppCompat 需要更改活动的资源配置以启用“夜间模式”。WebView 的问题源于使用了一种现已废弃的方法:`Resources.[updateConfiguration()](https://developer.android.com/reference/android/content/res/Resources.html#updateConfiguration(android.content.res.Configuration,%20android.util.DisplayMetrics))`来实现这一点。不幸的是，WebView 不能很好地使用这种方法(因此被弃用)。

重写的重点是转移到更新配置的新方法:`ContextThemeWrapper.[applyOverrideConfiguration()](https://developer.android.com/reference/android/view/ContextThemeWrapper.html#applyOverrideConfiguration(android.content.res.Configuration))`。不幸的是，这个 API 使用起来要复杂得多，因为它只能在对`getResources()`的任何调用之前被调用。原来`getResources()`被称为 **lot** ，并且在活动生命周期的早期被称为*。事实上，我找到的唯一可以称之为安全的地方是在`attachBaseContext()`，这比`onCreate()`要早得多。*

好了，我刚刚给你讲了很多关于 DayNight 内部的技术东西，那么这和`setDefaultNightMode()`有什么关系呢？因为我们只能在`attachBaseContext()`中调用`applyOverrideConfiguration()`，这给了我们一个很小的窗口让`setLocalNightMode()`工作，而不必重新创建活动。

以前，您可以非常愉快地执行以下操作，而无需 AppCompat 重新创建活动:

```
public class MyActivity extends Activity {

    public void onCreate(Bundle icicle) {
        super.onCreate(icicle); getDelegate().setLocalNightMode(
                AppCompatDelegate.MODE_NIGHT_YES);
    }
}
```

由于我们上面所说的，那个**现在将**在新版本的《白日之夜》中触发一个娱乐。事实上，任何对`setLocalNightMode`的调用都会触发一次重新创建(如果主题改变的话)。

这就是为什么你现在更喜欢`setDefaultNightMode()`的关键，以减少不必要的娱乐。因为它是一个静态方法，所以值总是可用的，并且不受活动生命周期的约束。该方法模拟了大多数应用程序想要做的事情，即提供应用程序范围的设置或首选项。

这并不意味着使用`setLocalNightMode`是错误的，只是将它用于它的本意:在单个活动中一次性覆盖。