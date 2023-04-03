# 在特定项目更新时通知 RecyclerView

> 原文：<https://medium.com/google-developer-experts/notifying-recyclerview-on-a-specific-change-b36e6dc59e0f?source=collection_archive---------0----------------------->

## 偶尔当使用 [*RecyclerView*](https://developer.android.com/reference/android/support/v7/widget/RecyclerView) 时，我们希望能够通知特定的更新。我们想知道条目在列表中的位置，以及它的更新(插入、删除、更改)。也许关于这次更新我们想传递更多的信息。例如，更改的原因、原始屏幕或发生日期。

知道了具体的变化，我们就可以调用`notifyItemAdded()`，而不是`notifyItemSetChanged()`，后者会遍历整个列表。

老实说，使用 [*分页库*或者仅仅是 *DiffUtil*](https://proandroiddev.com/clean-easy-new-how-to-architect-your-app-part-5-list-update-afac69da0b5e) ，通常都可以实现这个功能，而且性能也非常好。然而，在某些情况下，它仍然是我们想要保存的多余工作。

![](img/0dbf80467451796c40f203da61b1a7d6.png)

## 如何通知列表中的更改？

[*LiveData*](https://developer.android.com/topic/libraries/architecture/livedata) 是观察和通知 UI 变化的一个很棒的工具。它包装一个对象，每当这个对象改变时， *LiveData* 通知它所有的观察者新的值。但是，如果我们在 LiveData 中包装一个项目列表，并且列表中的一个项目发生了变化，那么列表对象本身保持不变。也就是说，如果我们还想让观察者得到条目变化的通知——我们需要这么小的技巧来解决这个问题。

此外，如前所述，我们希望保留一些关于变更的信息，并将其传递给观察者。我们应该怎样做呢？

为了解决这个问题，这里有一个我用过几次的食谱:

这个具体的例子是建立在我所做的一个例子之上的。它允许用户选择在通话过程中要执行的操作(文本到语音、录音等..)并使用 Nexmo Voice API 来执行它们。我在另一篇文章中写了更多关于它的内容[。](https://proandroiddev.com/moshi-polymorphic-adapter-is-d25deebbd7c5)

很明显，这只是一个演示项目，但这个想法在不止一个复杂的应用程序中对我很有用。

## 设置表示层

[在之前的帖子系列](https://proandroiddev.com/clean-easy-new-how-to-architect-your-app-part-1-e439668a523d)中，我分享了构建应用程序时我喜欢应用的架构，包括*表示层*、*域层*和*数据层*。

我的*展现*层持有一个 *UIModel* ，它扩展了 [*ViewModel*](https://developer.android.com/topic/libraries/architecture/viewmodel) 。它的作用是以最简单的方式向*表示*提供它需要的数据，这样就有尽可能少的计算和逻辑。

在这种情况下， *UiModel* 保持:

*   要拨打的电话号码
*   打电话的电话号码
*   要执行的操作列表

每当动作列表中有变化时，我们希望通知观察者，在本例中，通知 UI 和 *RecyclerView 适配器*。

这是我的用户界面模型:

```
class CallRequestUiModel : ViewModel() {
    lateinit var toPhone: String
    lateinit var fromPhone: String
 **val actions = ListLiveData<NccoAction>()**
}
```

## 什么是 **ListLiveData** ？

如前所述，我们需要向 *LiveData* 添加更多的信息和逻辑。所以，我延长了。

首先，它包装了一个类型为 ***ListHolder*** 的值，以保存我们需要的所有信息，而不仅仅是条目列表:

```
class ListLiveData<T> : LiveData<**ListHolder**<T>>() {}
```

在这种情况下， **ListHolder** 保存项目列表、已更改项目的索引以及更新的类型:

```
data class **ListHolder**<T>(val list: MutableList<T> = *mutableListOf*()) {
    var indexChanged: Int = -1
    var updateType: **UpdateType**? = null
}
```

***UpdateType*** 是我创建的一个枚举，它匹配 RecyclerView 可以做的更改，因此我们能够通知特定的更改:

```
private enum class UpdateType {
    INSERT, REMOVE, CHANGE
}
```

由于 **UpdateType** 的工作将是确定我们要在适配器上调用哪个方法，我使用了很酷的 Kotlin 特性，它允许你[使用枚举](https://kotlinlang.org/docs/reference/enum-classes.html#implementing-interfaces-in-enum-classes)实现接口。

**注**:这是我喜欢 Kotlin 的一个特性。**但是**！确保不要滥用它。对我来说，重要的是不要混淆架构层。因为在这种情况下，ListHolder(它拥有 UpdateType)和它所做的动作(通知适配器)都属于*表示层*我很乐意这样实现它。例如，如果一个属于 UI 的枚举将实现使用数据层的方法，我就不太推荐它。

```
private enum class **UpdateType** { abstract fun **notifyChange**(
        adapter: RecyclerView.Adapter<RecyclerView.ViewHolder>,
        indexChanged: Int
    ) **INSERT** {
        override fun **notifyChange**(
            adapter: RecyclerView.Adapter<RecyclerView.ViewHolder>,
            indexChanged: Int) = adapter.notifyItemInserted(indexChanged)
    },

  **REMOVE** {
        override fun **notifyChange**(
            adapter: RecyclerView.Adapter<RecyclerView.ViewHolder>,
            indexChanged: Int) = adapter.notifyItemRemoved(indexChanged)
    }, **CHANGE** {
        override fun **notifyChange**(
            adapter: RecyclerView.Adapter<RecyclerView.ViewHolder>,
            indexChanged: Int) = adapter.notifyItemChanged(indexChanged)
    };

}
```

这个枚举是私有的，有助于我上面提到的层的分离。这样，只有**列表持有人**(即已更改的项目)能够应用它:

```
data class **ListHolder**<T>(val list: MutableList<T> = *mutableListOf*()) {
    var indexChanged: Int = -1
    private var updateType: UpdateType? = null

//...

    fun **applyChange**(adapter: RecyclerView.Adapter<RecyclerView.ViewHolder>) {
 **updateType?.notifyChange(adapter, indexChanged)**    }
```

## 通知观察者变化

在我的 MainActivity.onCreate()中(这是我的*表示*层组件的起点)，我要求观察动作列表中的变化:

```
uiModel.actions.observe(this, this)
```

举个简单的例子，我的 *MainActivity* 也实现了 *Observer* ，因此覆盖了 *onChanged()* 。每当 *actions* 中的一个项目更新时，我们只需要请求*LiveData*(*list holder*)的新值来应用适配器上的更改。值 *ListHolder，*已经知道被更新的确切项目，以及在适配器上应用什么:

```
override fun **onChanged**(value: ListHolder<NccoAction>?) {
    list.*adapter*?.*let* **{** value?.applyChange(**it**)
    **}** }
```

## 更新一个项目看起来像什么？

UI 可以调用 *addItem()、removeItemAt()或 setItem()，*实际上是传递给 *ListLiveData* 处理的:

例如，用户界面调用:

```
fun **addAction**(action: NccoAction) {
    uiModel.actions.addItem(action)
}
```

***ListHolder*** 将执行更新的请求传递给 ***ListHolder*** ，然后通知观察者(此处为 UI)更新。

```
class ListLiveData<T> : LiveData<ListHolder<T>>() {

    //...

    fun addItem(item: T, position: Int = *value*?.size() ?: 0) {
        *value*?.addItem(position, item)
        *value* = *value* }

    fun removeItemAt(position: Int) {
        *value*?.removeItemAt(position)
        *value* = *value* }

    fun setItem(position: Int, item: T) {
        *value*?.setItem(position, item)
        *value* = *value* }

}
```

## 为什么价值=价值？

可能有一种更优雅的方式来做到这一点🤷‍♀ ️如前所述，因为我不更新值本身(ListHolder 对象)，而只更新它的内容，所以更新不会作为值的变化发送给观察者。 *LiveData* 只有在 *setValue()* 被调用时才会将变更发送给观察者。所以我通过使用具有相同值的 setValue()来欺骗它😬

```
From source code of LiveData.java@MainThread
protected void setValue(T value) {
    *assertMainThread*("setValue");
    mVersion++;
    mData = value;
    dispatchingValue(null);
}
```

关于 *ListLiveData 的另一个说明:*

为了方便起见，为了使它表现得更 list 一 List，我实现了 2 个方法:

```
class ListLiveData<T> : LiveData<ListHolder<T>>() {

    val **size**: Int get() {
            return *value*?.size() ?: -1
    } **operator** fun **get(position: Int)**: T? {
        return *value*?.list?.get(position)
    }

}
```

例如，`**size():**`用于 *Adapter.getItemCount()*

```
override fun getItemCount(): Int {
    return actions.size
}
```

`**operator fun get()**`是从列表中获取一个特定的项目，例如在*adapter . onbindviewholder():*上

```
override fun onBindViewHolder(holder: NccoCompVH, index: Int) {
    actions[index]?.*let* **{** holder.bind(**it**) **}** }
```

# 让我们看看整个流程

![](img/85f7dd50bf33dfc0664c5a246773c6b5.png)

1.  *UI* 要求 *ListLiveData* 更新列表项→
2.  *列表直播数据*要求*列表持有者*保存更新信息→
3.  *ListLiveData* 设置其值，并将更改发送给*观察器*
4.  *观察者* (UI) *onChange()* 用新值调用，该值在类型*列表框*上
5.  *UI* 要求新值*list holder*应用其保存的数据的更新
6.  *UpdateType，*包含在 *ListHolder* 中，通知适配器更新
7.  适配器应用特定的项目更新(你也可以传递更多的信息，做任何你想做的事情！)
8.  大家都很开心！！

# 就是这样！

就这些，希望有帮助:)

这里是 Github 上这个例子的代码

编码快乐！感谢您的阅读。[此处(上*推特*其实)](https://twitter.com/BrittBarak)提问🙌🙏😍✌