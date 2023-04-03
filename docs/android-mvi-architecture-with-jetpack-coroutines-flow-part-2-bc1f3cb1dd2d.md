# 带有 Jetpack 和协程/流的 Android MVI 架构—第 2 部分

> 原文：<https://medium.com/google-developer-experts/android-mvi-architecture-with-jetpack-coroutines-flow-part-2-bc1f3cb1dd2d?source=collection_archive---------0----------------------->

# 在 MVI 建筑中创建视图模型

![](img/a44e125b3b14c292ba9bc61e5d507d4d.png)

Photo by [NordWood Themes](https://unsplash.com/@nordwood?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/t/business-work?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

> 如果您还没有阅读这些系列的前一篇文章，您可以在这里找到它:
> 
> 第 1 部分— [使用视图绑定回收视图适配器](/google-developer-experts/android-mvi-architecture-with-jetpack-coroutines-flow-part-1-recyclerview-adapter-w-83a10134207f)

> 现在我们已经创建了一个基本的 RecyclerView 适配器来引导列表，我们需要看看如何获取、转换和显示屏幕数据。

# 进入视图模型

**ViewModel** 是 Jetpack 组件，我们需要对其进行扩展，以便托管其他几个组件的编排。其他组件的协调将导致实现我们的业务目标。

通常在 **ViewModel** 中，我们需要进行一些 API 调用或数据库操作，并相应地在 ViewModel 的 UI 消费者中显示我们更新的信息。

我们将展示的并且目前正在我们的示例项目中使用的组件是用例，也称为交互器。

我们更喜欢将这些用例“注入”到视图模型的构造函数中。通过这样做，我们获得了可测试性。

# **构造函数注入**

## 和 Koin 一起

如果我们使用 Koin，一个**视图模型**可以很容易地将它的依赖注入到它的构造函数中。

我们声明一个新的 Koin 模块，如下所示:

我们使用`factory`是为了总是在我们的 **ViewModel** 中注入一个新的实例(根据我们的应用程序的需求和我们的用例所做的工作，将它声明为 singleton 可能是合适的)。我们的**用例**的其余依赖项来自图的其余部分(我们将在以后的文章中对此进行分析)。

## 用匕首

为了在使用 **Dagger** 时通过构造函数注入依赖关系，我们需要创建一个 **ViewModelFactory** ，并通过类名将我们的**viewmodel**绑定到 **Dagger** 图。

该注入的一个快速实现如下:

# 视图模型中的用户操作消耗

因为我们想要的架构是 MVI 架构，所以我们需要创建一个用户动作/意图流，我们的视图模型将消费和操作它。

我们在示例项目中使用的方法如下:

我们的**视图模型**在这里使用了一个`ConflatedBroadcastChannel`，它在我们的`init`方法中被转换为一个`Flow`。在每一次发射中，我们都调用了等价的`suspend fun`，它要么产生一些数据在我们的 UI 上传播，要么做一些其他的工作！

# 向我们的 UI(生命周期感知)组件公开数据

既然我们能够消费用户动作/意图，我们需要看看如何将数据传播到我们的 UI。

为了做到这一点，我们更愿意使用数据库作为我们唯一的真实来源，并产生一个数据流，我们可能会在委托给我们的 UI 之前对其进行预处理。

例如，我们将观察本地数据库中的一个表，其中存储了我们所有的 Github 库(我们将在以后的文章中研究如何获取它们)。

我们注入的**用例**在这里可以被观察，以便在它被调用后返回一个**流< T >** 来产生它的工作。对**用例**的调用是在我们的`init`方法的末尾完成的。

然后，我们将发出的数据库实体`map`到我们的适配器项中，并在将它们“发送”到我们的 UI(在我们的例子中是一个**片段**)之前，将它们转换成 **LiveData** 。最后但同样重要的是，由于我们使用了 Flow，我们需要使用`viewModelScope.coroutineContext`来利用我们的**流**的内置取消。

我们现在需要做的就是观察这个 LiveData，并将商品列表提交给我们的适配器。考虑到我们根据上一篇文章中所做的工作来适当区分我们的项目，我们可以确信，尽管我们的 DB 表中有大量的无效项，但我们的适配器中所需的更新是最少的。

这里我们可以做的另一个增强是使用`.distinctUntiChanged()`操作符来避免相同的数据库排放。

> 免责声明:用例实现的灵感来自于 [Chris Banes](https://medium.com/u/9303277cb6db?source=post_page-----bc1f3cb1dd2d--------------------------------) TiVi repo

## 结论

这是一个基于我们所看到的对我们有用的 MVI 架构的建议方法:)我们将在下面的一篇文章中讨论我们的**视图模型**的测试部分！欢迎任何改进或建议！#安全停留

您可以在下面的 repo 中找到上面的所有代码以及它们的用法示例，这也是我们将在这一系列文章中讨论的内容！

[](https://github.com/pavlospt/refactored-umbrella) [## pavlospt/重构-雨伞

### 重构的保护伞是一个附带项目，以检查与科特林协同程序和流量，MVVM 和 Koin 现代 Android 开发…

github.com](https://github.com/pavlospt/refactored-umbrella)