# 卡帕头的拼图。学院，第 4 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-4-e891bfdb676a?source=collection_archive---------0----------------------->

![](img/a01a89e542bcc8d7433e587d066d2523.png)

今天我们将接触 Kotlin 文档，并尝试像使用 Java 一样使用 Kotlin。玩得开心:)

# 扩展是静态解析的

```
open class Cclass D: C()fun C.foo() = "c"fun D.foo() = "d"fun printFoo(c: C) {
    println(c.foo())
}fun main(args: Array<String>) {
    printFoo(D())
}
```

作者:[科特林文档](https://kotlinlang.org/docs/reference/extensions.html#extensions-are-resolved-statically)

它将显示什么？一些可能性:

a)不编译
b)运行时错误
c) c
d) d

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-46)或阅读本文直到最后，查看答案和解释。

# 表达与否

```
fun f1() {
    var i = 0
    val j = i = 42
    println(j)
}fun f2() {
    val f = fun() = 42
    println(f)
}fun f3() {
    val c = class C
    println(c)
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/4-expression-or-not.kts)

`f1`、`f2`、`f3`的结果是什么？一些可能性:
a)42
()->kotlin.Int
C 类
b)42
()->kotlin.Int
不编译
c)不编译
()->kotlin.Int
不编译
d)不编译
不编译
不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-43)或阅读这篇文章直到最后，查看答案和解释。

# 渴望还是懒惰？

```
fun main(args: Array<String>) {
  val x = listOf(1, 2, 3).filter { print("$it "); it >= 2 }
  print("before sum ")
  println(x.sum())
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/3-return-return.kts)

它将显示什么？一些可能性:
a)sum 5
之前的 1 2 3 b)sum 5
之前的 2 3 c)sum 1 2 3 5
之前的 d)顺序不确定

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-47)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于**“静态解析扩展”**，正确答案是:

**c)**

为什么？这里有一个解释:

> 这个例子将打印“C ”,因为被调用的扩展函数只依赖于参数 C 的声明类型，即 C 类。
> 
> 扩展实际上并不修改它们扩展的类。通过定义一个扩展，你不需要在一个类中插入新的成员，而仅仅是让新的函数可以用这种类型变量上的点符号来调用。

对于**“表达与否”**的正确答案是:

**c)不编译
()->kotlin.Int
不编译**

为什么？这里有一个解释:

> 变量初始化和类声明都是 Kotlin 中的语句，它们不声明任何返回类型。我们不能将这样的声明赋给变量。在`f2`中，我们有一个匿名函数。

对于**“渴望还是懒惰？”**正确答案是:

**a)总和 5 之前的 1 2 3**

为什么？这里有一个解释:

> 与 Java8 流不同，Kotlin 中的集合扩展函数是热切的。如果想偷懒，可以使用“T1”或“T2”。

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/#/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/#/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。