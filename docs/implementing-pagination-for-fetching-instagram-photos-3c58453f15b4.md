# 实现获取 Instagram 照片的分页

> 原文：<https://blog.kotlin-academy.com/implementing-pagination-for-fetching-instagram-photos-3c58453f15b4?source=collection_archive---------2----------------------->

在开始本教程之前，我推荐你阅读一下 Android 中的[分页库](https://developer.android.com/topic/libraries/architecture/paging)。

这里，我们将使用 MVVM 模式实现从 Instagram 获取用户照片的分页。让我们现在开始。

## **1。配置**

要在我们的项目中配置分页，请使用

*整合 Instagram 请遵循这些* [*步骤*](https://developers.facebook.com/docs/instagram-basic-display-api) *。*

当您拥有 Instagram 所需的访问令牌时，请转到下一步。

## **2。设置网络通话**

*   设置翻新客户端

*   创建我们的媒体模型

## **3。创建用于获取数据的存储库**

我们将只展示照片，因此我们需要:

`*media_type*` - >过滤图片/相册

`*media_url*` - >从 URL 加载图片

`*children*` - >仅在转盘专辑的情况下使用。我们也将为子节点定义相同的字段

## 4.创建数据源

有 3 种类型的`Data source` 可用。您可以根据自己的需要使用以下任何一种`Data source`。

*   ***itemckeyeddatasource***->如果需要从上次提取的项目 id 中提取数据。
*   ***positional data source***->如果需要从特定位置/页面取数据。
*   ***PageKeyedDataSource***->如果加载的页面嵌入下一个/上一个键

我们将使用`PageKeyedDataSource`，因为我们从 Graph API 获得下一个/上一个指针作为响应。

最初，我们将把`next`光标作为空值传递给他们。然后适配器将加载数据，直到 `params*.*key`*或下一个*为`null`。

在 API 响应中获取`*InstagramMediaModel*` 时，我们向回调对象提供数据以及上一个和下一个键。如果出错，我们只需传递一个空列表和下一个指针作为`null`。

`*getFilteredList*`方法过滤响应列表，并提供一个包含个人帖子和相册帖子中所有照片的列表。

## 5.创建数据源工厂

应用程序或库的数据加载系统可以实现这个接口来创建`LiveData<PagedList>`

## 6.正在创建列表适配器

一个简单的 PagedListAdapter 将照片项目放大到我们的布局中。PagedListAdapter 在内部扩展 RecyclerView.Adapter。它采用两个参数:

*   *数据* - >列表的类型。这里， *InstagramMediaModel。数据？*是我们的物品类型、
*   *ViewHolder。*

我们还提供了一个 **DiffUtil** 类。它是一个实用程序类，用于计算提供给适配器的新旧列表之间的差异，并输出更新操作以将旧列表转换为新列表。它使用一种有效的机制与回收器视图的更新进行通信。

## 7.创建视图模型

这个*ViewModel* 将用于创建分页列表。该 PagedList 将要求*instagramphotosdata source*加载所有照片列表，并保存照片项目。为了获取数据，它使用后台执行器。

为了建立页面列表，我们使用:

*   *data source factory*->取数据表单，
*   PagedList 配置-> paged list 的配置，例如初始页面大小、首次初始化列表时加载项目的大小、启用/禁用占位符等。

## 8.在活动中显示列表

在我们的活动中初始化适配器。如果我们收到一个空列表，那么我们可以检查是否有错误。如果适配器之前没有保存任何列表，那么我们可以在视图上处理我们的错误情况(现在，我显示一个 snackbar)，否则我们将列表提交给适配器，它将新列表附加到当前列表。

仅此而已！现在，您可以在您的应用程序中尝试这样做。

随时欢迎建议！编码快乐！

![](img/c36a1034e9e4fb2f28813f9dbed78796.png)

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在媒体上关注我们。

如果你需要一个科特林工作室，看看我们如何能帮助你: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)