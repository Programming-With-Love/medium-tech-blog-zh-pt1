# 导航合成中的动画

> 原文：<https://medium.com/androiddevelopers/animations-in-navigation-compose-36d48870776b?source=collection_archive---------0----------------------->

![](img/a20f2587a3ebee85850b5a77818aa2e9.png)

Jetpack Compose 将动画的标准从“波兰，如果我们有时间”提高到“如此简单，没有理由不做”，其中很大一部分是屏幕级别的过渡。这就是为什么[导航组合](https://developer.android.com/jetpack/compose/navigation)一直致力于开发一套解决三种特定情况的解决方案:

*   在 Compose 1.0.0 中仅使用稳定的动画 API
*   启用 Compose 1.0.0 中的实验动画 API 支持
*   构建面向未来的动画 API(共享元素转换！！！)在 Compose 1.1.0 及更高版本中

每一个都需要稍微不同的方法，我们将在这里讨论。

# 构成💚动画片

从第一个 0.1.0-dev01 发行版到新的 Compose 1.0.1 发行版，Jetpack Compose 走过了漫长的道路。与视图世界相比，动画和过渡是一个巨大的进步。在追求完美的动画 API 的过程中，Compose 向 1.0.0 迈进时做了很多改变。

虽然一些低级别的动画 API，如非常强大的`animateTo()`和`animate*AsState()`在这一点上是稳定的，Compose 的基础部分，但在标有`@ExperimentalAnimationApi`的构建块之上还有一整类 API。

# 实验性 API 和语义版本控制

一个实验性的 API(在 Kotlin land 使用`@RequiresOptIn` API 的任何 API)意味着这些 API 随时都可能发生变化。这意味着这些 API 可能会在任何未来的版本中被更改、改进或替换——可能是 Compose 1.1.0-alpha04 或 1.2.0-alpha08。因此，如果您要更新您正在使用的 Compose 版本，而不是同时*和*更新该库，任何基于这些实验性 API 构建的库都会立即崩溃和失败。(如果你是早期 Compose 发行版的跟随者，你就会知道这种痛苦。)

所有 AndroidX 库，包括导航和合成，都遵循[严格的语义版本](https://semver.org/)，正如在 [AndroidX 发布页面](https://developer.android.com/jetpack/androidx/versions)上所解释的。这意味着一旦一个库进入发布候选(RC)阶段，任何非实验性的 API 都是一成不变的。要对这些稳定的 API 做出突破性的改变，需要一个主要的版本提升(即“2.0”)。

这对于向前和向后兼容性来说非常好——例如，您可以升级您的片段版本来尝试新的 alpha 版本，同时保持您的其他依赖于它们的稳定版本，一切都正常工作。

然而，这也意味着实验性的 API，即可以从你下面转移出来的 API，在不同的工件组之间是严格禁止的——同样，升级你的版本`androidx.fragment`不应该破坏`androidx.appcompat`。这也适用于`androidx.navigation`和`androidx.compose.animation`。

# 让导航 2.4 稳定

Navigation 2.4 是一个大版本，它既是第一个 Navigation Compose 版本，也是第一个对 Navigation Compose 和 Navigation with Fragments 提供多种支持的版本。这意味着我们正在包装剩余的相关 API 请求，为通过 beta、RC 和 stable 做准备。

对于 Navigation Compose，这意味着我们在 Compose 1.0.1 的基础上构建，目标是向前兼容那些想要(或者已经！)根据 Compose 1.1.0-alpha01 及更高版本移动到 start。

该向前兼容性要求意味着 Navigation Compose 2.4.0 中的任何代码都只能依赖于稳定的合成动画 API。这就是我们如何能够在[导航 2.4.0-alpha05](https://developer.android.com/jetpack/androidx/releases/navigation#2.4.0-alpha05) 中添加交叉渐变支持——在作曲的世界中，跳转切换应该是你的列表中第一个要完全消除的东西。

这种只使用稳定合成动画 API 的限制意味着像`[AnimatedContent](https://developer.android.com/reference/kotlin/androidx/compose/animation/package-summary#AnimatedContent(kotlin.Any,androidx.compose.ui.Modifier,kotlin.Function1,androidx.compose.ui.Alignment,kotlin.Function2))`这样的 API 不是导航 2.4 可以直接用来提供丰富的动画控制的东西，而这种控制正是你想要的导航 2.4 的一部分。然而，导航的可扩展本质意味着底层框架已经构建并可用。

# 简介:伴奏导航动画！

这种对目的地之间动画的潜在支持是我们能够发布[伴奏导航动画](https://google.github.io/accompanist/navigation-animation/)的原因，该动画基于今天发布的[导航 2.4.0-alpha06](https://developer.android.com/jetpack/androidx/releases/navigation#2.4.0-alpha06) 。导航动画构件提供了您一直在使用的导航合成 API 的自己的动画启用版本集:

*   用`rememberAnimatedNavController()`替换`rememberNavController()`
*   将`NavHost`替换为`AnimatedNavHost`
*   将`import androidx.navigation.compose.navigation`替换为`import com.google.accompanist.navigation.animation.navigation`
*   将`import androidx.navigation.compose.composable`替换为`import com.google.accompanist.navigation.animation.composable`

乍一看，你的应用程序的外观没有改变:默认动画仍然是导航 2.4 中的交叉渐变为你做的相同类型的`fadeIn`和`fadeOut`。然而，你将获得一个至关重要的新特性:**能够配置那些动画，并在你自己的屏幕之间进行替换。**

这种控制以四种新参数的形式出现在每个可组合目标上:

*   `enterTransition`:指定当你`navigate()`到这个目的地时运行的动画。
*   `exitTransition`:指定当您通过导航到另一个目的地而离开该目的地时运行的动画。
*   `popEnterTransition`:指定该目的地经过`popBackStack()`后重新进入屏幕时运行的动画。这默认为`enterTransition`。
*   `popExitTransition`:指定当您将此目的地弹出后离开屏幕时运行的动画。这默认为`exitTransition`。

在每种情况下，这些参数都具有相同的格式:

```
enterTransition: (
  AnimatedContentScope<NavBackStackEntry>.() -> EnterTransition?
)? = null,
```

每个都需要一个λ。lambda 使用`[AnimatedContentScope](https://developer.android.com/reference/kotlin/androidx/compose/animation/AnimatedContentScope)`为您提供来自哪里的`NavBackStackEntry`(`initialState`)和去往哪里的`targetState`。例如，对于`enterTransition`，进入目的地是`targetState`——您正在应用`enterTransition`的那个。相反的情况也适用于`exitTransition`:屏幕`initialState`是您应用退出过渡的屏幕。

这允许你写下你的目的地，例如:

或者，根据您的来源/目的地控制您的动画:

这里，朋友列表屏幕控制其从朋友列表到简档屏幕的退出转变，简档屏幕控制其从朋友列表的进入转变，允许在这两个目的地之间的定制滑动动画。我们也看到了使用`null`来表示“使用默认值”。这些缺省值来自父导航图，然后是父导航图的父导航图，一直到根`AnimatedNavHost`。这意味着设置默认动画(比如交叉渐变的时间)只需要改变你的`AnimatedNavHost`上的全局`enterTransition`和`exitTransition`就可以了。

如果您只想更改一个子图的默认值(比如，您的登录流总是在动画中使用水平幻灯片)，您也可以在嵌套图级别上设置该动画:

请注意我们如何使用`[hierarchy](https://developer.android.com/reference/kotlin/androidx/navigation/NavDestination#(androidx.navigation.NavDestination).hierarchy())` [扩展方法](https://developer.android.com/reference/kotlin/androidx/navigation/NavDestination#(androidx.navigation.NavDestination).hierarchy())来确定目的地是否实际上是登录图的一部分——这样，我们从*到*登录图的转换和从到*登录图的转换就使用默认转换(或者您在更高级别设置的任何转换)。*

每当你有一个方向转换，比如水平滑动，这就是`enterTransition`和`popEnterTransition`之间的区别变得非常方便的地方——你将能够避免一个屏幕向右滑动而另一个屏幕向左滑动的情况。

伴奏者是 Jetpack 库的助推火箭，随着 Compose 1.1 工作的进展，现在让我们发布实验特性*。*

通过以下方式添加[伴奏导航动画](https://google.github.io/accompanist/navigation-animation/):

```
implementation
    "com.google.accompanist:accompanist-navigation-animation:0.16.1"
```

# 导航合成和动画未来

随着基于 Compose 1.0.1 的导航 2.4 和伴奏导航动画通过实验 API 扩展了 Compose 1.0 的限制，还有其他东西即将出现:Compose 1.1。查看[撰写路线图](https://developer.android.com/jetpack/androidx/compose-roadmap)，有一个真正重要的即将到来的特性令人兴奋不已:

> 支持共享元素转换

我们对导航 2.5 的目标是把所有的好的组合 1.1 带到导航组合。这意味着当动画 API 失去它们的实验状态时，我们可以将它们直接合并到导航组合中。这也意味着我们可以构建这样的 API，我们知道它将在共享元素转换可用时支持它们。

这也意味着伴奏导航动画应该被视为一种临时措施:一旦导航组合本身提供了相同级别的动画 API(根据您的反馈定制！)，您将能够直接依赖它，并完全删除伴奏导航动画。

# 往前走，做动画

平衡稳定性和我们作为 Jetpack 库对自己提出的向前和向后兼容性要求，以及快速发布特性的能力，意味着这并不像我们希望的那样简单。随着 Jetpack Compose 获得动力并加速超过对那些助推火箭的需求，伴奏已经成为一个巨大的福音。我要感谢 [Chris Banes](https://medium.com/u/9303277cb6db?source=post_page-----36d48870776b--------------------------------) 和所有投入时间的开发人员，Compose 背后的整个团队，以及所有帮助塑造 Android 开发未来的人们。

PS:如果你正在寻找更多的导航+伴奏的好东西，看看全新的[伴奏导航材料](https://google.github.io/accompanist/navigation-material/)！

[](https://jossiwolf.medium.com/introducing-navigation-material-%EF%B8%8F-a19ed5cc33fd) [## 介绍导航材料🧭🎨️

### 导航-撰写(嗯，有点)有一个令人兴奋的新功能:支持底部表目的地！

jossiwolf.medium.com](https://jossiwolf.medium.com/introducing-navigation-material-%EF%B8%8F-a19ed5cc33fd)