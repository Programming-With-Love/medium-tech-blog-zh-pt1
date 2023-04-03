# 卡帕头的拼图。学院，第 6 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-6-a83503b1f946?source=collection_archive---------6----------------------->

![](img/1ab4881589540c20868286f2e173e1bf.png)

时间为新电池的科特林字谜从 Kt。学院！本周我们在函数方面有很多乐趣。享受；)

## 一切都是可变的

```
val readonly = listOf(1, 2, 3)

if (readonly is MutableList) {
  readonly.add(4)
}

println(readonly)
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/tree/master/src/collections/everythingIsMutable)

它将显示什么？一些可能性:

a) [1，2，3]
b) [1，2，3，4]
c)不支持的操作异常
d)将不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-48)或阅读本文直到最后，查看答案和解释。

## 构图的乐趣

```
val increment = { i: Int -> i + 1 }
val bicrement = { i: Int -> i + 2 }
val double = { i: Int -> i * 2 }
val one = { 1 }

infix fun <T, R> (() -> T).then(another: (T) -> R) = { another(this()) }
operator fun <T, R1, R2> ((T) -> R1).plus(another: (T) -> R2) = { x: T -> this(x) to another(x) }

fun main(args: Array<String>) {
    val equilibrum = one then double then (increment + bicrement)
    print(equilibrum())
}
```

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

它将显示什么？一些可能性:

a)什么都没有，它不编译
b) 5
c) (1，2)
d) (3，4)

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-52)或阅读本文直到最后，查看答案和解释。

## 有身份的作文

```
val double = { i: Int -> i * 2 }
infix fun <A, I, R> ((A) -> I).then(f: (I) -> R) 
    = { a: A -> f(this(a)) }
fun <T> id(x: T) = x

fun main(args: Array<String>) {
    val f = double then ::id then double then ::id then ::id
    print(f(1))
}
```

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

它将显示什么？一些可能性:

a) 4
b)无法推断出`R`的类型
c)无法引用通用函数`id`d)1

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-54)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于“**一切都是可变的**”正确答案是:

c)不支持的操作异常

为什么？这里有一个解释:

> 辅助函数如`listOf`、`Array.asList()`等返回`java.util.Arrays$ArrayList`而不是`java.util.ArrayList`，不支持突变。

对于“**趣味作文**”的正确答案是:

d)(第 3、4 段)

为什么？这里有一个解释:

> 然后是经典作文。Plus 生成两个函数的结果对。签名正确。我们首先取 1，然后加倍(2)，然后在一个分支中递增(3)，在另一个分支中递增(4)。

对于“**作文与身份**”的正确答案是:

a) 4

为什么？这里有一个解释:

> 这是正确的组合和身份定义，所以我们可以像 puzzler 上呈现的那样组合这个操作。Identity 是一个通用函数(T)->T，但是由于类型推断，它被生成为(Int)->Int。

但是要小心，当我们处理对泛型函数的引用时，类型推理系统并不完美:[https://youtrack.jetbrains.com/issue/KT-24217](https://youtrack.jetbrains.com/issue/KT-24217)

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。