# 剑柄稳定！Android 上更容易的依赖注入

> 原文：<https://medium.com/androiddevelopers/hilt-is-stable-easier-dependency-injection-on-android-53aca3f38b9c?source=collection_archive---------2----------------------->

![](img/3c8627f9aad93b353d82fedf56062c97.png)

[剑柄](https://developer.android.com/training/dependency-injection/hilt-android)，Jetpack 推荐的[依赖注入(DI)](https://developer.android.com/training/dependency-injection) 安卓应用解决方案，已经**稳定**！这意味着 Hilt 已经完全可以用于生产。Hilt 比 Dagger 更简单，使你能够编写更少的样板代码，它是为 Android 设计的，并与多个 Jetpack 库集成。一些公司已经开始在他们的应用中利用 Hilt。

2020 年 6 月，Hilt】首次以 alpha 版本发布，其使命是定义一种**标准方式**在你的 Android 应用中进行 DI，从那以后，我们收到了来自开发者的大量反馈。这不仅改进了图书馆，而且让我们知道我们正在解决正确的问题。

与手动创建依赖图以及在需要的地方手动注入和传递类型不同，Hilt 会在编译时通过注释的方式自动为您生成所有代码。Hilt 可以帮助你**在你的应用程序中最大限度地利用 DI 最佳实践**,方法是努力工作并**生成你原本需要编写的所有样板文件**。此外，由于它与 Android 完全集成，Hilt 自动为您管理与 Android 框架类*相关的依赖图的生命周期。*

让我们用一个简单的例子来看看 Hilt 的作用吧！在[设置了 Hilt up](https://developer.android.com/training/dependency-injection/hilt-android#setup) 之后，在您的项目中从头开始使用它在活动中注入一个 ViewModel 就像在您的代码中添加一些注释一样简单，如下所示:

除了上面提到的，你为什么要在你的 Android 应用中使用 Hilt？

# 比匕首简单

Hilt 构建在流行的 DI 库 [Dagger](https://developer.android.com/training/dependency-injection/dagger-basics) 之上，因此受益于 Dagger 提供的编译时正确性、运行时性能、可伸缩性和 [Android Studio 支持](/androiddevelopers/dagger-navigation-support-in-android-studio-49aa5d149ec9)。一些 Dagger 注释，比如@Inject 告诉 Dagger 和 Hilt 如何提供一个类型的实例，经常在 Hilt 中使用。但是剑柄比匕首简单！

*“我强烈推荐在 Android 应用中利用 Dagger 进行依赖注入。但是，纯香草匕首会导致太多的创意空间。当这与 Android 开发中各种生命周期感知组件的复杂性混合在一起时，就有足够的空间来陷入诸如内存泄漏之类的陷阱:例如，不小心将活动范围的依赖项传递到视图模型中。Hilt 坚持己见，专为 Android 设计，这有助于你避免使用 vanilla Dagger 时的一些陷阱。”* — [Marcelo Hernandez](https://twitter.com/mhernand40) ，Tinder 软件工程师

如果你已经在你的应用程序中使用 Dagger，并想迁移到 Hilt…不用担心！匕首和剑柄可以共存，应用程序可以根据需要进行迁移。

# 较少样板文件

Hilt 是固执己见的——这意味着它替你做决定，这样你就有更少的代码要写。Hilt 定义了标准组件或依赖图，完全集成了 Android 框架类，如应用程序、活动、片段和视图。以及将类型的实例范围限定到那些组件的范围注释。

*“Hilt 通过@HiltAndroidTest 自动生成测试应用和测试组件。在迁移到 Hilt 之后，我们能够删除 20%到 40%的样板连线测试代码！”* —孙菊·李，YouTube 软件工程师

*“就移植到剑柄而言，我们只是触及了皮毛。然而，在我们迁移到 Hilt 的一个模块中，我们看到了+78/-182，这是针对这个库更改的行数。”* — Marcelo Hernandez，Tinder 软件工程师

# 专为 Android 设计

与 Dagger 不同，Dagger 是 Java 编程语言应用程序的依赖注入解决方案，而 Hilt 只在 Android 应用程序中工作。一些注释，如

`@HiltAndroidApp`、`@AndroidEntryPoint`或`@HiltViewModel`是特定的，定义了一种在 Android 上进行 DI 的自以为是的方式。

*“Hilt 终于提供了内置的 Android 生命周期感知 Dagger 组件。有了 Hilt，我们可以专注于 Dagger @模块，而不必担心组件、子组件、组件提供者模式等等。”* — Marcelo Hernandez，Tinder 软件工程师

# 组件和绑定的标准化

对于那些了解 Dagger 的人来说，Hilt 通过使用一个[单片组件系统](https://dagger.dev/hilt/monolithic)来简化依赖图，从而在编译时生成更少的代码。

有了 Hilt 的单片组件系统，绑定定义只需提供一次，就可以在所有使用该组件的类之间共享。这是一个巨大的胜利，因为之前，YouTube 使用了多石组件系统，其中模块被手动连接到定制组件中，并且有许多重复的绑定定义。” —孙菊·李，YouTube 软件工程师

*“由于我们的梯度模块分离允许功能独立开发，当使用 Dagger 时很容易变得太有创造性。我们发现，将这些模块迁移到 Hilt 实际上暴露了我们无意中违反了关注点分离的缺陷。”* — Marcelo Hernandez，Tinder 软件工程师

# 与其他 Jetpack 库集成

你可以使用你最喜欢的 Jetpack 库。到目前为止，我们提供了对**视图模型、工作管理器、导航和组合**的直接注入支持。

在[文档](https://developer.android.com/training/dependency-injection/hilt-jetpack)中了解更多关于 Jetpack 支持的信息。

*“我真的很欣赏 Hilt 开箱即用的视图模型，以及它如何消除必须建立视图模型的样板文件。带有香草匕首的工厂提供商。”* — Marcelo Hernandez，Tinder 软件工程师

# 学习剑柄的资源

Hilt 是 Jetpack 推荐给 Android 应用的 DI 解决方案。要了解更多信息并开始在您的应用中使用它，请查看以下资源:

*   点击了解依赖注入[的好处。](https://developer.android.com/training/dependency-injection)
*   [文档](https://developer.android.com/training/dependency-injection/hilt-android)了解如何在你的应用中使用 Hilt。
*   [从匕首到刀柄的迁移指南](https://dagger.dev/hilt/migration-guide)。
*   Codelabs 以循序渐进的方式学习 Hilt:[在你的 Android 应用中使用 Hilt](https://codelabs.developers.google.com/codelabs/android-hilt)和[从 Dagger 迁移到 Hilt](https://codelabs.developers.google.com/codelabs/android-dagger-to-hilt) 。
*   代码示例！在[谷歌 I/O 2020](https://github.com/google/iosched) 和[向日葵](https://github.com/android/sunflower/)应用中查看运行中的希尔特。
*   [备忘单](https://developer.android.com/images/training/dependency-injection/hilt-annotations.pdf)快速查看*不同的刀柄和匕首注释做什么*以及*如何使用它们*。

> 非常感谢 Android Tinder 团队的[**Marcelo Hernandez**](https://medium.com/u/974eff7f1bc5?source=post_page-----53aca3f38b9c--------------------------------)([Twitter 个人资料](https://twitter.com/mhernand40))和 Android YouTube 团队的**孙菊·李**花时间谈论他们如何以及为什么在应用中采用 Hilt。