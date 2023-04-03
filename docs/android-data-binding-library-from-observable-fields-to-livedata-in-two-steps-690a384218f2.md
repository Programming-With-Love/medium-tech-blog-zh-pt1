# Android 数据绑定库——两步从可观察字段到 LiveData

> 原文：<https://medium.com/androiddevelopers/android-data-binding-library-from-observable-fields-to-livedata-in-two-steps-690a384218f2?source=collection_archive---------1----------------------->

![](img/e1b4e2069f5d4909e74f2789a36b54c3.png)

Illustration by [Virginia Poltrack](https://twitter.com/vpoltrack)

数据绑定最重要的特性之一就是 [**可观察性**](https://developer.android.com/topic/libraries/data-binding/observability) 。它允许您绑定数据和 UI 元素，这样当数据改变时，相关的元素就会在屏幕上更新。

默认情况下，普通原语和字符串是**不*可观察的*，因此，如果您在数据绑定布局中使用它们，它们的值将在创建绑定时使用，但对它们的后续更改将被忽略。**

为了使对象可观察，我们在[数据绑定库](https://developer.android.com/topic/libraries/data-binding/)中包含了一系列可观察的类:`ObservableBoolean`、`ObservableInt`、`ObservableDouble` …以及泛型`ObservableField<T>`。从现在起，我们将这些*可观测场称为*。

若干年后，作为第一波[架构组件](https://developer.android.com/topic/libraries/architecture)的一部分，我们发布了 [**LiveData**](https://developer.android.com/topic/libraries/architecture/livedata) ，这是*另一个*可观察的。它显然是与数据绑定兼容的候选者，所以我们添加了这个功能。

LiveData 是生命周期感知的，但这对于可观察字段来说并不是一个巨大的优势，因为数据绑定已经检查了视图何时是活动的。但是， **LiveData 支持** [**变换**](https://developer.android.com/reference/android/arch/lifecycle/Transformations) **，很多架构组件，像** [**房间**](https://developer.android.com/topic/libraries/architecture/room) **和** [**工作管理器**](https://developer.android.com/reference/androidx/work/WorkManager) **，支持 LiveData** 。

**由于这些原因，建议迁移到 LiveData。你只需要两个简单的步骤就可以做到。**

# 步骤 1:用 LiveData 替换可观察字段

如果您在数据绑定布局中直接使用可观察字段，只需用`LiveData<*Something>*`替换`Observable*Something*`(或`ObservableField<*Something>*`)。

之前:

> **记住** `***%lt;***` **不是错别字。您必须对 XML 布局中的** `***<***` **字符进行转义。**

之后:

或者，如果你从一个[视图模型](https://developer.android.com/topic/libraries/architecture/viewmodel)(首选方法)或者一个演示者或者控制器中展示可观察的事物，你不需要改变你的布局。只需在 ViewModel 中将那些`ObservableField`替换为`LiveData`。

之前:

之后:

# 步骤 2 —设置 LiveData 的生命周期所有者

绑定类有一个名为`setLifecycleOwner`的方法，当从数据绑定布局中观察 LiveData 时，必须调用这个方法。

之前:

之后:

> **注意:如果你正在为一个片段设置内容，建议使用** `**fragment.viewLifecycleOwner**` **(而不是片段的生命周期)来处理潜在的分离片段。**

现在，您可以通过[转换](https://developer.android.com/reference/android/arch/lifecycle/Transformations)和 [MediatorLiveData](https://developer.android.com/reference/android/arch/lifecycle/MediatorLiveData) 来使用您的 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 对象。如果你不熟悉这些功能，请查看来自 Android Dev Summit 2018 的这个[记录的“用 LiveData 找乐子”。](https://www.youtube.com/watch?v=2rO4r-JOQtA)