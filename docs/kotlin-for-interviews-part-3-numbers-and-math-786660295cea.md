# 面试考林第三部分:数字和数学

> 原文：<https://blog.kotlin-academy.com/kotlin-for-interviews-part-3-numbers-and-math-786660295cea?source=collection_archive---------1----------------------->

![](img/aac028552a9e3ee52db579126efda8f2.png)

Photo by [Crissy Jarvis](https://unsplash.com/@crissyjarvis?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这是 Kotlin for Interviews 的第 3 部分，在这个系列中，我回顾了在 Android 面试准备期间经常出现的 Kotlin 函数和代码片段。我还编辑了一个备忘单，涵盖了这个系列的所有 5 个部分，你可以在这里找到。

你可以在这里找到第 1 部分:常用数据类型[，在这里](/kotlin-for-interviews-part-1-common-data-types-886ea1e40645?source=collection_home---4------1-----------------------)找到第 2 部分:集合函数[，在这里](/kotlin-for-interviews-part-2-collection-functions-a4a488fa0a14)找到第 4 部分:迭代[，在这里](/kotlin-for-interviews-part-4-iteration-b176dee4f1ae)找到第 5 部分:常用代码片段[。](/kotlin-for-interviews-part-5-frequently-used-code-snippets-444ad4d137f5)

这一部分包括:

*   [数字类型](#f288)
*   [数学运算符](#7f90)
*   [有用的功能](#9021)
*   [有用的常数](#9009)

一些数学性更强的面试问题要求你对科特林数和数学运算有比工作所需更深的理解，所以这一部分对它们进行了讨论。

# 数字类型

你可以在 Kotlin 的[基本类型文档](https://kotlinlang.org/docs/reference/basic-types.html#numbers)中找到更深入的概述。

*   `Int`是没有小数点的数字。它的范围从-2 到 2 -1，以 32 位存储。
*   `Long`也是一个没有小数点的数字，但它的范围是从-2⁶到 2⁶ -1，以 64 位存储，所以当需要存储更大的数字时可以使用。
*   `Float`是一个浮点数，可以精确表示 6–7 位十进制数字。这意味着如果你看到一个 0.12345678 的浮点数，它将精确到 0.123457，如果你看到 1234.5678，它将精确到 1234.57。它存储在 32 位中，其中 24 位用于表示基数，8 位表示指数。
*   `Double`是一个浮点数，可以精确表示 15–16 位十进制数字。它以 64 位存储。如果你不指定一个十进制数是`Float`还是`Double`，Kotlin 默认会将其初始化为`Double`。

以下是初始化每种类型的方法:

```
**val oneInt = 1** *// Int*
**val oneLong = 1L** *// Long* **val oneFloat = 1f** *// Float* **val oneDouble = 1.0** *// Double**// Note: simply specifying that a number is a Float or a Double, for example 
//* **val oneFloat: Float = 1 
// val oneDouble: Double = 1** *// does NOT work. You’ll get a "*The integer literal does not conform // to the expected type Float/Double*" error if you don’t append the* **f** *or* **.0**.
*// However you can declare a Long without appending the* ***L.***
// **val oneLong: Long = 1** *// is fine.*
```

# 数学运算符

科特林对大多数操作符都使用了期望的符号——`+`表示加法，`—`表示减法，`*`表示乘法，`/`表示除法，`%`表示 mod——所以我就不赘述了。

然而，Kotlin 中的指数稍微复杂一些。你必须使用`kotlin.math`库中的`Double.pow()`或`Float.pow()` 。没有`Int.pow()`，所以如果你使用`Int`，你必须先将它转换为`Double`。

```
*// pow() isn't in the standard library, so you'd have to explicitly // add this import.* **import kotlin.math.pow****3.0.pow(4)** *// returns 81.0 as a Double*
**3.toDouble().pow(4)** *// also returns 81.0 as a Double**// Note:* **3.pow(4)** *does NOT work and will give you an "*Unresolved 
// reference error"*, since there's no Int.pow() in kotlin.math.*
```

# 一些更有用的功能

我包含了我经常使用的数学函数的示例代码；你可以在这里看到功能[的完整列表。](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.math/#functions)

*   `abs()`返回给定值的绝对值。
*   `max()`返回两个给定值中的较大值。
*   `ceil()` 将给定值四舍五入到最接近的大于自身的整数。
*   `floor()` 将给定值四舍五入到比自身小的最近整数。
*   `round()`将给定值向最接近的整数舍入。
*   `sqrt()`返回给定值的正平方根。

```
*// Absolute value* **Math.abs(-1)** *// returns 1
// Max*
**Math.max(1, 3)** *// returns 3*
*// Min*
**Math.min(1, 3)** *// returns 1
// Ceiling*
**Math.ceil(2.2)** *// returns 3.0*
*// Floor*
**Math.floor(2.8**) *// returns 2.0*
*// Rounding*
**Math.round(2.5)** *// returns 3*
**Math.round(1.5)** *// returns 2*
**Math.round(1.4)** *// returns 1*
**Math.round(2.6)** *// returns 3*
*// Square root*
**Math.sqrt(6.25)** *// returns 2.5*
```

或者，您可以显式导入 [kotlin.math](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.math/) 包，并使用它提供的不带`Math.`前缀的字段和函数:

```
**import kotlin.math.****// Absolute value* **abs(-1)** *// returns 1* **-1.absoluteValue** *// also returns 1
// Max*
**max(1, 3)** *// returns 3*
*// Min*
**min(1, 3)** *// returns 1
// Ceiling*
**ceil(2.2)** *// returns 3.0*
*// Floor*
**floor(2.8**) *// returns 2.0*
*// Rounding*
**round(2.5)** *// returns 3* **2.5.roundToInt()** *// also returns 3
// Square root*
**sqrt(6.25)** *// returns 2.5*
```

# 一些有用的常数

Kotlin 的每个数字类型都有`MAX_VALUE`和`MIN_VALUE`常量。

一个问题是`MIN_VALUE`的概念定义对于整数和浮点数是不同的。

*   `Int.MIN_VALUE`保存一个`Int`实例可以拥有的最小值，也就是最大的负值。
*   `Float.MIN_VALUE`保存`Float`的最小*正*非零值。

如果你想要一个`Float`的最大可能负值，你需要使用`-1 * Float.MAX_VALUE`。

```
**Int.MAX_VALUE** *// 2147483647* **Int.MIN_VALUE** *//* -2147483648**Long.MAX_VALUE** *// 9223372036854775807* **Long.MIN_VALUE** *//* -*9223372036854775808***Float.MAX_VALUE** *// 3.4028235E38* **Float.MIN_VALUE** *// 1.4E-45*
**-1 * Float.MAX_VALUE** // *-3.4028235E38***Double.MAX_VALUE** *// 1.7976931348623157E308* **Double.MIN_VALUE** *// 4.9E-324*
**-1 * Double.MAX_VALUE** *//* -*1.7976931348623157E308*
```

对于需要跟踪集合中迄今为止看到的最小或最大数量，并需要用一些东西初始化跟踪器的问题，它们很有用。

例如，查找列表中的最大数字:

```
**var maxSoFar = Int.MIN_VALUE****val numbers: List<Int> = listOf(3, -1, 0, 3352)
numbers.forEach { number ->
    if (number > maxSoFar) {
        maxSoFar = number
    } 
}**
```

这是一个简单的例子，你可以使用`numbers.max()`得到同样的结果，但是你明白了。

这里是到备忘单的[链接，再次涵盖了所有 5 个部分。](/kotlin-for-interviews-cheatsheet-88a9831e9d55)

Kotlin 访谈的第 4 部分涵盖了迭代，在这里发布[。](/kotlin-for-interviews-part-4-iteration-b176dee4f1ae)

# 点击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 Medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)