# Effective Kotlin:考虑使用具有原语的数组进行性能关键处理

> 原文：<https://blog.kotlin-academy.com/effective-kotlin-use-arrays-with-primitives-for-performance-critical-processing-297283ed1f90?source=collection_archive---------0----------------------->

![](img/a94b35ed8ccdbcf36e8b35935ef9d622.png)

科特林在幕后非常聪明。我们不能在 Kotlin 中声明原语，但是当我们不使用像对象这样的变量时，它们会被使用。例如，看下面的例子:

```
**var** i = 10
i = i * 2
*println*(i)
```

这个声明使用了原语`int`。这是它在 Java 中的表示:

```
// Java **int** i = 10;
i = i * 2;
System.***out***.println(i);
```

这个实现比使用`Integer`的实现快多少？我们去看看。我们需要在 Java 中定义这两种方式:

```
**public class** PrimitivesJavaBenchmark {

    **public int** primitiveCount() {
        **int** a = 1;
        **for** (**int** i = 0; i < 1_000_000; i++) {
            a = a + i * 2;
        }
        **return** a;
    }

    **public** Integer objectCount() {
        Integer a = 1;
        **for** (Integer i = 0; i < 1_000_000; i++) {
            a = a + i * 2;
        }
        **return** a;
    }
}
```

当您测量这两种方法的性能时，您会注意到一个巨大的差异。在我的机器中，使用`Integer`的需要 *4 905 603* ns，而使用原语的需要 *316 594* ns ( [自己检查](https://github.com/MarcinMoskala/effective-kotlin-tests/blob/master/src/main/java/org/kotlinacademy/PrimitivesJavaBenchmark.java))。这就少了 15 倍！这是一个巨大的差异！

![](img/0e127b4573260b68db41e2eab634b7c3.png)

我们怎么可能有这样的差异？图元比对象轻得多。它们只是内存中的一个数字。他们不需要 OOP 周围的一切。当你看到这种差异时，你应该感谢 Kotlin 尽可能使用原语，我们甚至不需要意识到这一点。尽管您应该知道有些事情会阻止编译器使用原语:

*   可空类型不能是基元。编译器是聪明的，当它致力于你不设置 null 时，它就使用原语。如果它不确定，那么它需要使用非原始类型。请记住，在代码的性能关键部分，这是可空性的额外成本。
*   基元不能用作泛型类型参数。

第二个问题尤其重要，因为我们很少有只处理数字的代码的性能关键部分，但我们经常有一个处理元素集合的部分。这是一个问题，因为每个泛型集合都使用非基元类型。例如:

*   `List<Int>`相当于 Java `List<Integer>`
*   `Set<Double>`相当于 Java `Set<Double>`

当我们需要对数字列表进行操作时，这个事实是一个很大的成本。虽然也有解决办法。有一个 Java 集合允许原语。那是什么？数组！

```
// Java **int**[] **a** = { 1,2,3,4 };
```

如果在 Java 中可以使用带原语的数组，那么在 Kotlin 中也是可以的。为此，我们需要使用一种特殊的数组类型，用不同的原语来表示数组:`IntArray`、`LongArray`、`ShortArray`、`DoubleArray`、`FloatArray`或`CharArray`。让我们使用`IntArray`并对比`List<Int>`来看看对代码的影响:

```
**open class** InlineFilterBenchmark {

    **lateinit var list**: List<Int>
    **lateinit var array**: IntArray

    @Setup
    **fun** init() {
        **list** = *List*(1_000_000) **{ it }
        array** = IntArray(1_000_000) **{ it }** }

@Benchmark
    **fun** averageOnIntList(): Double {
        **return list**.*average*()
    }

@Benchmark
    **fun** averageOnIntArray(): Double {
        **return array**.*average*()
    }
}
```

差别不是那么壮观，但总是看得见。例如，`average`函数快了大约 25%，这要归功于在幕后使用了原语([自己看看](https://github.com/MarcinMoskala/effective-kotlin-tests/blob/master/src/main/kotlin/org/kotlinacademy/PrimitiveArraysBenchmark.kt))。

具有原语的数组也比集合更轻。当你进行测量时，你会发现上面的`IntArray` 分配了 400 000 016 字节，而`List<Int>`分配了 2 000 006 944 字节。是 5 倍多！

正如您所看到的，原语和带原语的数组可以用作代码中性能关键部分的优化。它们分配的内存更少，处理速度更快。尽管在大多数情况下，改进并不显著，不足以在默认情况下使用具有原语的数组来代替列表。列表更直观，也更常用，所以在大多数情况下我们应该使用列表。但是您应该记住这种优化，以防您需要优化一些性能关键部分。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

# 有效科特林

这是关于有效科特林的第二篇文章。当我们看到有兴趣时，我们会发表下一部分，所以如果你想知道更多关于这个主题的内容，请让我们知道这篇文章。在卡帕头。学院我们也在研究关于这个主题的书:

[](https://leanpub.com/effectivekotlin) [## 有效科特林

### 这本书对好的实践进行了深入的分析，包括官方的(Kotlin 和 Google 对 Kotlin 的最佳实践)和…

leanpub.com](https://leanpub.com/effectivekotlin) 

它将涵盖更广泛的主题，并深入其中的每一个问题。它还将包括 Kotlin 和 Google 团队发布的最佳实践、与我们合作的 Kotlin 团队成员的经验，以及“Kotlin 中的有效 Java”系列中涉及的主题。为了支持它并使我们更快地发布它，[使用此链接并订阅](https://leanpub.com/effectivekotlin)。

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注我们。

要在 Twitter 上引用我，请使用 [@MarcinMoskala](https://twitter.com/marcinmoskala) 。使用以下链接订阅时事通讯:

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。