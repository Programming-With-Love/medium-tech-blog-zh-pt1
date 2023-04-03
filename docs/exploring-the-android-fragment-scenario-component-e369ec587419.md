# 探索 Android 片段场景组件

> 原文：<https://medium.com/google-developer-experts/exploring-the-android-fragment-scenario-component-e369ec587419?source=collection_archive---------2----------------------->

![](img/ce4dc1ec0fec5b58c5d7a6a2ab20b468.png)

我总是很好奇 android 下一步会有什么工具和功能——为了确保我不会错过这些，我喜欢关注 android 开发者网站上的发布说明。最近引起我注意的两个是`[fragment-1.1.0-alpha01](https://developer.android.com/jetpack/androidx/androidx-rn#2018-nov-fragment)`和`fragment-testing-1.1.0-alpha01`的发布，在测试方面，我们看到一个新的片段场景组件，它为我们提供了一种简单的方法来测试我们的片段。在这篇文章中，我想对此做一个快速的探究，这样我们就可以将它集成到我们的应用程序测试中。

**注意:**这目前只在 alpha 版本中可用。

[![](img/e8dd46ab2c119165e7a212299a73013e.png)](http://eepurl.com/dIKgiT)

当谈到我们的 android 应用程序的自动化测试时，我们可能会使用 espresso 来满足这一要求。如果是这样，您可能已经使用 espresso 测试了应用程序中的活动——这可能涉及到启动活动、为活动意图提供额外内容以及检查某些 UI 组件状态。当谈到活动时，这总是好的，你是如何处理应用程序中的片段测试的？有可能是这样的事情:

*   启动您的应用程序的活动，该活动包含您想要测试的片段
*   创建一个定制的测试规则，允许您启动想要单独测试的片段

虽然这两种方法都有效，但它们都有缺陷。首先，启动包含该片段的活动意味着您不是在孤立地测试该片段——您的测试应该只关心该片段本身，而不是它的父容器。例如，如果您在一个活动中使用底部导航视图，该活动还管理一些幕后状态(例如一个经过验证的用户),那么这些数据将需要在您的片段的测试中进行处理——您可能会遇到必须模拟许多不同的请求，而这些请求是片段根本不关心的。然后，如果住房活动发生变化，我们的片段测试会像活动测试一样失败吗？虽然这些只是简单的要点，但我希望它显示出像我们的应用程序代码一样，我们的测试应该关注它们的责任并保持轻量级。这将有助于确保我们的测试保持可维护性、可读性，并且在前进的过程中不太可能出错。

转到第二点——这一点无疑是对第一点的巨大改进。这允许我们孤立地启动我们的片段，并关注我们正在测试的东西。这要么需要使用外部依赖(比如 https://github.com/novoda/espresso-support 的[)要么编写自己的实现。然而，每当您想要设置隔离测试时，这些都需要向您的应用程序添加一个依赖项，或者向您的项目添加一点样板文件。没什么大不了的，但是值得记住这些事情。](https://github.com/novoda/espresso-support)

现在，随着`[fragment-1.1.0-alpha01](https://developer.android.com/jetpack/androidx/androidx-rn#2018-nov-fragment)`和`fragment-testing-1.1.0-alpha01`的最新发布，我们看到了这个新的 FragmentScenario 组件，它让我们不再担心如何测试我们的片段。这个新组件为我们处理了所有这些责任，让我们看看如何使用这个组件编写一些片段测试。

在我们可以设置这个新功能之前，我们需要继续将以下依赖项添加到我们的 **build.gradle** 文件中:

```
implementation 'androidx.fragment:fragment:1.1.0-alpha01'
debugImplementation 'androidx.fragment:fragment-testing:1.1.0-alpha01'
```

既然我们已经这样做了，我们可以继续改进我们的片段测试。让我们先快速浏览一下可用于启动所需片段的代码，然后检查其中是否显示了给定的视图:

```
@Test
fun tournamentsContainerIsDisplayed() {
    launchFragmentInContainer<TournamentsFragment>()

    onView(withId(R.id.*recycler_tournaments*))
            .check(matches(isDisplayed()))
}
```

这里的关键是我们调用的这个**launchFragmentInContainer()**函数——这是用来在一个隔离的环境中启动我们想要的片段的(本质上是一个容纳该片段的空活动)。您会注意到，这个函数将 fragment 类作为它的类型——这个类引用稍后用于执行启动。所有这些功能都是获取给定的片段并在内部 **EmptyFragmentActivity** 类中启动它——将片段放在根视图容器 **android 中。R.id.content** 这很可能类似于如果您以一种隔离的方式测试片段，您以前在您的应用程序内部会有的样板代码。

一旦我们的片段使用这个函数启动，我们就可以像在其他 espresso 测试中一样与之交互。

对 launchFragmentInContainer()的调用也可以通过添加两个参数来完成:

*   **片段参数** —提供可以传递给片段的附加参数，以配置其行为。很可能你的一些片段使用了片段参数，使用这个属性为你提供了一个简单的方法来测试这些条件值的行为

```
val args = Bundle().apply {
            putString(ARG_FRAGMENT_MODE, "some_mode_key") 
}
```

*   **片段工厂**—**androidx . fragment 1 . 1 . 0-alpha 01**版本现在允许使用带有 FragmentManager 实例的 fragment factory 来构建片段，这允许您提供一种方式来描述应该如何实例化片段。所以这个工厂的结果也可以被测试，这个工厂可以作为一个参数传入。

```
val factory = SomeFragmentFactory()
```

记住这些，我们就可以用我们已经提供的附加属性来启动我们的片段:

```
launchFragmentInContainer<TournamentsFragment>(args, factory)
```

现在，在某些情况下，您可能想要对您已经在测试中启动的片段执行触发器操作。当调用**launchFragmentInContainer**方法时，我们得到一个 **FragmentScenario** 类的实例。

```
val scenario = launchFragmentInContainer<TournamentsFragment>()
```

现在我们已经访问了这个片段场景，我们可以做一些不同的事情来进一步操作和测试我们的片段。

## 触发片段函数

在某些情况下，您的片段可能包含外部类可能调用的公共函数——可能该函数刷新内容，或者可能它处理一些应用程序状态更改。无论哪种方式，这个新的 API 都允许我们轻松地与我们发布的片段进行交互:

```
val scenario = *launchFragmentInContainer*<TournamentsFragment>()
scenario.onFragment **{
    it**.onUserSignedOut()
**}**
```

这个 **onFragment** 函数允许我们在片段上触发我们想要的动作。在这个调用中，API 使用片段管理器来定位我们的片段，然后执行我们所请求的动作。这将有助于测试可以在我们的片段上触发的公共函数。

## 碎片再创造

如果在某些情况下，我们可能需要重置片段的状态，那么我们可以通过使用 **recreate()** 函数来实现:

```
val scenario = *launchFragmentInContainer*<TournamentsFragment>()
scenario.recreate()
```

当调用这个时，托管我们的片段的空活动被重新创建，这反过来将导致片段状态被重置为它所处的前一个状态。

## 改变状态

在我们的一些片段中，我们可能会在它的生命周期中发生不同的事情——可能我们会在恢复期间触发一些特定的事件，而一旦片段开始，可能会发生不同的事件。在这些情况下，我们可以利用 **moveToState()** 函数以编程方式触发状态变化

```
val scenario = *launchFragmentInContainer*<TournamentsFragment>()
scenario.moveToState(Lifecycle.State.RESUMED)
```

当被调用时，这将检索我们当前的片段，并使用我们给定的参数强制设置状态。

在这篇文章中，我们看了一下新的片段场景组件，以及我们如何使用它在一个隔离的环境中测试我们的片段。这个新增加的 API 将帮助我们改进我们的片段测试，并保持事情向前简化。如果您对片段场景有任何问题或意见，请联系我们！

[](https://twitter.com/hitherejoe) [## 乔·伯奇(@hitherejoe) |推特

### 乔伯奇的最新推文(@hitherejoe)。Android Lead @Buffer。谷歌开发专家为@Android，@GooglePay &…

twitter.com](https://twitter.com/hitherejoe)