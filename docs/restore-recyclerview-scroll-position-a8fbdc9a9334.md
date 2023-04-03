# 恢复 RecyclerView 视图滚动位置

> 原文：<https://medium.com/androiddevelopers/restore-recyclerview-scroll-position-a8fbdc9a9334?source=collection_archive---------0----------------------->

![](img/3ef0d041827392982de1593b540e5bf6.png)

Illustration by [Ella Dobson](https://www.instagram.com/elladobbie/)

你可能遇到过这样的问题:当你的`Activity` / `Fragment`被重新创建时，一个`RecyclerView`丢失了滚动位置。这通常是因为`Adapter`数据是异步加载的，在`RecyclerView`需要布局时数据还没有加载，所以无法恢复滚动位置。

从`[**1.2.0-alpha02**](https://developer.android.com/jetpack/androidx/releases/recyclerview)`开始，`RecyclerView`提供了一个新的 API，让`Adapter` **块布局恢复，直到它准备好**。请继续阅读，了解如何使用这个新的 API 以及它是如何工作的。

# 恢复滚动位置

有几种方法可以确保您可能已经采用的正确滚动位置。最好的方法是确保在第一次布局通过之前总是在`Adapter`上设置数据，方法是将您想要显示的数据缓存在内存、`ViewModel`或存储库中。如果这种方法不可行，其他解决方案要么更复杂，比如避免在`RecyclerView`上设置`Adapter`，这可能会带来标题等项目的问题，要么误用`[LayoutManager.onRestoreInstanceState](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.LayoutManager?hl=en#onRestoreInstanceState(android.os.Parcelable)https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.LayoutManager?hl=en#onRestoreInstanceState(android.os.Parcelable))` API。

`recyclerview:1.2.0-alpha02`解决方案是一种新的`Adapter`方法，允许您设置状态恢复策略(通过`[StateRestorationPolicy](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter.StateRestorationPolicy)`枚举)。这有 3 个选项:

*   `[ALLOW](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter.StateRestorationPolicy#ALLOW)`—*默认*状态，在下一个布局走刀中立即恢复`RecyclerView`状态
*   `[PREVENT_WHEN_EMPTY](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter.StateRestorationPolicy#PREVENT_WHEN_EMPTY)` —仅当适配器不为空时恢复`RecyclerView`状态(`adapter.getItemCount() > 0`)。如果您的数据是异步加载的，`RecyclerView`会一直等待，直到数据加载完毕，然后状态才会恢复。如果您有默认项目，如标题或装载进度指示器作为您的`Adapter`的一部分，那么您应该使用`PREVENT`选项，除非默认项目是使用`[ConcatAdapter](https://developer.android.com/reference/androidx/recyclerview/widget/ConcatAdapter)`添加的(点击了解更多[)。`ConcatAdapter`等待其所有适配器准备就绪，然后才恢复状态。](/androiddevelopers/merge-adapters-sequentially-with-mergeadapter-294d2942127a)
*   `[PREVENT](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter.StateRestorationPolicy#PREVENT)` —所有状态恢复被推迟，直到您设置`ALLOW`或`PREVENT_WHEN_EMPTY`。

在适配器上设置状态恢复策略，如下所示:

```
adapter.stateRestorationPolicy = PREVENT_WHEN_EMPTY
```

就是这样！一个简短而甜蜜的帖子，让你了解`RecyclerView`的懒惰状态恢复特性。开始使用它🏁👩‍💻👨‍💻！