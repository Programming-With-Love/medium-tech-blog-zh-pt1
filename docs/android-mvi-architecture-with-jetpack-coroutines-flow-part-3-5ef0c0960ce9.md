# 带有 Jetpack 和协程/流的 Android MVI 架构—第 3 部分

> 原文：<https://medium.com/google-developer-experts/android-mvi-architecture-with-jetpack-coroutines-flow-part-3-5ef0c0960ce9?source=collection_archive---------4----------------------->

# 创建协同程序/流授权用例

![](img/c5a43b23e3dcc86567e2e2a362a99274.png)

Photo by [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

> 如果您没有读过这些系列的前几篇文章，您可以在这里找到它们:
> 
> [第 1 部分—具有 ViewBinding 的 RecyclerView 适配器](/google-developer-experts/android-mvi-architecture-with-jetpack-coroutines-flow-part-1-recyclerview-adapter-w-83a10134207f)
> 
> [第 2 部分—在 MVI 架构中创建视图模型](/google-developer-experts/android-mvi-architecture-with-jetpack-coroutines-flow-part-2-bc1f3cb1dd2d)

> 在本文中，我们将看到如何创建将在我们的视图模型中使用的不同类型的用例。

# 什么是用例？

> 一个**用例**是通常定义角色(在[统一建模语言](https://en.wikipedia.org/wiki/Unified_Modeling_Language) (UML)中称为 [*参与者*](https://en.wikipedia.org/wiki/Actor_(UML)) )和系统之间交互的动作或事件步骤列表，以实现一个目标。参与者可以是人或其他外部系统。在系统工程中，用例在比[软件工程](https://en.wikipedia.org/wiki/Software_engineering)更高的层次上使用，通常代表使命或[利益相关者](https://en.wikipedia.org/wiki/Project_stakeholder)的目标。然后，详细的需求可能被捕获在[系统建模语言](https://en.wikipedia.org/wiki/Systems_Modeling_Language) (SysML)中，或者作为契约声明。

这是维基百科[这里](https://en.wikipedia.org/wiki/Use_case)提供的定义。

> 但是在我们的项目中，什么是真正的用例呢？

在许多应用程序中，我们的业务逻辑的某些部分涉及到几个本地或远程资源的协调，以便实现一个目标。

例如，每当我们进行 API 调用来获取 Github 存储库的信息时，我们也希望存储这些信息或者更新这些信息，以防我们已经存储了这个 Github 存储库的信息。这是一个可以从应用程序中的几个地方触发的业务流。

由于这种需要，我们只需编写一次，并单独测试该组件:)

# 输入用例

我们的**用例**例子深受克里斯·班斯的《TIVI [回购](https://github.com/chrisbanes/tivi)的影响。

## FlowUseCase

每当我们有可观察的/可流动的结果(例如:数据库变化)时，我们都希望以`Flow<T>`的形式扩展 **FlowUseCase** 。

我们这里的抽象类实现了定义属性和方法的`ObservableUseCase<T>`。

*   `dispatcher`是 Kotlin 协程调度程序，我们将使用它来执行用例的工作。通常，对于网络或数据库操作，这可以是`Dispatchers.IO`,或者如果我们想要执行 CPU 密集型任务，这可以是`Dispatchers.Default`。
*   `observe`是将返回一个包含用例操作结果的`Flow<T>`的方法。

`FlowUseCase<Params: Any, Type : Any>`抽象类包含一个接受`Params`类型的通道。`Params`可以是我们的用例需要的任何类型的数据，以便执行它的动作并产生结果。

正如我们在这里看到的，`invoke`操作符被覆盖，以便将`Params`输入发送到我们的内部通道。

然后，通道被转换为`Flow`并被`flatMap`d，以便执行产生`Flow<T>`结果的方法，该结果为`doWork(Params)`。

## NoResultUseCase

每当我们想执行一个动作而不关心任务的实际成功或失败时，我们都想扩展 **NoResultUseCase** ，因为它可能对我们的业务逻辑不重要，或者不会影响我们的用户。

在这个**用例**中，我们需要做的就是根据我们之前的解释，提供最适合我们需求的`Dispatcher`。然后，它将把协程上下文切换到那个`Dispatcher`并执行在`run`方法中给出的任何动作。

## 结果用例

每当我们有一个想要执行的动作时，我们都想要扩展 **ResultUseCase** ，但是我们实际上关心它的结果。这可能是因为我们的产品还需要进行错误处理，或者通知用户操作没有成功完成。

如前所述，我们需要指出我们希望用例使用的`Dispatcher`。这里我们的`run`方法有一个返回类型。在我们的例子中，这种类型是`Result`，它可以有两种不同的形式。可以是`Success`也可以是`Failure`。通常我们可以用一个密封的类创建一个非常简单的`Result`类，但是通常这是基于项目的需要。

# 结论

上面的基本用例类通常足以满足我们的项目需求，但是就像我常说的“视情况而定”。在后面的文章中，我们还将探索孤立地测试我们的用例是多么容易。

您可以在下面的 repo 中找到上面的所有代码以及它们的用法示例，这也是我们将在这一系列文章中讨论的内容！

[](https://github.com/pavlospt/refactored-umbrella) [## pavlospt/重构-雨伞

### 重构的保护伞是一个附带项目，以检查与科特林协同程序和流量，MVVM 和 Koin 现代 Android 开发…

github.com](https://github.com/pavlospt/refactored-umbrella)