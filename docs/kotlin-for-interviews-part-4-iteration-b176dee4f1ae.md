# 面试用科特林——第 4 部分:迭代

> 原文：<https://blog.kotlin-academy.com/kotlin-for-interviews-part-4-iteration-b176dee4f1ae?source=collection_archive---------2----------------------->

![](img/6c45753a76ff2e315c5ebc1601518efa.png)

Photo by [Dan Gold](https://unsplash.com/@danielcgold?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这是 Kotlin for Interviews 的第 4 部分，在这个系列中，我回顾了在 Android 面试准备期间经常出现的 Kotlin 函数和代码片段。我还编辑了一个包含本系列所有 5 个部分的备忘单，你可以在这里找到。

你可以在这里找到第 1 部分:常用数据类型[，](/kotlin-for-interviews-part-1-common-data-types-886ea1e40645)第 2 部分:集合函数[这里](/kotlin-for-interviews-part-2-collection-functions-a4a488fa0a14)和第 3 部分:数字和数学[这里](/kotlin-for-interviews-part-3-numbers-and-math-786660295cea)和第 5 部分:常用代码片段[这里](/kotlin-for-interviews-part-5-frequently-used-code-snippets-444ad4d137f5)。

这一部分包括:

*   [范围更新](#9ca3)
*   [1D 数组/列表](#d553)
*   [2D 数组/列表](#74be)
*   [地图](#c4a8)
*   [优先级队列](#739b)

许多面试问题需要某种迭代，无论是操作输入数组还是使用映射来存储信息，所以我将介绍一些常见数据结构的不同迭代方法。

# 靶场复习

Kotlin 中的`Range`是由起始值、结束值和步长定义的值序列。两个值之间的步长或距离的默认值为 1。你最常遇到的是`IntRange`，但是你也可以使用`LongRange`和`CharRange`。

# 1D 数组/列表和范围

*   `forEach()`对集合中的每个元素执行给定的操作。这是我在普通项目中最常使用的迭代方法，但在面试中不常使用，因为 1) `forEach()`会抛出一个`ConcurrentModificationException`如果你试图在迭代过程中修改集合，2)很多面试问题也需要考虑索引。
*   `forEachIndexed()`类似于`forEach()`，除了你还可以访问 lambda 中元素的索引，这是你在面试问题中经常需要的。

```
*// Print elements at even indices.*
**list.forEachIndexed { index, element -> 
    if (index % 2 == 0) println("$element")
}**
```

*   `*..*`，又名`rangeTo()`，可以用`for(i in a..b)`的形式来创建然后迭代一个`Range`。这个范围将包括开始(`a`)和结束(`b`)元素，所以如果你想用它来遍历一个列表/数组，你应该把它写成`for (i in 0..list.size-1)`或者用`until`来代替。尽管它最常用于`Int` s，但是你也可以迭代`Char` s。

```
*// Iterate and print from i = 0 to i = 100*
**for (i in 0..100) { println(i) }***// Iterate from i = 0 to i = list.size-1*
**for (i in 0..list.size-1) { println(list[i]) }***// Iterate and print 'a', 'b', 'c', 'd'*
**for (i in 'a'..'d') { println(i) }**
```

*   `downTo()`的工作方式类似于`..`，除了每次迭代都向下一步而不是向上一步。

```
*// Iterate and print from i = 100 to i = 0*
**for (i in 100 downTo 0) { println(i) }**
```

*   `step()`允许您指定每次迭代之间数值的变化。

```
*// Iterate and print 1, 3, 5, 7*
**for (i in 1..8 step 2) { println(i) }***// Iterate and print 8, 5, 2* **for (i in 8 downTo 1 step 3) { println(i) }**
```

*   `until()`包括开始元素，但不包括结束元素。这是我首选的遍历集合索引的方式，因为如果你使用列表的大小作为结束，你就不用担心索引越界。

```
*// Iterate and print from i = 0 to i = 99*
**for (i in 0 until 100) { println(i) }***// Iterate and print elements from i = 0 to i = list.size-1*
**for (i in 0 until list.size) { println(list[i]) }**
```

*   `indices`返回一个代表集合有效索引的`IntRange`，可以类似于`until()`使用。

```
**val list = listOf('a', 'b', 'c')
println(list.indices)** *// Prints 0..2*
*// Iterate and print elements from i = 0 to i = 2* **for (i in list.indices) { println(list[i]) }**
```

*   `repeat()` 执行指定次数的给定功能动作。

```
*// Print "Hello" 100 times*
**repeat(100) {
    println("Hello")
}**
```

# 2D 数组/列表

这种情况在实际项目中并不常见，但在面试问题中却经常出现——网格、迷宫、图表等等都可以用 2D 数组来表示。

下面是我使用`until`遍历 2D 数组或列表的首选方法:

```
**if (grid.isEmpty()) return
for (i in 0 until grid.size) {
    for (j in 0 until grid[0].size) {
        println(grid[i][j])
    }
}**
```

或者，您可以使用`indices`:

```
**if (grid.isEmpty()) return
for (i in grid.indices) {
    for (j in 0 until grid[0].size) {
        println(grid[i][j])
    }
}**
```

# 地图

地图在面试问题中也很常见，尤其是当列表不够用时，作为存储信息的好方法。

如果需要遍历键值对，可以使用`for`循环或`forEach`循环。这个就看个人喜好了。

```
*// Iterate through entries using a for-loop*
**for ((key, value) in map) {
    println("$key = $value")
}***// Iterate through entries using forEach()*
**map.forEach { (key, value) -> println("$key = $value") }**
```

如果您只需要遍历这些键，您可以使用`keys` val 来获得映射中所有键的`Set`，并遍历这些键。

```
**map.keys.forEach { println(it) }**
```

类似地，如果您只需要遍历这些值，`values` val 将返回地图中所有值的一个`Set`。

```
**map.values.forEach { println(it) }**
```

# 优先级队列

当您希望根据元素的优先级来处理元素时，s 很有帮助。它们经常被用在要求 K 次最大，K 次最小，K 次最频繁等等的面试问题中。我在第 1 部分中更详细地介绍了它们，但是这里提醒一下遍历一个是什么样子的。

```
**val pq = PriorityQueue<Int>(listOf(2, 1, 3))**
*// pq will be empty after 3 iterations*
**while(pq.isNotEmpty()) {
    println(pq.poll())** *// prints 1, then 2, then 3* **}**
```

第 4 部分到此为止！第五篇也是最后一篇科特林访谈文章涵盖了常用的代码片段。这里是[链接到备忘单](/kotlin-for-interviews-cheatsheet-88a9831e9d55)再次涵盖所有 5 个部分。

# 点击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 Medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)