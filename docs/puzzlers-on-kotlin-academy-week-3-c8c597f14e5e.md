# 卡帕头的拼图。学院，第 3 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-3-c8c597f14e5e?source=collection_archive---------0----------------------->

![](img/62896652c6e70a60c8ab32e72a1cb614.png)

科特林谜题的新电池时间。有点挑战性。玩得开心:)

# 作文

```
operator fun (() -> Unit).plus(f: () -> Unit) = {
    this()
    f()
}fun main(args: Array<String>) {
    ({ print("Hello, ") } + { print("World") })()
}
```

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

它将显示什么？一些可能性:
a)“Hello，World”
b)错误:期望顶级声明
c)错误:表达式 f 不能作为函数调用
d)错误:未解析的引用(操作符+没有为此类型定义)
e)工作，但不打印任何内容

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-40)或阅读本文直到最后，查看答案和解释。

# 我是什么？

```
fun main(args: Array<String>) {
    val whatAmI = {}()
    println(whatAmI)
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/2-what-am-i.kts)

它将显示什么？一些可能性:

a) "null"
b) "kotlin。Unit"
c)不打印任何
d)不编译的内容

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-41)或阅读本文直到最后，查看答案和解释。

# 返回返回

```
fun f1(): Int {
    return return 42
}fun f2() {
    throw throw Exception()
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/3-return-return.kts)

`f1`和`f2`是怎么工作的？一些可能性:
a)返回 42；抛出异常
b)返回 42；不编译
c)不编译；抛出异常
d)不编译；不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-42)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于**“作文”**正确答案是:

**答【你好，世界】**

为什么？这里有一个解释:

> 函数“plus”定义明确。它返回新函数(使用 lambda 表达式创建),该函数由两个作为参数提供的函数组成。当我们添加两个函数时，我们可以调用另一个函数。当我们调用它时，我们一个接一个地调用 lambda 表达式。

对于**“我是什么？”**正确答案是:

**b)“科特林。单位"**

为什么？这里有一个解释:

> 这是正确的 lambda 表达式，调用时返回“Unit”。

对于**“退货退货”**的正确答案是:

**a)返回 42；抛出异常**

为什么？这里有一个解释:

> `return`表达式有返回类型，可以作为表达式使用，但也以结果`42`结束`f1`执行。类似地，`throw`将`Nothing`声明为返回类型，并且编译良好，但是在它被使用之前，方法`f2`异常结束。

你对谜题有自己的想法吗？在 [Kt 提交。学院门户](http://portal.kotlin-academy.com/#/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/#/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。