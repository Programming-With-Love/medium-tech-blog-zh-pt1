# MAD 技能系列中的分页 3.0 介绍

> 原文：<https://medium.com/androiddevelopers/introduction-to-paging-3-0-in-the-mad-skills-series-648f77231121?source=collection_archive---------1----------------------->

![](img/ec3aed893acac649e393e9ce71ba397f.png)

欢迎来到寻呼 3.0 MAD 技能系列！在本文中，我将介绍分页 3.0，并强调如何将其集成到应用程序的数据层中。想看看书？点击这里查看这一集的视频！

Paging MAD Skills episode 1

# 为什么选择分页 3.0？

向用户显示数据列表是最常见的 UI 模式之一。在处理大型数据集时，您可以通过以块为单位异步获取/显示数据来提高应用程序性能并减少内存使用。这是一个非常常见的模式，让一个库提供一个促进这一点的抽象是一个巨大的好处。这些都是分页 3.0 从容解决的用例。作为额外的好处，它还允许您的应用程序支持无限数据集，如果您正在下载网络数据，它还提供了本地缓存的途径。

如果您目前正在使用分页 2.0，分页 3.0 提供了一系列超越其前身的改进，包括:

*   对 Kotlin 协程和流的一流支持。
*   使用 RxJava Single 或 Guava ListenableFuture 原语支持异步加载。
*   响应性 UI 设计的内置加载状态和错误信号，包括重试和刷新功能。
*   对存储库层的改进，包括取消支持和简化的数据源接口。
*   对表示层、列表分隔符、自定义页面转换以及加载状态页眉和页脚的改进。

请务必查看分页 2.0 到分页 3.0 [迁移指南](https://developer.android.com/topic/libraries/architecture/paging/v3-migration)了解更多信息。

## 扔进去

在您的应用程序架构方案中，分页 3.0 最适合作为从数据层获取数据的方式，并通过`ViewModel`在 UI 层进行转换和呈现。在 Paging 3.0 中，对数据层的访问是通过一个称为`PagingSource`的类型来实现的，这个类定义了如何在由`PagingConfig`定义的边界周围获取和刷新数据。

一个`PagingSource`，类似于一个`Map`，由两个通用类型定义:其分页键的类型和加载数据的类型。例如，从基于页面的 Github API 获取`Repo`项的`PagingSource`的声明将被定义为:

PagingSource definition

PagingSource 需要实现两个抽象方法才能完全发挥作用:

1.  `load()`
2.  `getRefreshKey()`

# 加载方法

`load()`方法，就像它的名字所暗示的那样，被分页库调用来异步获取要显示的数据。这或者是为了初始加载，或者是为了响应用户的滚动。可以从作为参数传递给方法的 LoadParams 对象中确定调用 load 的触发器。它保存与加载操作相关的信息，包括以下内容:

*   待加载页面的按键:如果是第一次调用加载(初始加载)，则`LoadParams.key`为`null`。在这种情况下，您必须定义初始页面键。
*   加载大小:请求加载的项目数。

负载的返回类型为`LoadResult`。它可以是:

*   `LoadResult.Page`:成功加载。
*   `LoadResult.Error`:出现错误时使用。

load implementation

请注意，默认情况下，初始加载大小是 3 *页面大小。这确保了第一次加载列表时，如果用户滚动一点，用户将看到足够多的条目，而不会触发太多的网络请求。这也是在`PagingSource`实现中计算下一个键时要考虑的事情。

# getRefreshKey 方法

刷新键用于对`PagingSource.load()`的后续刷新调用(第一次调用是初始加载，它使用提供给寻呼机的初始键)。每当分页库想要加载新数据来替换当前列表时，例如，在滑动以刷新时，或者在由于数据库更新、配置改变、进程死亡等而无效时，都会发生刷新。通常，后续的刷新调用将希望重新开始加载以代表最近访问的索引的`PagingState.anchorPosition`为中心的数据。

getRefreshKey implementation

# 寻呼机对象

定义了`PagingSource`之后，我们现在可以创建一个`Pager`。一个`Pager`是一个类，它负责按照 UI 的请求从`PagingSource`中逐步提取数据块。由于`Pager`需要访问`PagingSource`，它通常在定义`PagingSource`的数据层中创建。

构建`Pager`需要的第二件事是`PagingConfig`，它定义了控制`Pager`如何获取数据的参数。它公开了相当多的可选参数，让您可以微调分页器的行为，但是`pageSize`参数是必需的。

Creating a Pager

以下是构建上述`PagingConfig`时使用的参数的简要描述:

*   `pageSize`:从`PagingSource`一次装载的物品数量。
*   `enablePlaceholders`:对于尚未加载的项目，`PagingData`是否返回空值。

我们通常希望`pageSize`足够大(至少足够填充屏幕的可见区域，但最好是 2-3 倍)，这样`Pager`就不必在用户滚动时反复读取数据以在屏幕上显示足够的内容。

# 获取您的数据

由`Pager`产生的类型是`PagingData`，这种类型提供了一个进入其背面的独特窗口`PagingSource`。当用户滚动列表时，分页数据将继续从`PagingSource`获取以提供内容。如果`PagingSource`无效，将发出一个新的`PagingData`，以确保分页的项目与用户界面中显示的项目同步。将`PagingData`视为某个时间点上`PagingSource`的快照可能会有所帮助。

由于`PagingSource`是在`PagingSource`无效时会发生变化的快照，因此分页库提供了多种将`PagingData`作为流使用的方式，包括:

*   柯特林流过`Pager.flow`
*   LiveData 通过`Pager.liveData`
*   RxJava 可通过`Pager.flowable`流动
*   RxJava 可通过`Pager.observable`观察到

这种`PagingData`流是`ViewModel`在将分页项目呈现给用户界面之前可以选择转换的。

# 下一步是什么？

有了以上这些，我们已经将 Paging 3.0 集成到了应用程序的数据层！请继续关注，看看如何在用户界面中使用分页数据，并填充我们的转发列表！