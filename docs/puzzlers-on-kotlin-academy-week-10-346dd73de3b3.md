# 卡帕头的拼图。学院，第 12 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-10-346dd73de3b3?source=collection_archive---------5----------------------->

时间为新电池的科特林字谜从 Kt。学院！玩得开心；)

![](img/1bfd193d7deb1101a6fb5f5fa3d280e9.png)

## 去火星的人口

```
class Population(var cities: Map<String, Int>) {
    val `san francisco` by cities
    val `tallinn` by cities
    val `kotlin` by cities
}

val population = Population(mapOf(
        "san francisco" to 864_816,
        "tallinn" to 413_782,
        "kotlin" to 43_005)
)

fun main(args: Array<String>) {
    // many years have passed, now all humans live on Mars
    population.cities = emptyMap()

    with(population) {
        println("$`san francisco`; $tallinn; $kotlin")
    }
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/delegates/mars/PopulationToMars.kts)

它将显示什么？一些可能性:

a)0；0;0
b)864816；413782;43005
c)NullPointerException
d)NoSuchElementException

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-63)或阅读这篇文章直到结束，查看答案和解释。

## 扩展枚举

```
enum class Color {
    Red, Green, Blue
}

fun Color.from(s: String) = when (s) {
    "#FF0000" -> Color.Red
    "#00FF00" -> Color.Green
    "#0000FF" -> Color.Blue
    else -> null
}

fun main(args: Array<String>) {
    println(Color.from("#00FF00"))
}
```

作者:[德米特里·坎达洛夫](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/types/extendedEnum/extendedEnum.kts)

它将显示什么？一些可能性:

a)绿色
b)彩色。绿色
c)空
d)将不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-71)或阅读本文直到最后，查看答案和解释。

## 不变性

```
class Wrapper<T>

val instanceVariableOne : Wrapper<Nothing> = Wrapper<Any>() // Line A
val instanceVariableTwo : Wrapper<Any> = Wrapper<Nothing>() // Line B
```

作者:[艾伦·凯恩](https://medium.com/@adcaine)

它将显示什么？一些可能性:

A)A 行和 B 行都编译
B)A 行和 B 行不编译
c)A 行编译；B 行不
d)B 行编译；线 A 没有

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-73)或阅读本文直到最后，查看答案和解释。

# 答案和解释

对于“**人口前往火星**”的正确答案是:

b)864816；413782;43005

为什么？这里有一个解释:

> 委托时，委托对象保存在最终字段中，不能更改。

对于“**扩展枚举**，正确答案是:

d)不会编译

为什么？这里有一个解释:

> 颜色的扩展功能适用于颜色的实例，例如颜色。Blue.from()扩展函数只能在枚举本身上使用，前提是它有一个伴随对象:

```
enum class Color {
  Red, Green, Blue;
  companion object 
}

fun Color.Companion.from(...)
```

对于**不变性**，正确答案是:

B)行 A 和 B 不编译

为什么？这里有一个解释:

> 类包装器相对于 t 是不变的，所以，包装器和包装器是不同的类型。没有什么是任何的子类型，这并不重要。

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？访问[我们的网站](https://www.kt.academy/)，看看我们能为您做些什么。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。