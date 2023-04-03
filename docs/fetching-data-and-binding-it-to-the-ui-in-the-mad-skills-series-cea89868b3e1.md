# 获取数据并将其绑定到 MAD 技能系列中的 UI

> 原文：<https://medium.com/androiddevelopers/fetching-data-and-binding-it-to-the-ui-in-the-mad-skills-series-cea89868b3e1?source=collection_archive---------2----------------------->

![](img/ec3aed893acac649e393e9ce71ba397f.png)

## 疯狂寻呼技巧系列的第二集

欢迎回到寻呼 3.0 MAD 技能系列！在之前的[文章](/androiddevelopers/introduction-to-paging-3-0-in-the-mad-skills-series-648f77231121)中，我们介绍了分页库，了解了它如何适应应用的架构，并将其集成到应用的数据层中。我们使用一个`PagingSource`来获取我们的数据，然后使用它和一个`PagingConfig`来创建一个为 UI 消费提供一个`Flow<PagingData>`的`Pager`对象。在这篇文章中，我将讲述如何在你的用户界面中使用`Flow<PagingData>`。

# 为用户界面准备分页数据

该应用程序目前有一个`ViewModel`,它在`UiState`数据类中公开了呈现 UI 所需的信息，该数据类包含一个`searchResult`字段；内存中的缓存，用于保存配置更改后的结果搜索。

Initial UiState Definition

在分页 3.0 中，我们从`UiState`中去掉了`searchResult` val，取而代之的是用`PagingData<Repo>`的`Flow`来代替，它与`UiState`分开暴露。这个新的`Flow`将与`searchResult`的目的相同:提供一个由 UI 呈现的项目列表。

在`ViewModel`中增加一个私有方法`searchRepo()`，调用`Repository`从`Pager`中提供一个`PagingData` `Flow`。然后，我们可以调用这个方法，根据用户输入的搜索词创建我们的`Flow<PagingData<Repo>>`。我们还在生成的`PagingData` `Flow`上使用了`cachedIn`操作符，该操作符使用`ViewModelScope`缓存它以便更快地重用。

PagingData Flow integration from the repository

**重要的是要揭露** `**PagingData**` `**Flow**` **独立于其他** `**Flows**` **。**这是因为`PagingData`本身是一个可变类型，它维护着自己的内部数据流，这些数据流会随着时间的推移而更新。

完全定义了包含`UiState`字段的`Flows`后，我们可以将它们组合成`UiState`的`StateFlow`，然后可以与`PagingData`的`Flow`一起暴露给 UI，并由 UI 使用。有了以上内容，我们现在可以开始在 UI 中使用我们的`Flows`。

Exposing the PagingData Flow to the UI. Note the use of the cachedIn operator

# 在用户界面中使用分页数据

我们做的第一件事是将`RecyclerView`和`Adapter`从`ListAdapter`切换到`PagingDataAdapter`。一个`PagingDataAdapter`是一个`RecyclerView` `Adapter`，优化用于区分和聚集来自`PagingData`的更新，以确保后台数据集中的更改尽可能高效地传播。

Switching from a ListAdapter to a PagingDataAdapter

接下来，我们开始从`PagingData` `Flow`收集，因此我们可以使用`submitData`暂停函数将其排放绑定到`PagingDataAdapter`。

Consuming PagingData with a PagingDataAdapter. Note the use of the collectLatest operator

此外，作为用户体验津贴，我们希望确保当用户搜索新事物时，他们被带到列表顶部的**以显示第一个搜索结果。当我们确信**已经完成加载并在 UI** 中呈现数据时，我们希望这样做。我们通过利用由`PagingDataAdapter`暴露的`loadStateFlow`和`UiState`中的`hasNotScrolledForCurrentSearch`字段来实现这一点，如果用户自己手动滚动了列表，则用于跟踪。这两者的组合创建了一个标志，让我们知道是否可以触发自动滚动。**

由于`loadStateFlow`提供的负载状态与 UI 中显示的内容是同步的，所以一旦负载状态流通知我们没有为每个新查询加载，我们就可以放心地滚动到列表的顶部。

Implementing auto scroll to the top for new queries

# 添加页眉和页脚

分页库的另一个优点是能够借助`LoadStateAdapter`在列表的顶部或底部显示进度指示器。当加载数据时，`RecyclerView.Adapter`的这个实现自动被通知`Pager`中的变化，这使它能够根据需要在列表的顶部或底部插入项目。

最好的部分是你甚至不需要改变你现有的`PagingDataAdapter`。`withLoadStateHeaderAndFooter`扩展方便地用页眉和页脚包装您现有的`PagingDataAdapter`！

Headers and Footers

`withLoadStateHeaderAndFooter`函数的参数接受页眉和页脚的`LoadStateAdapters`定义。`LoadStateAdapter’s`又拥有自己的`ViewHolders`，它们与最新的加载状态绑定在一起，使得定义视图的行为变得容易。我们甚至可以传递参数，让我们在出现错误的情况下重试加载，这将在下一篇文章中详细介绍。

# 接下来

这样我们就把分页数据绑定到 UI 上了！简单回顾一下，我们:

*   使用 PagingDataAdapter 将分页集成到 UI 层中
*   使用 PagingDataAdapter 公开的 LoadStateFlow 来保证我们只在页面加载完成时自动滚动到列表的顶部
*   提取数据时，使用 withLoadStateHeaderAndFooter()向用户界面添加进度条

感谢您的阅读！请继续关注，在下一篇文章中再见，我们将把来自数据库的分页视为一个真实的来源，并进一步研究`LoadStateFlow`！