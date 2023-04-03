# 继承的危险

> 原文：<https://blog.kotlin-academy.com/the-perils-of-inheritance-698b777fa773?source=collection_archive---------1----------------------->

![](img/3181607cd68ad0c2d4595afb1de5b87f.png)

Photo by [Helloquence](https://unsplash.com/@helloquence?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

本文将展示类继承的危险。本文将说明类继承的替代方法——组合。看完这篇文章，你就会明白为什么 Kotlin 默认所有类都是 final 了。这篇文章将解释为什么除非有充分的理由，否则不应该创建 Kotlin 类。

假设我们有以下接口:

Fig. 1: Insertable interface

此外，`BaseInsert`是`Insertable<Number>`接口的一个实现。`BaseInsert`是`open`。所以，我们可以延长`BaseInsert`。

我们希望`CountingInsert`扩展`BaseInsert`。每次代码插入一个`Number`，代码必须将变量`count`加 1。所以，我们有:

Fig. 2: count algorithm implemented through inheritance

这种实现应该是可行的。第 8 行增加一个`count`；第 13 行是变量参数的个数。

代码没有按预期工作。参见下面图 3 的第 7 行。

Fig 3\. CountingInsert() produces wrong result

问题出现在下面图 4 的第 10 行:

Fig. 4: Implementation of BaseInsert

`BaseInsert.insertAll`功能是一种便利功能。`insertAll`函数为`vararg`列表中的每一项调用`insert`。`CountingInsert`类重复计算了整数 3 和 4 的插入。`CountingInsert.insertAll`执行语句`count++`两次；语句`count += items.size`一次。`CountingInsert.insertAll`功能将`count`增加了四个而不是两个。

有其他选择吗？是的。我们可以修改代码，也可以使用合成。

更改代码似乎是一个显而易见的解决方案。假设我们被允许改变基类。我们可以将`BaseInsert.insertAll`的实现改为:

Fig. 5 Updated implementation of BaseInsert.insertAll

这个实现避免了调用`BaseInsert.insert()`，这是我们麻烦的来源。

假设我们没有访问`BaseInsert`类的权限。然后我们可以移除对`insertAll()`的覆盖。参见图 6:

Fig 6\. CountingInsert class with the override to insertAll removed

通过修改代码来解决问题是脆弱的。`CountingInsert`类依赖于`BaseInsert`的实现细节。有更好的方法吗？对，就用构图吧。

图 7 是通过组合的实现:

Fig. 7: Implementation by Composition

假设`BaseInsert`类使用图 4 的实现。当我们测试`InsertDelegation`类时，结果是正确的。参见下面图 8 的第 15 行。

Fig 8.: Test results for CompositionInsert

比较图 2 和图 7，`insert`和`insertAll`的实现是相似的。参见图 9。下面。

Fig. 9: comparison of Inheritance vs composition

除了一个例外，可比较的方法是相同的。继承用途`super`；构图用途`insertable`。比较第 3 行和第 12 行；和 7 相对于图 8 的 16。委托模式将插入任务留给了`insertable`。`CompositionInsert`类递增`count`变量。相比之下，继承打破了`BaseInsert`类的封装。

问题的根本原因是什么？假设`BaseInsert`不是`open`。参见图 4 的第 1 行。如果`BaseInsert`是`final`，那么 Kotlin 编译器会将图 2 和图 5 中的代码标记为错误。只有图 7 中的解决方案是可行的。当我们制作`BaseInsert`类`final`时，`BaseInsert`的封装并没有被打破。

科特林明白遗传的危险。Kotlin 禁止继承，除非开发者将类标记为`open`。结论:一般来说，Kotlin 类应该是`final`类，除非有很好的理由让它成为`open`类。

你需要 Kotlin 工作室吗？访问我们的网站，看看我们能为你做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)