# 面试用 Kotlin 第 1 部分:常见数据类型

> 原文：<https://blog.kotlin-academy.com/kotlin-for-interviews-part-1-common-data-types-886ea1e40645?source=collection_archive---------1----------------------->

![](img/2f4d31a1ddd9b0cb1a00d70f61bda580.png)

Photo by [Christina Rumpf](https://unsplash.com/@rumpflet?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我花了五月和六月的大部分时间准备面试，我说的准备面试是指做大量的 Leetcode。自从我申请 Android 职位以来，我决定解决 Kotlin 中的大多数问题，并注意到我写的 Kotlin 与我在工作中使用的 Kotlin 非常不同。我决定将面试准备过程中经常出现的代码片段汇总成一份备忘单，然后在 5 篇中型文章中深入研究这些代码片段。你可以在这里找到备忘单。

在第 1 部分中，我将介绍一些在算法和数据结构问题中经常出现的常见数据类型——如何初始化它们，如何对它们进行一些常见操作，以及一些用例。我们将回顾:

*   [可变列表](#6f73)
*   [数组](#adbe)
*   [散列表](#f261)
*   [优先级队列](#911c)
*   [比较器](#332c)
*   [可比](#3f49)

你可以在这里找到第 2 部分[这里](/kotlin-for-interviews-part-2-collection-functions-a4a488fa0a14)，第 3 部分[这里](/kotlin-for-interviews-part-3-numbers-and-math-786660295cea)，第 4 部分— [这里](/kotlin-for-interviews-part-4-iteration-b176dee4f1ae)，第 5 部分— [这里](/kotlin-for-interviews-part-5-frequently-used-code-snippets-444ad4d137f5)。

## 可变列表

这是我最常用的数据结构，你可能已经很熟悉了。我想指出的是，如果你遇到一个需要堆栈或队列的问题，你可以很容易地将它用作堆栈或队列；在广度优先搜索中，我通常使用一个可变列表来表示队列。

```
*// Create an empty MutableList* **val list1 = mutableListOf<Int>()***// Create a MutableList with elements [0, 1, 2, 3, 4]* **val list2 = mutableListOf(0, 1, 2, 3, 4)**
```

**使用可变列表作为堆栈**

*   `add()`在列表末尾添加一个元素，可用作`push`
*   `removeLast()`从列表中删除最后一个元素并返回，可用作`pop`
*   `last()`返回最后一个元素而不删除，可用作`peek`

```
**val stack: MutableList<Int> = mutableListOf()** *// push* **stack.add(1)
stack.add(2)** *// peek* **stack.last()** *// returns 2
// pop*
**stack.removeLast()** *// returns 2* **stack.removeLast()** *// returns 1*
```

**使用可变列表作为队列**

*   `add()`可用作`enqueue`
*   `removeAt(0)`从列表中删除第一个元素并返回，可用作`dequeue`
*   `get(0)`返回第一个元素，可作为`peek`

```
**val queue: MutableList<Int> = mutableListOf()** *// enqueue* **queue.add(1)
queue.add(2)** *// peek* **queue[0]** *// returns 1
// dequeue*
**queue.removeAt(0)** *// returns 1* **queue.removeAt(0)** *// returns 2*
```

## 排列

数组看起来很基本，但是当我开始准备面试时，我意识到我对它们不是很了解，因为我倾向于使用列表。当一个 Leetcode 问题有一个数组输入或者期望一个数组输出时，我必须做一些谷歌搜索。

在 Kotlin 中，数组是固定大小的，支持`get()`、`set()`和`size`，以及大多数 [Kotlin 集合函数](https://kotlinlang.org/docs/reference/collection-operations.html)。您可以使用`[]`，也就是一个索引操作符，更简洁地进行获取和设置。您不能更改大小或删除元素。我发现数组对于需要跟踪固定数量的计数器或标志的问题很有用。

有两种方法可以初始化数组。可以用`arrayOf<T>()`:

```
*// Create an array with elements [0, 1, 2, 3, 4]*
**val array1 = arrayOf<Int>(0, 1, 2, 3, 4)**
```

或者用`Array<T>(n) { initFunction }`。这将创建一个大小为 *n* 的数组，其中每个元素都是使用`initFunction`创建的。`initFunction`可以只是一个缺省值，比如一个数组`Ints`的-1，或者一个代码块。

```
*// Create an array with elements [0, 0, 0, 0, 0]*
**val array2 = Array<Int>(5) { 0 }***// Create an array with elements [0, 2, 4, 6, 8]
// The initFunction block can access the index as a parameter.
// Here, each element is initialized by taking its index and multiplying it by 2.* **val array3 = Array<Int>(5) { index -> 2 * index }**
```

以下原语类型也有专用的*数组*方法:`double` *，* `float` *，* `long` *，* `int` *，* `char` *，* `short` *，* `byte` *，* `boolean`，通过避免科特林的[装箱开销](https://kotlinlang.org/docs/reference/basic-types.html#representation):

```
*// Create an array with elements [0, 1, 2, 3, 4]* **val intArray1 = intArrayOf(0, 1, 2, 3, 4)***// Create an array with elements [0, 0, 0, 0, 0]* **val intArray2 = IntArray(5) { 0 }***// Create an array with elements [true, true, false, true, true]* **val booleanArray1 = booleanArrayOf(true, true, false, true, true)***// Create an array with elements [true, false, true, false, true]* ***//*** *Each element is initialized by by whether or not its index is even* **val booleanArray2 = BooleanArray(5) { index -> index % 2 == 0 }**
```

[运行一维数组的和](https://leetcode.com/problems/running-sum-of-1d-array/)是一个简单的 Leetcode 问题的例子，您可以使用`IntArray`来跟踪结果:

```
*/**
** ***Problem description****: Given an array* nums*. We define a running sum 
* of an array as* *runningSum[i] = sum(nums[0]…nums[i])**. Return the 
* running sum of* nums*.
*
** ***Example:*** ** Input: nums = [1,2,3,4]
* Output: [1,3,6,10]
* Explanation: Running sum is obtained as follows: [1, 1+2, 1+2+3, 
* 1+2+3+4].
** /*
**fun runningSum(nums: IntArray): IntArray {
    val results = IntArray(nums.size) { 0 }
    results[0] = nums[0]
    for (i in 1 *until* nums.size) {
        results[i] = results[i - 1] + nums[i]
    }
    return results
}**
```

## 散列表

HashMaps 表示一组键-值对，其中键是惟一的，每个键只能有一个值。在面试问题中，它们通常有助于存储信息。例如，`HashMap<Node, Boolean>`可以存储图中哪些节点已经被访问过，而`HashMap<String, Int>`可以存储句子中出现的不同单词的计数。

一些有用的功能:

*   `clear()` 从地图上删除所有元素。
*   `containsKey(key: K)`如果映射包含指定的键，则返回 true。
*   `remove(key: K)`从该映射中删除指定的键及其相应的值。
*   `getOrPut(key: K, defaultValue: () -> V)` 如果给定键在地图中，则返回该键的值。如果不是，调用`defaultValue`函数，将其结果放入给定键下的 map 中，并返回。

```
*// Create an empty HashMap* **val map1 = hashMapOf<String, Int>()***// Create a HashMap with initial values* **val map2 = hashMapOf(
    "Never Let Me Go" to 2005,
    "A Little Life" to 2015
)***// Two ways to insert a key-value pair* **map2.put("The Name of the Wind", 2007)
map2["The Bell Jar"] = 1963***// Two ways to retrieve a value* **map2.get("The Name of the Wind")** *// returns 2007* **map2["The Bell Jar"]** *// returns 1963**// Using getOrPut()* **map2.getOrPut("The Name of the wind", 1990)** *// Returns 2007*
**map2.getOrPut("1984", 1949)** *// Inserts new entry and* returns 1949
```

## 优先级队列

当您希望根据元素的优先级来处理元素时，优先级队列非常有用。这个类是使用堆实现的。它们经常被用在要求 K 次最大，K 次最小，K 次最频繁等等的面试问题中。

您可以使用`PriorityQueue<T>()`来初始化一个，在这里它将使用类型`T`的自然排序来确定优先级，或者使用像`PriorityQueue<T> { (a: T, b: T) -> Int }`这样的自定义`Comparator`，在这里您可以指定一种自定义方式来确定优先级。

一些有用的功能:

*   `add(element: E)`和`offer(element: E)`都插入了一个元素，你可以使用其中任何一个；`PriorityQueue`有两个函数做完全相同的事情，因为它实现了两个接口`Collection`和`Queue`。集合使用`add()`进行插入，而队列使用`offer()` *。*
*   `peek()` 返回，但不删除队列的头。
*   `poll()` 返回并删除队列头。

```
*// Create a PriorityQueue pq where the maximum value Int has highest 
// priority*
**val pq = PriorityQueue<Int> {
    a, b -> b - a
}** *// Add 2, 1, 3 to the pq*
**pq.offer(2)
pq.add(1)
pq.offer(3)**

**pq.peek()** *// returns 3 but does not remove it from pq*// *pq will be empty after 3 iterations* **while(pq.*isNotEmpty*()) {
    pq.poll()***// returns 3, then 2, then 1* **}**
```

## 比较仪

`Comparator<T>`接口提供了一个比较函数，用于在类型`T`的实例之间强加一个总排序。对于需要排序或区分优先次序的技术面试问题来说，这通常很有用，因为大多数 Kotlin 排序算法会让你通过一个`Comparator`。

您可以使用 Kotlin 的`[compareBy(](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/compare-by.html))`函数创建一个`Comparator`，它使用您传入的函数序列来计算比较结果。对成对的值连续计算函数序列，一旦函数返回的两个值的结果不相等，就返回结果，并跳过该对的其余函数。

```
**val customComparator = compareBy<T> { 
    ...
}**
```

下面是一个用绝对值来比较`Ints`的`Comparator`的例子:

```
**val absComparator = compareBy<Int>{ Math.abs(it) }
val nums: MutableList<Int> = mutableListOf(-1, 3, 7, -5, 3)
nums.sortWith(absComparator)***// Or if you only need to use the comparator once, you can pass in 
// the call to compareBy() directly.* **val nums: MutableList<Int> = mutableListOf(-1, 3, 7, -5, 3)
nums.sortWith(compareBy{ Math.abs(it) })**
```

这里有一个`Comparator`的例子，它通过颜色属性来比较自定义的`Card`类，并且`BLACK`卡比`RED`卡“更好”。我们将使用 number 属性来打破平局:

```
**enum class Color { RED, BLACK }****data class Card(val number: Int, val color: Color)****val cardComparator = compareBy(
** *// Compare by color first***
    { if(it.color == BLACK) 1 else 0 }, 
***    // If the results of color comparison are equal, compare by 
    // number* **{ it.number }
)****val cards: MutableList<Card> = mutableListOf(
    Card(4, Color.RED), 
    Card(2, Color.BLACK), 
    Card(1, Color.RED), 
    Card(3, Color.BLACK)
)
cards.sortWith(cardComparator)** 
*// cards becomes:
// [Card(1, RED), Card(4, RED), Card(2, BLACK), Card(3, BLACK)]*
```

如果比较比较复杂，您可能希望创建一个显式实现`Comparator`接口的对象，并覆盖`compare()` 函数。如果参数相等，它应该返回零；如果第一个参数小于第二个参数，它应该返回负数；如果第一个参数大于第二个参数，它应该返回正数。

```
**object CustomComparator: Comparator<T>{ 
        override fun compare(a: T, b: T): Int { 
            ...
            return ...
        }
}**
```

下面是一个`Comparator`对象在卡片比较中的样子:

```
**object cardComparator: Comparator<Card>{ 
        override fun compare(a: Card, b: Card): Int { 
            if (a.color == b.color) {
                return a.number - b.number
            } else if (a.color == Color.BLACK) {** *// Case where a is BLACK and b is RED* **return 1
            } else {*** // Case where a is RED and b is BLACK   ***              
                return -1
            }
        }
}**
```

## 可比较的

对于自定义类，您也可以让您的类扩展`Comparable`接口并覆盖`compareTo()`来实现总排序，而不是创建一个单独的对象。您可以使用 Kotlin 的`[compareValuesBy(](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/compare-values-by.html))`辅助函数，类似于`compareBy()`，它使用您传入的函数序列来计算比较结果。因此，不需要使用`sortWith()`并传入您的自定义比较器，您可以只使用`sort()`，该函数将知道如何进行总排序。

```
**enum class Color { RED, BLACK }****data class Card(val number: Int, val color: Color)
        : Comparable<Card> {
    override operator fun compareTo(other: Card): Int {
        compareValuesBy(
            this, 
            other,** *// Compare by color first
* **{ it.color },** *// If the results of color comparison are equal, 
            // compare by number
* **{ it.number }
        ) 
    }
}
val cards: MutableList<Card> = mutableListOf(
    Card(4, Color.RED), 
    Card(2, Color.BLACK), 
    Card(1, Color.RED), 
    Card(3, Color.BLACK)
)
cards.sort()** 
*// cards becomes:
// [Card(1, RED), Card(4, RED), Card(2, BLACK), Card(3, BLACK)]*
```

同样，如果您能更清楚地理解，您可以选择在没有`compareValuesBy()`助手的情况下实现`compareTo()`:

```
**data class Card(val number: Int, val color: Color)
        : Comparable<Card> {
    override operator fun compareTo(other: Card): Int {
        if (this.color == other.color) {
            return this.number - other.number
        } else if (this.color == Color.BLACK) {
            return 1
        } else {
            return -1
        }
    }
}**
```

这就是第 1 部分！在[第 2 部分](/kotlin-for-interviews-part-2-collection-functions-a4a488fa0a14)中，我回顾了有用的 Kotlin 集合函数。这里是[到备忘单](/kotlin-for-interviews-cheatsheet-88a9831e9d55)的链接，再次涵盖了所有 5 个部分。

# 点击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 Medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)