# 使用 KTX 库

> 原文：<https://medium.com/androiddevelopers/using-ktx-libraries-da1dc81c6ecf?source=collection_archive---------2----------------------->

![](img/f54d5257763f1249e0482b52c4b4c9b2.png)

当在 Kotlin 中使用 Android Java APIs 时，您很快就会意识到您错过了一些 Kotlin 特性，这些特性使编码变得如此简单和愉快。与其为这些 API 编写自己的包装器和扩展函数，不如看看 Jetpack KTX 库。目前，超过 20 个库拥有 KTX 版本，创建了 Java APIs 的甜蜜惯用版本，从 Android 平台 API 到 ViewModels、SQLite 甚至 Play Core。在这篇文章中，我们将看看一些可用的 API，看看它们是如何制作的。

如果你更喜欢看视频而不是看博客，请点击这里:

# 可发现性

作为一个最佳实践，为了减轻 ktx 功能的可发现性，总是在可用时导入`-ktx`工件。由于`-ktx`工件暂时依赖于非 ktx 版本，所以不需要包含其他工件。例如，对于`viewmodel`，你得到两个神器:`viewmodel`和`viewmodel-ktx`。`-ktx`工件将包含 Kotlin 扩展:

```
// Java language implementation
implementation "androidx.lifecycle:lifecycle-viewmodel:$lifecycle_version"// Kotlin implementation
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycle_version"
```

## 总是导入-ktx 工件

对于 Android 平台 API 上的扩展，导入`core-ktx`工件。

```
implementation "androidx.core:core-ktx:$corektx_version"
```

ktx 的大部分功能都是作为[扩展函数](/androiddevelopers/extend-your-code-readability-with-kotlin-extensions-542bf702aa36)实现的，所以你可以通过使用 Android Studio 中的自动完成功能轻松找到它们。

其他功能，比如像`[Color](https://developer.android.com/kotlin/ktx/extensions-list#for_androidgraphicscolor)`类上可用的析构和操作符重载，可以通过查看 [KTX 扩展](https://developer.android.com/kotlin/ktx/extensions-list#androidxactivity)的列表来发现。

# 平台 API—core-ktx

`core-ktx`为来自 Android 平台的 API 提供惯用的 Kotlin 功能。

例如，如果您正在使用`SharedPreferences`，当您想要更新一个值时，不需要执行 3 个不同的调用，您只需执行一个即可:

在幕后，[ktx 编辑方法](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:core/core-ktx/src/main/java/androidx/core/content/SharedPreferences.kt;l=39)调用相同的功能，提供了一个很好的提交默认选项:`apply()`。`apply()`与`commit()`不同，异步提交磁盘上的更改:

在`core-ktx`中，您还会发现一种处理常用平台监听器的更简单的方法。例如，如果您想在`EditText`中的文本被更改时触发一个动作，您必须实现`TextWatcher`的所有方法，即使您只对`onTextChanged()`感兴趣。`core-ktx`创建相应的`TextWatcher`方法:`[doOnTextChanged](https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary#doontextchanged)`、`[doAfterTextChanged](https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary#doaftertextchanged)`和`[doBeforeTextChanged](https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary#dobeforetextchanged)`，但是在你的 Kotlin 代码中，你只使用你需要的那个:

这带来了几个好处:您的代码变得更容易阅读，因为它更简洁，并且您可以获得更好的命名和可空性注释。

您会发现`[AnimatorListener](https://developer.android.com/reference/kotlin/androidx/core/animation/package-summary#(android.animation.Animator).addListener(kotlin.Function1,%20kotlin.Function1,%20kotlin.Function1,%20kotlin.Function1))`和`[TransitionListener](https://developer.android.com/reference/kotlin/androidx/core/transition/package-summary#(android.transition.Transition).addListener(kotlin.Function1,%20kotlin.Function1,%20kotlin.Function1,%20kotlin.Function1,%20kotlin.Function1))`有相似的监听器 API。

在幕后，`[doOnTextChanged](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:core/core-ktx/src/main/java/androidx/core/widget/TextView.kt;l=42?q=doOnTextChanged)`是作为`TextView`上的扩展函数实现的——这个类也有`addTextChangedListener`方法。`doOnTextChanged`为`TextWatcher`的其他函数创建[空实现](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:core/core-ktx/src/main/java/androidx/core/widget/TextView.kt;l=65)。

# Jetpack APIs

大多数可用的扩展是针对 Jetpack APIs 的。在这里，我将回顾一下我发现自己最常用的一些。

# LiveData

许多 LiveData 功能也是作为扩展函数实现的:像`[map](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#map)`、`[switchMap](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#switchmap)`或`[distinctUntilChanged](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#distinctuntilchanged)` ( [源](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:lifecycle/lifecycle-livedata-ktx/src/main/java/androidx/lifecycle/Transformations.kt;l=35))这样的方法。

例如，使用`livedata-ktx`中的`[map](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#map)`可以消除调用`Transformations.map(livedata) { /* map function */ }`的需要，并允许我们以更符合 Kotlin 习惯的方式直接调用`liveData.map`。

当你观察一个`LiveData`对象时，你必须实现一个`[Observer](https://developer.android.com/reference/kotlin/androidx/lifecycle/Observer)`。但是使用来自`lifecycle-ktx`的 observe，代码变得更简单。如果没有找到方法，确保调用 import `androidx.lifecycle.observe`。

`LiveData`非常适合暴露数据供用户界面使用，因此，为了从`Flow`转换到`LiveData`以及从`LiveData`转换到`Flow`,`lifecycle-livedata-ktx`工件提供了两个方便的扩展函数:`[Flow.asLiveData()](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#aslivedata)`和`[LiveData.asFlow()](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#asflow)`。

# 活动/片段和视图模型

为了构造一个视图模型，如果你的`ViewModel`有依赖关系，你可以扩展`[ViewModel](https://developer.android.com/reference/androidx/lifecycle/ViewModel)`类并实现`[ViewModelProvider.Factory](https://developer.android.com/reference/androidx/lifecycle/ViewModelProvider.Factory)`。要实例化它，使用`[viewModels](https://developer.android.com/reference/kotlin/androidx/activity/package-summary#(androidx.activity.ComponentActivity).viewModels(kotlin.Function0))`委托(点击阅读更多关于委托[):`by viewModels(factory)`:](/androiddevelopers/delegating-delegates-to-kotlin-ee0a0b21c52b)

`viewModels`在`activity`和`fragment`的`-ktx`神器中有。

使用协程时，您会发现自己需要在 ViewModel 中启动一个协程。当 ViewModel 被销毁时，协程所做的工作应该被取消。不要实现你自己的`CoroutineScope`，使用`[viewModelScope](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary#viewmodelscope)`。取消将在`[ViewModel.onCleared()](https://developer.android.com/reference/kotlin/androidx/lifecycle/ViewModel#oncleared)`为您自动完成。从[这篇博文](/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471)中找出`viewModelScope`的来龙去脉。

# 房间和工作经理

Room 和 WorkManager 都通过它们的`-ktx`构件提供协程支持。因为我们认为更深入地讨论这些是值得的，所以请继续关注专注于这些特定库的 MAD 技能文章！

# 其他 KTX 模块

AndroidX 神器不是唯一提供 KTX 版本的:

*   Firebase 创建了[通用 Kotlin 扩展](https://firebaseopensource.com/projects/firebase/firebase-android-sdk/docs/ktx/common.md)
*   谷歌地图提供[地图](https://developers.google.com/maps/documentation/android-sdk/ktx)和[地点](https://developers.google.com/places/android-sdk/ktx) ktx 图书馆
*   Play Core 有一个 core-ktx 工件，为监控应用内更新提供协程支持

简洁、易读和 Kotlin 惯用——一旦你开始使用`-ktx`扩展，这些特性将使你的代码受益。敬请关注更多在应用中利用 Kotlin 和 Jetpack 的方式！