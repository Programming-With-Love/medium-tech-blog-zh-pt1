# Twitter 上的 Kotlin 娱乐和教育

> 原文：<https://blog.kotlin-academy.com/kotlin-fun-and-education-on-twitter-5ad5ded8f067?source=collection_archive---------2----------------------->

![](img/34ccca21b526dda328a3c1a50a704067.png)

正如已经提到的，我有时会在 Twitter 上分享一些有趣且有教育意义的代码片段[。在这里，我提出了整个包给你学习和享受。先来点好玩的:)](https://twitter.com/marcinmoskala)

这怎么可能呢？`fun`是 Kotlin 中的一个[硬关键字](https://kotlinlang.org/docs/reference/keyword-reference.html#hard-keywords)，但只是小写。这就是为什么我们可以将类命名为`Fun`，或者像这个例子中的[类型参数](/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)。当我们[用反斜杠字符](https://kotlinlang.org/docs/reference/java-interop.html#escaping-for-java-identifiers-that-are-keywords-in-kotlin)(正式名称为[重音符](https://en.wikipedia.org/wiki/Grave_accent))将硬关键字包围时，它们也可以用作名称。这是一种 Kotlin 方式，允许使用 Java 中的名称，这些名称是 Kotlin 中的保留关键字:

```
`when`(dao.inspections).thenReturn(Flowable.just(Inspection())
assertThat(inspection.id, `is`(1))
```

用它给单元测试起一个描述性的名字也很流行。这让我们也可以使用表情符号:

和科特林玩得开心，也可以提醒自己一些老歌:

我们还可以玩[操作符重载](https://kotlinlang.org/docs/reference/operator-overloading.html)，同时提醒自己[好的老漫画](https://www.youtube.com/watch?v=EtoMN_xi-AM):

# 很高兴知道

有些可能性是 Kotlin 程序员经常忘记的。例如，`fold`功能是最强大的采集处理功能之一:

我还提到了一些可以用来引入元素间交互的函数:

暂时就这样了。如果你想要更多，我会在一两个月后在 medium 上发布下一部分。第一，我会把所有的[都贴在 Twitter](https://twitter.com/marcinmoskala) 上，所以之前跟着我了解一下；)

你需要 Kotlin 工作室吗？访问[我们的网站](https://www.kt.academy/)，看看我们能为你做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)