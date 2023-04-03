# 用 Kotlin 扩展链接像 RxJava 这样的 LiveData

> 原文：<https://medium.com/google-developer-experts/chaining-livedata-like-rxjava-with-kotlin-extension-e3a2c15ac11?source=collection_archive---------5----------------------->

如果你没有看过我关于[非空 LiveData](/@henrytao/nonnull-livedata-with-kotlin-extension-26963ffd0333) 的文章，你需要看一下。同样的想法将在这里重复使用。

如果你不了解 RxJava，你还需要从 https://github.com/ReactiveX/RxJava[那里了解一下。](https://github.com/ReactiveX/RxJava)

在本文中，我们将讨论如何借鉴 RxJava 的一些思想，并将其应用到 LiveData 中。当然，感谢 Kotlin 扩展。它有助于开发人员更轻松地工作。对不起 Java！目标将是这样的:

```
val liveData: MutableLiveData<Boolean> = MutableLiveData()
liveData
  .distinct()
  .filter { it == false }
  .map { true }
  .nonNull()
  .observe(lifecycleOwner, { result ->
    // result is non-null and always true
  })
```

# 最简单的方法

我们以`distinct`为例。

```
val liveData: MutableLiveData<Boolean> = MutableLiveData()
liveData
  .distinct()
  .observe(lifecycleOwner, { result ->
    // new result is different from previous result
  })
```

为了做到这一点，您需要为 LiveData 创建`distinct`和`observe`扩展。

```
**fun** <T> LiveData<T>.distinct(): LiveData<T> {
    **val** mediatorLiveData: MediatorLiveData<T> = MediatorLiveData()
    mediatorLiveData.addSource(**this**) **{
        if** (**it** != mediatorLiveData.*value*) {
            mediatorLiveData.*value* = **it** }
    **}
    return** mediatorLiveData
}**fun** <T> LiveData<T>.observe(owner: LifecycleOwner, observer: (t: T?) -> Unit) {
    observe(owner, *Observer* **{** observer(**it**) **}**)
}
```

其他方法也可以遵循同样的模式，比如`filter`、`map` …

# 支持非空

支持非空有点棘手。正如我在[非空 LiveData](/@henrytao/nonnull-livedata-with-kotlin-extension-26963ffd0333) 文章中提到的，您需要有`NonNullMediatorLiveData`来区分空和非空观察者。因此，您可能需要为每个扩展方法复制您的逻辑。您的过滤器扩展将如下所示:

```
**fun** <T> LiveData<T>.distinct(): LiveData<T> {
    **val** mediatorLiveData: MediatorLiveData<T> = MediatorLiveData()
    mediatorLiveData.addSource(**this**) **{
        if** (**it** != mediatorLiveData.*value*) {
            mediatorLiveData.*value* = **it** }
    **}
    return** mediatorLiveData
}// Exactly same logic but return NonNullMediatorLiveData
**fun** <T> NonNullMediatorLiveData<T>.distinct(): LiveData<T> {
    **val** mediatorLiveData: NonNullMediatorLiveData<T> = NonNullMediatorLiveData()
    mediatorLiveData.addSource(**this**) **{
        if** (**it** != mediatorLiveData.*value*) {
            mediatorLiveData.*value* = **it** }
    **}
    return** mediatorLiveData
}**fun** <T> LiveData<T>.observe(owner: LifecycleOwner, observer: (t: T?) -> Unit) {
    observe(owner, *Observer* **{** observer(**it**) **}**)
}
```

现在你可以不顾顺序一起做`distinct`和`nonNull`。

```
val liveData: MutableLiveData<Boolean> = MutableLiveData()
liveData
  .nonNull()
  .distinct()
  .observe(lifecycleOwner, { result ->
    // new result is different from previous result
  })// or 
liveData
  .distinct()
  .nonNull()
  .observe(lifecycleOwner, { result ->
    // new result is different from previous result
  })
```

# 用 MediatorObserver 润色代码

复制逻辑是不好的。我们可以通过定义`interface MediatorObserver<IN, OUT>`来改进。您的代码看起来会更整洁，如下所示:

```
**private class** DistinctExt<T> : MediatorObserver<T, T> {

    **override fun** run(source: LiveData<T>, mediator: MediatorLiveData<T>, value: T?) {
        **if** (value != mediator.*value*) {
            mediator.*value* = value
        }
    }
}

**fun** <T> LiveData<T>.distinct(): LiveData<T> = *createMediator*(**this**, DistinctExt())
**fun** <T> NonNullMediatorLiveData<T>.distinct(): NonNullMediatorLiveData<T> = *createMediator*(**this**, DistinctExt())
```

更多信息，请看 [LiveData.kt](https://github.com/henrytao-me/livedata-ktx/blob/master/livedata-ktx/src/main/java/me/henrytao/livedataktx/LiveData.kt) 。

# 准备使用库

你可以试试 https://github.com/henrytao-me/livedata-ktx。在 build.gradle 中添加一行代码，准备开始工作:

```
implementation "me.henrytao:livedata-ktx:LATEST_VERSION"
```

# 感觉缺少方法？

请通过创建[问题](https://github.com/henrytao-me/livedata-ktx/issues)来提出您的需求。我会尽快支持它。

让我知道你的想法和快乐编码！