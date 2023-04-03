# 科特林揭秘:理解速记 Lambda 语法

> 原文：<https://medium.com/androiddevelopers/kotlin-demystified-understanding-shorthand-lamba-syntax-74724028dcc5?source=collection_archive---------1----------------------->

![](img/383fe39aa3bf5bdf1bef9e23599b4e4d.png)

Photo by [Stefan Steinbauer](https://unsplash.com/photos/HK8IoD-5zpg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/secret?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在一次奥地利之旅中，我参观了位于维也纳的奥地利国家图书馆。特别是州政府大厅，这是一个令人惊叹的空间，感觉就像是印第安纳·琼斯电影中的场景。在房间的周围是这些嵌在架子上的门，很容易想象它们背后隐藏着什么样的秘密。

然而，事实证明，它们只是阅览室。

让我们想象一下，我们有一个跟踪图书馆书籍的应用程序。有一天，我们想知道藏书中最长和最短的书是什么。过了一会儿，我们编写代码，让我们找到这两个:

```
val shortestBook = library.minBy { it.pageCount }
val longestBook = library.maxBy { it.pageCount }
```

完美！但这让我想知道，这些方法是如何工作的？`it`怎么知道，仅仅从写`it.pageCount`就知道，怎么做到这一点？

我做的第一件事是点击进入`minBy`和`maxBy`的定义，它们都在 [_Collections.kt](https://github.com/JetBrains/kotlin/blob/1.2.50/libraries/stdlib/common/src/generated/_Collections.kt) 中。因为它们几乎完全相同，所以让我们把注意力集中在从第[行的第 1559](https://github.com/JetBrains/kotlin/blob/1.2.50/libraries/stdlib/common/src/generated/_Collections.kt#L1559) 行开始的`maxBy`上。

这里的方法是建立在`[Iterable](https://developer.android.com/reference/java/lang/Iterable)`接口上的，但是如果我们做一点小的重写来使用`[Collection](https://developer.android.com/reference/java/util/Collection)` s，并且可能重命名一些变量使其更详细一点，那么就更容易理解了:

```
public inline fun <T, R : Comparable<R>> Collection<T>.maxBy(selector: (T) -> R): T? {
    if (isEmpty()) return null
    var maxElement = first()
    var maxValue = selector(maxElement)
    for (element in this) {
        val value = selector(element)
        if (maxValue < value) {
            maxElement = element
            maxValue = value
        }
    }
    return maxElement
}
```

我们可以看到它只是抓取了`Collection`中的每个元素，检查来自`selector`的值是否大于它所看到的最大值。如果是，它将保存元素和值。最后，它返回找到的最大元素。相当简单。

然而，`selector`看起来有点整洁，它一定是允许我们使用上面的`it.pageCount`的东西，所以让我们再深入研究一下。

甚至在这一行中也有相当多的语法上的好处。`selector: (T) -> R`是一个函数的简称，它接受一个参数，在本例中是`T`，并返回`R`类型的结果。

Kotlin 的工作方式包括一组名为`FunctionN`的接口，其中 *N* 是它接受的参数数量。因为我们有一个接口，我们可以实现`Function1`接口，然后在我们的代码中使用它:

```
class BookSelector : Function1<Book, Int> {
   override fun invoke(book: Book): Int {
       return book.pageCount
   }
}

val longestBook = library.maxBy(BookSelector())
```

这当然说明了它是如何非常容易地工作的。`selector`是一个`Function1`，当给定一个`Book`时，它返回一个`Int`。然后，`maxBy`获取`Int`并将其与它所具有的值进行比较。

顺便说一下，这也解释了为什么通用参数`R`具有类型`R [implements] Comparable<R>`。如果`R`不是`Comparable`，我们就做不了`if (maxValue < value)`。

下一个问题是，我们如何从[到那个](#full)，到我们开始的那个班轮？让我们一步一步地完成这个过程。

首先，代码可以用 lambda 替换，这已经使它缩小了不少:

```
val longestBook = library.maxBy({
    it.pageCount
})
```

下一步是，如果方法的最后一个参数是 lambda，我们可以关闭括号，然后将 lambda 添加到行尾，就像这样:

```
val longestBook = library.maxBy() {
    it.pageCount
}
```

最后，如果一个方法只接受一个 lambda 参数，我们可以完全省略掉这个方法中的`()`,这让我们回到了最初的代码:

```
val longestBook = library.maxBy { it.pageCount }
```

但是等等！那`Function1`呢！每当我使用它时，我是在执行分配吗？

这是一个很好的问题！好消息是，不，你不是。如果你再看一下，你会看到`maxBy`被标记为`inline`函数。这种情况发生在编译期间的源代码级别，因此尽管编译的代码比最初看起来的要多，但不会对性能产生任何显著影响，当然也不会有对象分配。

厉害！现在我们不仅知道图书馆里最短(和最长)的书是什么，我们也更好地理解了`maxBy`是如何工作的。我们看到了 Kotlin 如何为 lambda 使用`[FunctionN](#full)`接口，以及有时如何将 lambda 表达式移到函数的参数列表之外。最后，我们了解到，当只有一个 lambda 参数时，可以完全省略调用函数时通常使用的括号。

查看 [Google Developers](https://medium.com/google-developers) 博客，了解更多精彩内容，并关注更多关于 Kotlin 的文章！