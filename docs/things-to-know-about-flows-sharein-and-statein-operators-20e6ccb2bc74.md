# 关于流的 shareIn 和 stateIn 操作符需要知道的事情

> 原文：<https://medium.com/androiddevelopers/things-to-know-about-flows-sharein-and-statein-operators-20e6ccb2bc74?source=collection_archive---------1----------------------->

![](img/f00272b8dcaa4837eeca156a2a6ec168.png)

`[Flow.shareIn](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/share-in.html)`和`[Flow.stateIn](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/state-in.html)`操作符将*冷*流转换为*热*流:它们可以将来自冷上游流的信息多播到多个收集器。它们通常用于提高性能，在收集器不存在时添加缓冲区，甚至用作缓存机制。

> **注意** : **冷流**是按需创建的，并在被观察时发出数据。**热流**始终*处于活动状态，无论是否被观察到都可以发出数据。*

在这篇博文中，您将通过示例熟悉`shareIn`和`stateIn`操作符。您将学习如何配置它们来执行某些用例，并避免您可能遇到的常见陷阱。

# 底层流生成器

继续我在[上一篇博文](/androiddevelopers/a-safer-way-to-collect-flows-from-android-uis-23080b1f8bda)中的例子，我们使用的底层流生成器发出位置更新。这是一个*冷*流，因为它是使用`[callbackFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/callback-flow.html)`实现的。每一个新的收集器都会触发 flow producer 块，一个新的回调会被添加到`FusedLocationProviderClient`。

让我们看看如何使用`shareIn`和`stateIn`操作符来优化不同用例的`locationsSource`流程。

# 分享还是陈述？

我们要讨论的第一个话题是`shareIn`和`stateIn`的区别。`shareIn`操作符返回一个`[SharedFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-shared-flow/)`实例，而`stateIn`返回一个`[StateFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html)`。

> **注意**:要了解更多关于`StateFlow`和`SharedFlow`的信息，请查看[我们的文档](https://developer.android.com/kotlin/flow/stateflow-and-sharedflow)。

`StateFlow`是针对共享状态优化的`SharedFlow`的专门配置:最后发出的项目被重放给新的收集器，项目被*使用`[Any.equals](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html)`合并*。您可以在`[StateFlow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html)` [文档](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html)中了解更多相关信息。

这些 API 之间的主要区别在于，`StateFlow`接口允许您通过读取它的`value`属性来同步访问最后发出的值。`SharedFlow`就不是这样了。

# 提高性能

这些 API 可以通过共享由所有收集器观察的流的相同实例来提高性能，而不是按需创建相同流的新实例。

在下面的例子中，`LocationRepository`使用由`LocationDataSource`公开的`locationsSource`流，并应用 shareIn 操作符使每个对用户位置感兴趣的人从流的同一个实例中收集数据。只有一个`locationsSource`流实例被创建并为所有收集器共享:

`[WhileSubscribed](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-sharing-started/-while-subscribed.html)`共享策略用于在没有采集器时取消上游流量。这样，我们避免了在没有人对位置更新感兴趣时浪费资源。

> **安卓应用提示！**在最后一个收集器消失后，您可以在大部分时间使用`**WhileSubscribed(5000)**`来保持上游流活动 5 秒钟以上。这避免了在诸如配置改变的某些情况下重新启动上游流。当创建上游流的成本很高，并且在视图模型中使用这些操作符时，这个技巧特别有用。

# 缓冲事件

对于这个例子，我们的要求已经改变，现在我们被要求*始终*监听位置更新，并在应用程序从后台出现时在屏幕上显示最后 10 个位置:

我们使用一个值为 10 的`replay`,将最后 10 个发出的条目保存在内存中，并在每次收集器观察到流时重新发出这些条目。为了保持底层流始终处于活动状态并发出位置更新，使用`SharingStarted.Eagerly`策略来监听更新，即使没有收集器。

# 缓存数据

我们的需求又变了，在这种情况下，如果应用程序在后台，我们就不需要总是*监听位置更新。但是，我们需要缓存最后发出的项，以便用户在获取当前位置时，总是可以在屏幕上看到一些数据，即使是陈旧的数据。对于这种情况，我们可以使用`stateIn`操作符。*

`Flow.stateIn`将最后发出的项目缓存并重放到新的收集器中。

# 小心！不要在每次函数调用时创建新的实例

**NEVER** 使用`shareIn`或`stateIn`创建一个新的流，该流在调用函数时返回。这将在每次函数调用时创建一个新的`SharedFlow`或`StateFlow`，它们将一直保留在内存中，直到作用域被取消，或者当没有对它的引用时被垃圾收集。

# 需要输入的流程

需要输入的流，如`userId`，不能使用`shareIn`或`stateIn`轻易共享。以 [iosched](https://github.com/google/iosched) 开源项目——Google I/O 的 Android 应用程序——从 [Firestore](https://firebase.google.com/docs/firestore/quickstart) 获取用户事件的流程是使用`callbackFlow`实现的，你可以在源代码中看到[。因为它将`userId`作为一个参数，所以使用`shareIn`或`stateIn`操作符不能轻易重用这个流。](https://github.com/google/iosched/blob/main/shared/src/main/java/com/google/samples/apps/iosched/shared/data/userevent/FirestoreUserEventDataSource.kt#L107)

优化此用例取决于您的应用程序的要求:

*   你允许同时接收来自多个用户的事件吗？您可能需要创建一个`SharedFlow` / `StateFlow`实例的映射，并在`subscriptionCount`达到零时删除引用和取消上游流。
*   如果您只允许一个用户，并且所有的收集器都需要更新到新用户，那么您可以向所有收集器的公共`SharedFlow` / `StateFlow`发出事件更新，并使用公共流作为类中的变量。

`shareIn`和`stateIn`操作符可以与冷流一起使用，以提高性能，在收集器不存在时添加缓冲区，甚至作为缓存机制！明智地使用它们，不要在每次函数调用时都创建新的实例——它不会像您预期的那样工作！