# 带有 Jetpack 和协程/流的 Android MVI 架构—第 1 部分

> 原文：<https://medium.com/google-developer-experts/android-mvi-architecture-with-jetpack-coroutines-flow-part-1-recyclerview-adapter-w-83a10134207f?source=collection_archive---------1----------------------->

# 具有 ViewBinding 的 RecyclerView 适配器

![](img/8c1484ec73a3a34bcd7809ebe53d5a9b.png)

Photo by [Mike Lewis HeadSmart Media](https://unsplash.com/@mikeanywhere?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/flow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我从 2011 年末开始在 Android 上进行开发，并且一直记得，从早期开始，我们就希望谷歌对 Android 上建议的架构发表意见:)

这确实在几年后发生了，Jetpack 的 Android 架构组件引入了**视图模型**。 **ViewModels** 是 **VM** 的一部分，属于 **MVVM** 架构，也能经受住屏幕上的配置变化。视图模型通过由 **LiveData** 实现的可观察流与 UI 通信。

不过，事实是，社区也需要一种新的方法来使异步 API 调用和数据库操作(或一般的非主线程工作)更容易，而没有 RxJava 对大多数反应式框架初学者的学习曲线。RxJava 的替代方案也很老套，更容易被像我这样不擅长原始线程和并发的人错误地使用😝！

# 输入 Kotlin 协程和流程

尽管社区最近开始转向 Kotlin 协同例程，它提供了结构化并发和 Kotlin 流，可以基本上取代我们过去使用的 RxJava 方式，以便将我们架构各层之间的数据转换为流。这可能是双向的，也可能是单向的，取决于每个项目的需要。

# 我们将要讨论的内容

在这一系列文章中，我们将看到如何创建一个新的应用程序，它获取 Github 用户的 Github 存储库，将它们保存在本地存储中，并且只观察本地存储，以便显示任何 UI 更改。我们还将看到如何在新发布的 ViewBinding 的帮助下创建 RecyclerView ListAdapter，以便免费利用不同的功能。

为了实现上述所有目标，我们将利用 Jetpack 的 Android 架构组件以及 Kotlin Coroutines + Flow 来处理我们需要在任何层上进行异步工作的部分，无论是远程 API 源，还是由 Room 支持的本地存储。

最后但同样重要的是，我们将看到如何模块化应用程序，以及如何为多模块项目配置我们的 Kotlin 风格和静态代码分析工具(Detekt+pincavity)。最后，我们将了解如何利用所有这些配置来为我们新创建的应用程序创建无缝的 CI 渠道。

# 使用 ViewBinding 创建基本 RecyclerView 适配器

为了在应用程序中为我们所有的 RecyclerViews 创建一个基本适配器，我们决定也使用新发布的 ViewBinding，并使用 ListAdapter 的功能来非常容易地实现区分。

## 创建基本适配器项目

通过这样做，我们将能够有一个单一的接口来实现和使用我们的适配器项目的公共配置。

该项只包含一个`itemViewType`属性，这样我们就可以拥有多态 RecyclerView 适配器。我们现在只需要这个属性，但是我们可以在将来添加更多的属性来丰富我们的基本功能。

## 创建基础视图固定器

我们的基本视图容器将从每个适配器中的每个视图容器扩展而来。

我们在这里做的是创建一个抽象类，并使用泛型来利用我们在上面创建的基本适配器项和该项布局的 ViewBinding。

我们的视图持有者的`bind`方法是抽象的，因为我们需要每个新的视图持有者扩展那个视图持有者来实现这个方法。

另一个可用的方法是`bind`，它有一个有效负载列表，可以帮助我们部分更新 RecyclerView 中的行。因为我们在基本适配器上覆盖了这两个方法(见下文)，所以我们需要生成那个方法`open`并提供一个调用`abstract bind(item: Item)`的默认实现，以确保当区分操作不输出任何结果时(见下文)，我们能够正确地回退。

## 创建基本适配器

我们的基本适配器将从`ListAdapter`开始扩展，因为我们希望在回收视图中使用`DiffUtil`进行区分。

我们这里的实现只是将`onBindViewHolder`方法委托给我们自己的视图持有者。我们还提供了一个扩展函数，以便在创建新的适配器时更容易访问布局生成器。

## 创建基本差异回调

我们的基本 DiffUtil 回调将从`DiffUtil.ItemCallback`扩展并使用`ViewBindingAdapterItem`作为通用输入。

# 结论

我们现在可以通过扩展已经创建的基本组件，用最少的努力创建尽可能多的适配器:)

您可以在下面的 repo 中找到上面的所有代码以及它们的用法示例，这也是我们将在这一系列文章中讨论的内容！

[](https://github.com/pavlospt/refactored-umbrella) [## pavlospt/重构-雨伞

### 重构的保护伞是一个附带项目，以检查与科特林协同程序和流量，MVVM 和 Koin 现代 Android 开发…

github.com](https://github.com/pavlospt/refactored-umbrella)