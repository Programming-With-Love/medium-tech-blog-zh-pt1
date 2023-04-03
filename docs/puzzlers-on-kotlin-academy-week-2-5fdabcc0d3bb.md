# 卡帕头的拼图。学院，第 2 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-2-5fdabcc0d3bb?source=collection_archive---------0----------------------->

![](img/17135e91253ae6119e238adc32a2f605.png)

科特林谜题的新电池时间。本周，我们将更多的精力放在教学和展示关于科特林的惊人事实上。玩得开心:)

# Lambda runnables

```
fun run() {
    val run: () -> Unit = {
        println("Run run run!")
    } object : Runnable {
        override fun run() = run()
    }.run()
}run()
```

作者:[雷德菲尔德](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/16-lambda-runnables.kts)

它将显示什么？一些可能性:
答:“快跑快跑！”
b)不编译
c)堆栈溢出错误
d)以上都不是

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-5)或阅读这篇文章直到最后，查看答案和解释。

# 制作公开摘要

```
open class A {
    open fun a() {}
}abstract class B: A() {
    abstract override fun a()
}open class C: B()
```

a)编译良好
b)错误:类“C”不是抽象的，并且不实现抽象基类成员
c)错误:“a”不覆盖任何内容
d)错误:函数“a”必须有一个体

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

这个事实并不广为人知，但有一天它可能会帮助你；)

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-6)或阅读本文直到最后，查看答案和解释。

# 列表减去列表

```
val list = listOf(1, 2, 3)
print(list - 1)
print(list - listOf(1))val ones = listOf(1, 1, 1)
print(ones - 1)
print(ones - listOf(1))
```

它显示什么？一些可能性:
a)【2，3】【2，3】【1，1】
b)【2，3】【2，3】【1，1】[]
c)【1，3】【2，3】[]【1，1】
d)【2，3】【2，3】【2，3】[][]

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

另一种令人惊讶的知识的平静，可以让你一天不花几个小时寻找错误；)

使用[这个链接](http://portal.kotlin-academy.com/#/?tag=puzzler-39)或者阅读这篇文章直到最后来查看答案和解释。

# 答案和解释

对于**“Lambda runnables”**的正确答案是:

**a)“跑跑跑！”**

为什么？这里有一个解释:

> 在对象表达式中，Kotlin 更喜欢使用定义为局部变量的“run”。小心点！这不太好:

```
val run: () -> Unit = {
    println("Run run run!")
}fun run() {
    object : Runnable {
        override fun run() = run()
    }.run()
}
```

> 也不是这个:

```
fun run() {
    val run: () -> Unit = {
        println(“Run run run!”)
    }
    object : Runnable {
       override fun run() = this.run()
    }.run()
}
```

对于**“制作公开摘要”**的正确答案是:

错误:类“C”不是抽象的，并且没有实现抽象基类成员

为什么？这里有一个解释:

> 我们可以用抽象函数覆盖一个开放函数，但是它是抽象的，我们需要在所有子类中覆盖它。这很好:

```
open class A {
    open fun a() {}
}abstract class B: A() {
    abstract override fun a()
}open class C: B() {
    override fun a() {}
}C().a()
```

对于**“列表减列表”**正确答案是:

**b)【2，3】【2，3】【1，1】**

为什么？这里有一个解释:

> List <t>减去 T 删除第一个等于的元素。列表<t>减去列表<t>过滤掉等于第二个列表中任何元素的所有元素。</t></t></t>

下周谜题:

[](/puzzlers-on-kotlin-academy-week-3-c8c597f14e5e) [## 科特林学院的谜题，第 3 周

### 科特林谜题的新电池时间。有点挑战性。玩得开心:)

blog.kotlin-academy.com](/puzzlers-on-kotlin-academy-week-3-c8c597f14e5e) 

你对谜题有自己的想法吗？在 [Kt 提交。学院门户](http://portal.kotlin-academy.com/#/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/#/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。