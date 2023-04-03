# 继续深入，MAD 技能系列中的网络和数据库分页

> 原文：<https://medium.com/androiddevelopers/going-deeper-paging-from-network-and-database-in-the-mad-skills-series-9c98250b246b?source=collection_archive---------0----------------------->

![](img/ec3aed893acac649e393e9ce71ba397f.png)

欢迎回来！在第一集[最后一集](/androiddevelopers/fetching-data-and-binding-it-to-the-ui-in-the-mad-skills-series-cea89868b3e1)中，我们将一个`Pager`集成到我们的`ViewModel`中，并用它来填充使用`PagingDataAdapter`的 UI。我们还考虑了添加负载状态指示器和出错时重试的问题。

这一次，我们要更上一层楼。到目前为止，我们一直直接从网络上获取数据，这种方式只能在最好的情况下使用。有时，我们的网络连接速度很慢，或者完全失去了连接。即使我们的连接是好的，我们当然不希望我们的应用程序成为一个数据猪，因为每次你导航到一个屏幕时重新获取数据是浪费。

这些问题的解决方案是让我们从本地缓存中取出一个**并仅在必要时刷新。对缓存的更新应该总是首先命中缓存，然后传播到`ViewModel`。这样，本地缓存就是唯一的真实来源。对我们来说很方便，分页库在房间库的帮助下解决了这个问题！让我们开始吧！**

# 创建带有空间的分页源

因为我们将要分页的数据源将来自我们的本地数据库，而不是直接来自 API，所以我们要做的第一件事是更新我们的`PagingSource`。好消息是我们几乎不用做太多。我之前提到的那个房间的小帮手？事实证明，事情远不止如此:从房间`DAO`中获取一个`PagingSource`就像在`DAO`中为它添加一个定义一样简单！

在`GitHubRepository`中，我们现在可以更新`Pager`的构造以使用新的`PagingSource`:

# 远程调解人

这很好，但我们遗漏了一些东西。本地数据库是如何填充的？进入`[RemoteMediator](https://developer.android.com/reference/kotlin/androidx/paging/RemoteMediator)`；当`PagingSource`从数据库中加载的项目用完时，这个类负责从网络中获取更多的数据。让我们看看它是如何工作的。

关于`RemoteMediator`，需要注意的一点是，它是一个回调函数。来自`RemoteMediator`的结果永远不会原样返回给 UI，这只是分页通知开发人员`PagingSource`用完了数据的方式。我们的工作是更新数据库，并告诉分页，数据库中有新的数据。与`PagingSource`类似，`RemoteMediator`的查询参数和结果类型是通用的。

让我们仔细看看`RemoteMediator`中的抽象方法。第一种方法是`initialize()`方法。这是在任何加载开始之前对`RemoteMediator`的第一次调用，并返回一个`InitializeAction`。`InitializeAction`或者是`LAUNCH_INITIAL_REFRESH`，这将导致用刷新加载类型调用`load()`方法，或者是`SKIP_INITIAL_REFRESH`，这将导致`RemoteMediator`不刷新，除非 UI 明确请求它。在我们的例子中，由于 repo stats 可能经常更新，所以我们返回`LAUNCH_INITIAL_REFRESH`。

接下来是`load`方法。在由`loadType`和`PagingState`定义的边界处调用`load`方法，其中负载类型可以是`refresh`、`append`或`prepend`。它负责获取数据，将数据保存到磁盘并通知结果，结果可以是`Error`或`Success`。如果是`Error`，加载状态将会反映出来，加载可能会重试。然而，如果成功，则需要通知`Pager`是否可以获取更多数据。

因为`load`方法是一个返回结果的挂起函数，所以 UI 能够准确地反映正在完成的工作的状态是很重要的。在上一篇[文章](/androiddevelopers/fetching-data-and-binding-it-to-the-ui-in-the-mad-skills-series-cea89868b3e1)中，我们简要介绍了`withLoadStateHeaderAndFooter`扩展，并看到了如何使用它来显示加载的页眉和页脚。仔细查看扩展的名称可以发现一种类型，即`LoadState`。让我们再看一遍这种类型。

# 加载状态、加载状态和组合加载状态

因为分页是一系列异步事件，所以 UI 反映正在获取的数据的当前状态非常重要。分页时，`Pager`的装载状态用`CombinedLoadStates`类型表示。

顾名思义，这个类是传递加载信息的其他类型的组合。这些其他类型是:

`LoadState`:完整描述加载状态的密封类；

*   `Loading`
*   `NotLoading`
*   `Error`

`LoadStates`:包含`LoadState`值的数据类，用于:

*   `append`
*   `prepend`
*   `refresh`

通常，`prepend`和`append`加载状态用于对额外的数据获取做出反应，而刷新加载状态用于对初始加载、刷新和重试做出反应。

由于`Pager`可能从`PagingSource`或`RemoteMediator`加载，因此`CombinedLoadStates`数据类有两个`LoadState`字段，一个用于名为`source`的`PagingSource`，另一个用于名为`mediator`的`RemoteMediator`。

为了方便起见，`CombinedLoadStates`也有类似于`LoadStates`的`refresh`、`append`和`prepend`字段，这些字段将根据您的分页配置和其他语义来反映`RemoteMediator`或`PagingSource`的`LoadState`。请务必查看关于不同场景中字段行为的文档。

使用这些信息来更新我们的 UI 就像从由`PagingAdapter`公开的`loadStateFlow`中收集一样简单。在我们的应用程序中，我们可以使用它在第一次加载时显示一个加载微调器。

我们从`Flow`开始收集，如果`Pager`没有加载并且现有列表为空，使用`CombinedLoadStates.refresh`字段显示进度条。我们使用`refresh`字段，因为我们只想在第一次启动应用程序时显示大进度条，或者因为我们明确触发了刷新。我们还可以检查是否有任何装载状态出错，并通知用户。

# 概述

感谢您的阅读！概括地说，我们:

*   从数据库中调出，作为事实的单一来源
*   用一个`RemoteMediator`给房间喂食的基础`PagingSource`
*   根据`PagingAdapter’s` `LoadStateFlow`中的负载状态更新了带有进度条的用户界面

我们将在下一篇文章中结束这个系列，所以请继续关注，稍后见！