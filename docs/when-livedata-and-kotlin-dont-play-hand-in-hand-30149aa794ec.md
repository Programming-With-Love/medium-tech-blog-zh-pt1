# 当 LiveData 和 Kotlin 不能很好地合作时

> 原文：<https://medium.com/google-developer-experts/when-livedata-and-kotlin-dont-play-hand-in-hand-30149aa794ec?source=collection_archive---------0----------------------->

![](img/0e1051f32f9ee7e55dca59837cb102a7.png)

Playing well together

这个想法很有趣。基于反应流的想法，在 RxJava plus 增加自动生命周期处理的时候达到了顶峰——这是 Android 上的一个问题。尽管时机不佳。它是在 Kotlin 在 Android 社区产生影响之前到来的，有时两者不能很好地合作。让我们来探讨一下为什么会这样，会发生什么！

# 好的和坏的

`LiveData`的想法非常简单:可观察模式的生命周期感知实现。此外，如果您重新订阅，您将再次获得上次发出的值。这可以比作带有粘性消息的`EventBus`的类型化版本。

这是`LiveData`的核心特征之一。但是很快，包括作者在内的开发人员发现，你并不总是想要那种行为。假设你有一个误差值。然后，您可能不想在重新订阅后再次显示该错误值，例如在设备旋转后。

解决这个问题的一个方法是`[SingleLiveEvent](https://github.com/android/architecture-samples/blob/dev-todo-mvvm-live/todoapp/app/src/main/java/com/example/android/architecture/blueprints/todoapp/SingleLiveEvent.java)`，对于像错误这样的一次性事件，这是一个很好的选择。

# 两个世界

但是如果你两者都想要一点呢？您希望最后一个值“粘性”加上不显示潜在的错误多次！
在这种情况下，在它们被消耗后，从`LiveData`中删除我们的错误值会很好，对吗？

我们来看看`LiveData`的实现:

当前值保存在字段中:

```
private volatile Object mData;
```

最初，它被设置为`NOT_SET`。

```
static final Object *NOT_SET* = new Object();
```

不幸的是，不是`public`否则，我们可以用它来重置值。

# 现在怎么办？

如果您搜索这个问题，最建议的解决方案之一是简单地将其设置为`null`。

让我们来探索一下:

```
viewModel.results.observe(lifecycleOwner) **{** result **->** when (result) {
        SomeResult.Error -> {
            handleError()
            **viewModel.results.*reset*()**
        }
        SomeResult.Result -> { handleResult(result) }
    }
**}**
```

其中 ViewModel 的实现是:

```
class MyViewModel: ViewModel() {

    private val mutableResults = **MutableLiveData<SomeResult>()**

    **val results: LiveData<SomeResult>**
        get() = mutableResults

 **fun reset() {
        mutableResults.*value* = null
    }**
}
```

这个管用，对吧？

恭喜你！您刚刚在代码中引入了崩溃！

# 但是为什么呢？

你甚至可能没有注意到，并可能推出它。(如果是，请下次添加单元测试)。

这里的问题是我们声明了`LiveData`对于编译器是不可空的！

您的观察者将得到这个`null`值的通知，然后一旦它得到一个带有`KotlinNullPointerException`的值，就会立即崩溃。你已经无能为力了！这是科特林工作方式的一部分！

`LiveData`是用 Java 写的。它允许将其值设置为`null`。所以作者可以添加`@Nullable`注释，就像我们在其他 Jetpack 库上一样，对吗？不，因为那样你总是需要在`getValue`或`Observer`中处理可空类型，尽管你可能已经明确地将`LiveData`声明为非空。泛型是运行时信息(对于 Java)，注释是编译时信息，它们不能为我们解决这个问题。而 Java 在类型系统中根本没有 null。

这不是一个 bug——这是一个特性！

# 你如何解决这个问题？

一个简单的方法是将您的`LiveData`标记为可空:

```
private val mutableResults = MutableLiveData<**SomeResult?**>()

    val results: LiveData<**SomeResult?**>
        get() = mutableResults
```

现在我们可以在观察器中处理它，只对非空值做出反应。更重要的是:编译器强迫我们这么做！

# 还有别的办法吗？

我之前说过我们不能访问原始的`NOT_SET`值。不完全是真的。我们可以通过在 jetpack 包中设置一些东西来暴露它:

```
**package androidx.lifecycle**

fun <T> LiveData<T>.reset(){
 **this.*value* = LiveData.*NOT_SET***as T?
}
```

虽然有点脏。

但除此之外，这实际上是允许的吗？
至少不禁止！即使根据新的更严格的关于 Google Play 应用程序私有 API 的规则，这应该没问题。

实际问题是另一个问题，但最重要的是，您依赖于`LiveData`的实现细节，它不是公共 API 的一部分，因此可能随时改变！

# 好了，现在怎么办？

另一种方法是将结果打包，这样您就可以拥有自己的`NOT_SET`:

```
**sealed class** ViewModelResult {
    data class **Result**(val result: SomeResult): ViewModelResult()
    object **NotSet**: ViewModelResult()
}
```

我们将使用`NotSet`清除以前的值，然后可以忽略`Observer`中的值。

这不是最好的解决方法，但可能是最干净的。
这也是 [Jose Alcérreca](https://medium.com/u/e0a4c9469bb5?source=post_page-----30149aa794ec--------------------------------) 在这篇[文章](/androiddevelopers/livedata-with-snackbar-navigation-and-other-events-the-singleliveevent-case-ac2622673150)中推荐的。

# 还有别的吗？

此外，试着重新考虑你是否首先需要 LiveData。有了`S[tateFlow](https://blog.jetbrains.com/kotlin/2020/10/kotlinx-coroutines-1-4-0-introducing-stateflow-and-sharedflow/)` [和](https://blog.jetbrains.com/kotlin/2020/10/kotlinx-coroutines-1-4-0-introducing-stateflow-and-sharedflow/) `[ShareFlow](https://blog.jetbrains.com/kotlin/2020/10/kotlinx-coroutines-1-4-0-introducing-stateflow-and-sharedflow/)`科特林提供了一个绝佳的选择。

如果你想坚持使用 LiveData，想一想是否有一种方法可以让你有粘性或非粘性事件，而不是混合？

在这种情况下，考虑更新您的 Linter 来添加一个定制的 Lint 检查，其中我们的不可空的活动数据不能设置为空，并且在这种情况下编译器会导致错误。

保持警惕！这不是 LiveData 的问题，其他库也可能发生这种情况。我们已经习惯了 Kotlin 编译器的警告。但是如果它不能，它可能会给你带来麻烦，如果没有它你就不会有这些麻烦。