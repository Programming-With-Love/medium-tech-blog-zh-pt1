# 从 Java 使用 Kotlin 标准库

> 原文：<https://medium.com/google-developer-experts/using-the-kotlin-standard-library-in-java-ea0766deac10?source=collection_archive---------2----------------------->

![](img/7ffba6e63f9c8f8097a16fc0a1be435a.png)

giphy.com

如果你每天都在使用 Kotlin，你可能会喜欢这种语言，不想回到 Java。然而，我们中的许多人工作的代码库并不纯粹是 Kotlin。我们在 [SoundCloud](https://soundcloud.com/) 的 Android 代码库仍然有相当数量的 Java 代码。随着时间的推移，这一比例越来越小，但现在并不迫切需要大规模迁移遗骸。

所以，每隔一段时间，我就发现自己，在改 Java 文件。在这种情况下，我想念一些我习惯的 Kotlin 函数；我很容易想到字符串操作或处理集合作为例子。当然，Java 有强大的实用程序库，如 [Googles Guava](https://github.com/google/guava) 或 [Apache Commons](https://commons.apache.org/proper/commons-collections/) ，在 Kotlin 出现之前，我们曾经使用过这些库。

但是当整天写 Kotlin 代码时，记住那些“老”API 有时很难。这是一个完整的上下文切换。使用我们在科特林使用的东西会容易得多。你应该这么做！让我给你看看！

假设我们想要创建一个项目列表。在科特林，我们会写:

```
valitems= ***listOf*(
    items1, item2, item3
)**
```

在 Java 中，我会把它写成:

```
List<Item> items = **Arrays.*asList***(item1, item2, items3);
```

用法是非常相似的，但是你仍然需要记住函数的名字。使用以下代码不是更简单吗:

```
List<Item> items = **listOf(**item1, item2, items3);
```

**是的，你可以！**

你所需要做的就是`static import`这个函数！这就像`listOf`一样，就像我们日常使用的许多 Kotlin 实用函数一样，都是可以从 Java 中使用的简单静态函数。

更好的是，由于您的项目已经使用了 Kotlin，您已经包含了那些依赖项，不需要额外的库。

# 近距离观察

如果您点击 Kotlin 中的`listOf` 的详细信息，您会看到一个名为`Collections.kt`的文件，其定义如下:

```
public **fun** <T> **listOf**(vararg elements: T)**: List<T>**
```

它是一个自由函数，不绑定任何类。但是从 Java 的角度来看，这看起来像这样:

```
public final **class CollectionsKt** {
   @NotNull
   **public static** final List<T> **listOf**(@NotNull T... elements) {
   ...
} 
```

这个阶层来自哪里？注意，没有绑定到 Kotlin 中的类的函数仍然必须放入字节码中的类中。在这种情况下，来自文件`Collections.kt`的函数将简单地在一个名为`CollectionsKt`的类中结束。
还有疑问，如果你想知道你需要在 Java 中静态导入的类名，只需转到你的 Kotlin 函数的声明并检查文件名。

# 扩展功能

扩展函数呢？在我看来，这些是 Kotlin 提供的最强大的工具之一。从 Java 的角度来看，扩展函数也只是静态函数，接收者成为第一个参数。让我们看一个例子:

```
validItems *=* items**.filterNotNull()**
```

`filterNotNull`是一个扩展函数，我们可以在声明中看到:

```
public fun<T : Any> **Iterable<T?>.filterNotNull**(): List<T>
```

在 Java 中，这看起来像这样:

```
**public static** finalList<T> **filterNotNull**(@NotNull Iterable<T> $this$filterNotNull)
```

用法如下所示:

```
*validItems = filterNotNull*(items);
```

那不是太糟糕，是吗？

在 Java 中使用扩展函数的唯一缺点是不能像在 Kotlin 中那样将调用与其他扩展函数连接起来:

```
items.filterNotNull().find{...}.map{...}
```

正如我们所了解的，这在 Java 中是行不通的:接收者成为第一个参数，所以我们需要将它重写为多行代码。但是每个功能都可以使用。

# 边注

如果您进一步研究这个例子，您可能会注意到`filterNotNull`函数位于一个名为`_Collections.kt`而不是`Collections.kt`的文件中。尽管如此，我们还是导入了类`CollectionsKt`，与上面我们使用的名字相同。这是因为作者通过注释明确定义了目标类名:`@file:kotlin.jvm.JvmName(“CollectionsKt”)`

# 费用

您可能会问，在 Java 中使用这些 Kotlin 库时，是否有任何隐藏的成本。答案很简单:不会！像所有用 Java 编写的库一样，Kotlin 库也是字节码。在普通的 Java 库中，您可能找不到的惟一东西就是空检查。与转移到 Kotlin 时类似，在不期望的地方传递 null 时要注意突然的异常。

# 摘要

Kotlin 中的许多函数都是作为扩展函数实现的，可以很容易地通过静态导入从 Java 中使用。您可能无法使用 Kotlin 的所有功能，尤其是考虑到像协程这样的功能。虽然调用标准函数不是问题，但应该经常使用。你甚至可以去掉一些只用于 Java 的第三方库。

当在混合代码库上工作时，使用相同的功能减少了团队的认知负荷。这对于开发人员直接从 Kotlin 开始而没有长期 Java 背景的人来说更有价值，我们会越来越多地看到这种情况。

但是让我提醒您，这个技巧是针对您的代码库还没有完全 Kotlin 时的中间状态的。如果你有一个 Java 项目，并打算长期使用 Java，那么使用 Kotlin 库不应该是你的策略。