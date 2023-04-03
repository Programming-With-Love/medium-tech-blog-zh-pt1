# 一个常见问题。

> 原文：<https://medium.com/androiddevelopers/jetpack-compose-accompanist-an-faq-b55117b02712?source=collection_archive---------0----------------------->

![](img/c879e02f3b526311f4a38fa079fca564.png)

伴奏者是一组库，旨在为 [Jetpack Compose](https://developer.android.com/jetpack/compose) 补充开发人员通常需要的特性，但在 Jetpack Compose AndroidX 库中尚未得到官方支持。

伴奏者是一个类似实验室环境，用于新的组合 API。我们使用它来帮助填补 Compose toolkit 中已知的空白，试验新的 API，并收集对开发 Compose 库的开发经验的见解。伴奏者的目标是不存在。我们希望将这些库放入官方工具包，在这一点上，它们将被弃用并从伴奏中移除。

目前在伴奏中你可以找到[流程布局](https://google.github.io/accompanist/flowlayout/)、[页面](https://google.github.io/accompanist/pager/)、[导航过渡](https://google.github.io/accompanist/navigation-animation/)、[滑动刷新](https://google.github.io/accompanist/swiperefresh/)和[更多](https://google.github.io/accompanist/)的库。

我们经常被问到很多关于这个图书馆的问题，所以我想我们可以写一个帖子来回答一些最常见的伴奏问题。

# 伴奏者为什么存在？

伴奏者存在的一个主要原因是 AndroidX 不允许跨不同模块使用实验性 API。有些我们知道可以开发的功能在 AndroidX 中是不允许的。例如，[导航转换](/androiddevelopers/animations-in-navigation-compose-36d48870776b)需要一些实验性的动画 API，所以不能在导航合成中构建，但是可以在伴奏中构建。

对于独立的特性，你可能会想，为什么不在 AndroidX 中将 API 标记为实验性的呢？当我们向 AndroidX 添加一个实验性的 API 时，我们确信这个特性会以某种形式存在，但是 API 可能会改变形状。我们也试图让他们尽快脱离实验，我们知道这并不总是发生，但这是我们的目标。当我们需要它们作为实验更持久或者不确定特性有意义时，伴奏者就是它们生活的地方。

下表总结了适用于不同 API 位置的不同规则。这些规则也将在后面的文章中详细介绍。

伴奏者的目标是最终不存在，随着 AndroidX Compose 库的成熟和稳定，所有的功能都被上游化。

# 历史

伴奏者存在的另一个主要原因是历史。伴奏者最初是在 Compose 早期由[克里斯·贝恩斯](http://twitter.com/chrisbanes)和 Compose 团队开发的。Compose 团队正在开发 [Compose 样本](http://goo.gle/compose-samples)，由于 Compose 仍处于非常早期的开发阶段，所以在他们需要的特性集上有很多缺口。既有 Compose 本身原因，也有其他库缺乏早期支持的原因。该团队编写了大量代码来使应用程序工作。伴奏者是作为一个发布这些代码供他人使用的地方而创建的。第一个伴奏库是一个[线圈](https://coil-kt.github.io/)包装器，用于异步加载图像。接下来添加了窗口插入处理，接着是 [AppCompat 主题适配器](https://google.github.io/accompanist/#appcompat-theme-adapter)，然后在 0.7.0 中又添加了 4 个库。[分页器](https://google.github.io/accompanist/pager/)、[分页指示器](https://github.com/google/accompanist/tree/main/pager-indicators)、[系统 UI 控制器](https://google.github.io/accompanist/systemuicontroller)和[流程布局](https://google.github.io/accompanist/flowlayout/)。这也是库从 Chris 的个人账户转移到 Google 官方库的时候，包名也相应地从`dev.chrisbanes.accompanist`改为`com.google.accompanist`。此时，Compose 团队和社区的多个成员正在开发和贡献这个库，它从那里开始发展。

目前我们有 12 个活跃的库和 1 个废弃的库( [Insets](https://google.github.io/accompanist/insets/) ,因为它已经被上游化了)。我们的目标是从伴奏者那里获取最常用的特性，并将它们上传到主作曲库中。这最初是用第一个伴奏库 Coil 成功完成的，随后用 Insets 完成了作曲基础。在接下来的几个 Compose 版本中，我们将加大我们的上游工作，API 可能会在上游过程中发生变化，但我们致力于为所有用户提供一个简单的迁移路径。随着[合成路线图](http://goo.gle/compose-roadmap)的更新，你可以看到，下一个被上游化的 API 是 Flow Layout。

# 什么是上游？

主要的 Compose 库是在 AndroidX 中开发的，这是我们的 Android 库的开源库。这组库被称为 Jetpack。然而伴奏是与主作曲库分开开发的。当我们谈论“上游化”时，我们的意思是将伴奏者的开发转移到主 Compose 库，作为一个官方特性。

# 为什么它生活在 GitHub 而不是 AndroidX？

伴奏者是在 GitHub 上开发的，是开放的。如上所述，这种情况的主要原因只是历史。最初由一个作者开发的库，发展成为由多个作者开发的库集合。然而，它仍然保留在 GitHub 上有一些重要的优势和原因。

## AndroidX 实验 API

如上所述，AndroidX 不允许跨模块实验 API。这意味着，合成导航将无法使用使导航转换成为可能的实验性合成动画 API。底部工作表导航器将不能使用实验材料 API，等等。因为这些是我们知道社区需要的特性，所以在伴奏中开发它们是理想的。它允许我们将特性发布给开发人员，而不必等待删除实验性注释的 Compose 版本。

## 二进制兼容性

AndroidX 也有严格的二进制兼容性规则，这使得在不影响主要版本号的情况下更改 API 很困难。这些规则的存在有一个很好的理由，因为 API 一直在变化会使用它们进行开发成为一种负担。伴奏者试图在不同版本之间不彻底改变 API 结构，但并不保证。当我们在 AndroidX 中发布一个 API 时，我们可能会改变它，但我们希望我们不必这样做。如果我们不确定某个 API，我们会将其标记为实验性的。在伴奏中，我们可以自由地进行更多的实验，这对于我们在没有社区反馈的情况下不能确定的事情来说是非常好的。

## 社区贡献

在你第一次投稿之前，AndroidX 有一条相当陡峭的入门之路，它也很难在本地构建和运行。GitHub 允许更容易的社区贡献，这是我们鼓励的。因为我们在开发人员已经熟悉的系统中开发，我们得到了比其他方式更多的贡献，例如[导航材料](https://google.github.io/accompanist/navigation-material/)是由[乔西·沃尔夫](http://twitter.com/jossiwolf)在加入谷歌之前开发的！

## 洞察作曲体验

在 GitHub 上开发让我们对外部开发人员面临的问题有了有价值的了解。仅仅停留在 AndroidX 上，我们经常会忽略对外部开发人员来说显而易见的问题。通过在 GitHub 中开发，我们可以更早地发现问题。

# 你多久发布一次伴奏？

我们为每次发布的 Compose 发布一个新版本的伴奏。

# 伴奏在生产中使用安全吗？

我们相信是的！在 Play Store 上使用 Compose 的应用中，只有不到 30%的应用使用伴奏者，而且这一数字还在增长。Play Store 应用程序本身以及其他几个谷歌应用程序也使用它。伴奏与你可能导入到应用程序中的任何其他库没有什么不同。我们尽一切努力维护它，修复错误，并保持在问题的顶端。在将源代码添加到你的应用程序之前，你应该经常自己检查源代码，看看你是否满意，就像你使用的其他库一样。

# 明天它会消失吗？

我们的目标是尽我们所能将最常用的特性上传到主 Compose 库中，我们将很快对更多的库这样做。虽然我们不会立即将它们从伴奏中移除。任何时候一个库被上游化，我们都会在至少一个稳定的 Compose 版本中保留一个不推荐使用的版本。我们还将提供一个迁移指南或`@ReplaceWith`注释，使从伴奏版迁移到官方版变得容易。

# 为什么我总是要把我的伴奏版本和我的作曲版本匹配起来？

您应该始终匹配您的作曲 UI 版本和伴奏版本。我们总是在[伴奏者自述文件](https://github.com/google/accompanist#compose-versions)中显示每个受支持的 Compose 版本的最新版本。这对于主要版本(x.0.0)和次要版本(1.x.0)来说尤其重要，就好像您使用新版本的伴奏器和旧版本的 Compose UI，您的项目将被升级到新版本的 Compose UI [过渡到](https://docs.gradle.org/current/userguide/dependency_management_terminology.html#sub:terminology_transitive_dependency)。例如:

伴奏者 0.26.0-alpha 构建于作曲 UI 1.3.0-alpha02 之上。如果您的项目使用 Compose UI 1.2.1，并且您添加了伴奏者 0.26.x，那么您将把您的 Compose 版本从 1.2.1 升级到 1.3.0-alpha02。这会给你的项目带来意想不到的突破性变化！

# 你如何处理 Compose 编译器升级？

我们目前的策略是，对于伴奏者的 alpha/beta/rc 版本，我们将始终使用编译器的最新版本，无论它是否稳定。对于与 Compose UI 的稳定版本相匹配的伴奏者版本，我们将使用编译器的最新稳定版本。我们也将保持伴奏者的最新稳定版本与最新稳定编译器版本同步。

例如，在写作时，Compose UI 1.2.1 是稳定的，我们有伴奏者 0.25.1 与之匹配。0.25.1 是用使用 Kotlin 1.7.10 的 Compose 编译器 1.3.0 构建的。当下一个稳定的编译器发布时，我们将创建一个新版本的伴奏者，它仍然以 Compose UI 1.2.1 为目标，但使用新的编译器构建，以便您可以升级项目的 Kotlin 版本。

一旦 Compose UI 1.2 不再是最新的稳定版本，我们将不再为其匹配的伴奏者版本提供编译器更新。

# 你接受来自社区的新特性/库吗？

没有一个谷歌所有者与你合作，不，我们不会再有了。现在 Compose 已经发布且稳定，我们希望图书馆生态系统能够成长！我们不希望每个库都以伴奏结束，这只会让开发者感到困惑。我们希望你能发布你的特性作为一个自己的库。如果你认为你的库在伴奏中有意义，请首先打开一个问题与我们讨论，我们可能会找到一个所有者与你合作。

我们肯定接受并鼓励对现有库的错误修复和改进。如果你有一个大的贡献，就像任何开源库一样，最好通过 GitHub 问题联系我们，看看我们是否能支持它。

# 你会发布更多的伴奏库吗？

刚刚发布了一个来帮助[自适应布局](https://github.com/google/accompanist/pull/1281)，但这是目前唯一计划的新库。除了伴奏，我们还出版并开发了[钟表师](https://github.com/google/horologist)，以类似的方式帮助作曲。

我们知道在 AndroidX 之外有一个单独的库会让开发人员感到困惑，并且在迁移过程中会产生额外的工作，所以我们只想在有明确的上行路径并且明确需要在伴奏中进行实验的地方向伴奏添加新的库。