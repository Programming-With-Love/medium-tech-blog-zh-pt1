# 支持库 27.1.0 中的加载程序

> 原文：<https://medium.com/androiddevelopers/loaders-in-support-library-27-1-0-b1a1f0fee638?source=collection_archive---------2----------------------->

对于[支持库 27.1.0](https://developer.android.com/topic/libraries/support-library/revisions.html#27-1-0) ，我重写了`[LoaderManager](https://developer.android.com/reference/android/support/v4/app/LoaderManager.html)`的内部，驱动[加载器 API](https://developer.android.com/guide/components/loaders.html) 的类，我想解释这些变化背后的原因以及未来会发生什么。

## 装载机和碎片，历史

从一开始，装载器和碎片就紧密地联系在一起。这意味着`[FragmentActivity](https://developer.android.com/reference/android/support/v4/app/FragmentActivity.html)`和`[Fragment](https://developer.android.com/reference/android/support/v4/app/Fragment.html)`中的许多代码都是为了支持加载器，尽管事实上它们确实是相当独立的。这也意味着，与活动、片段和架构组件[生命周期](https://developer.android.com/topic/libraries/architecture/lifecycle.html)相比，加载器的生命周期和保证是完全独特的，并且受制于它们自己的一套有趣和令人兴奋的行为差异和缺陷。

## 27.1.0 中有什么变化

有了 27.1.0，加载器的技术负担已经大大减少:实现`LoaderManager`所需的代码行减少到原来的三分之一，并且有更多的测试要回填，以确保加载器在前进中保持良好的状态。

这在很大程度上要归功于架构组件。更具体地说， [ViewModels](https://developer.android.com/topic/libraries/architecture/viewmodel.html) (用于跨配置更改保留状态)和 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata.html) (用于提供生命周期感知回调)。装载机现在受益于这些同样更高水平、经过良好测试的组件作为基础，减少了持续的位损坏，并允许在可靠性/保证方面的改进自动传播到装载机。

## 行为改变

这确实意味着一些行为上的改变。

首先，现在必须在主线程上调用`[initLoader](https://developer.android.com/reference/android/support/v4/app/LoaderManager.html#initLoader(int, android.os.Bundle, android.support.v4.app.LoaderManager.LoaderCallbacks<D>))`、`[restartLoader](https://developer.android.com/reference/android/support/v4/app/LoaderManager.html#restartLoader(int, android.os.Bundle, android.support.v4.app.LoaderManager.LoaderCallbacks<D>))`和`[destroyLoader](https://developer.android.com/reference/android/support/v4/app/LoaderManager.html#destroyLoader(int))`。这为回调何时停止或开始提供了一些非常具体的保证——例如，在销毁一个加载程序后，您将永远不会得到对`[onLoadFinished](https://developer.android.com/reference/android/support/v4/app/LoaderManager.LoaderCallbacks.html#onLoadFinished(android.support.v4.content.Loader<D>, D))`的回调。

> 注意:虽然在这个版本之前，技术上你可以在其他线程上进行加载操作，但是`LoaderManager`从来都不是线程安全的，导致经常出现未定义的行为。

最重要的是，`[onLoadFinished](https://developer.android.com/reference/android/support/v4/app/LoaderManager.LoaderCallbacks.html#onLoadFinished(android.support.v4.content.Loader<D>, D))`现在遵循与 LiveData 观察者相同的规则来决定何时被调用——总是在`onStart`和`onStop`之间，从不在`onSaveInstanceState`之后。这允许您在`onLoadFinished`中安全地执行[片段事务](https://developer.android.com/guide/components/fragments.html#Transactions)。

## 我应该使用什么，装载机要去哪里？

正如我在以前的博客文章中提到的， [*使用架构组件加载生命周期感知数据*](/google-developers/lifecycle-aware-data-loading-with-android-architecture-components-f95484159de4) ，我觉得 ViewModels+LiveData 绝对是一个更灵活、更易于理解的系统，我强烈推荐给开发人员。但是，如果您已经有了围绕加载器构建的 API，这些变化应该会极大地提高组件的可靠性和稳定性。

这里的许多变化为使加载器成为一个更加可选的依赖项打下了基础，它不需要深入到[生命周期所有者](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) / [视图模型存储所有者](https://developer.android.com/reference/android/arch/lifecycle/ViewModelStoreOwner.html)中就能运行。

## 试试吧！

如果您正在使用加载器，请尽早仔细查看，并注意在[发行说明](https://developer.android.com/topic/libraries/support-library/revisions.html#27-1-0)中列出的行为变化。

> 注意:显然，这些更改只适用于支持库加载器。如果您正在使用 Android 框架加载器，请尽快切换到支持库加载器。没有为框架加载器 API 计划错误修复或改进。