# 卡帕头的拼图。学院，第 11 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-10-ec7989b0152f?source=collection_archive---------4----------------------->

新科特林拼图在线！训练大脑，玩得开心；)

![](img/71a1b0ce4db71f48b809200ec9a72b92.png)

## 接口委托和数据类

```
data class Container(
        val name: String,
        private val items: List<Int>
) : List<Int> by items

fun main(args: Array<String>) {
    val (name, items) = Container("Kotlin", listOf(1, 2, 3))
    println("Hello $name, $items")
}
```

作者:[尼古拉·哈夫里科夫](https://github.com/angryziber/kotlin-puzzlers/pull/40/files)

它将显示什么？一些可能性:

a)你好科特林，[1，2，3]
b)你好科特林，1
c)你好 1，2
d)你好科特林，2

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-77)或阅读本文直到最后，查看答案和解释。

## 可空运算符的阶

```
fun main(args: Array<String>) {
    val x: Int? = 2
    val y: Int = 3

    val sum = x?:0 + y

    println(sum)
}
```

作者:托马斯·尼尔德

它将显示什么？一些可能性:

a) 3
b) 5
c) 2
d) 0

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-80)或阅读本文直到最后，查看答案和解释。

## 逆变

```
class Wrapper<in T>val instanceVariableOne : Wrapper<Nothing> = Wrapper<Any>() // A
val instanceVariableTwo : Wrapper<Any> = Wrapper<Nothing>() // B
```

作者:[艾伦·凯恩](https://medium.com/@adcaine)

它将显示什么？一些可能性:

A)A 行和 B 行都编译
B)A 行和 B 行不编译
c)A 行编译；B 行不
d)B 行编译；线 A 没有

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-75)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于“**接口委托和数据类**”，正确答案是:

d)你好科特林，2

为什么？这里有一个解释:

> `private val items`使`Container.component2()`私有。`public List<T>.component2()`在 kotlin-stdlib 中定义为扩展函数。`Container`通过委托实现`List<Int>`，因此使用 stdlib 方法。你可以在问题跟踪上找到:[https://youtrack.jetbrains.com/ssue/KT-24308](https://youtrack.jetbrains.com/ssue/KT-24308)

对于“**可空运算符的顺序**，正确答案是:

c) 2

为什么？这里有一个解释:

> Elvis 运算符的优先级比+低，因此首先计算加号，y 放在 Elvis 运算符的右侧。使用括号来纠正这一点。

[](https://kotlinlang.org/docs/reference/grammar.html#precedence) [## 语法

### 终端符号名称以大写字母开头，例如 SimpleName。非终结符名称以小写字母开始…

kotlinlang.org](https://kotlinlang.org/docs/reference/grammar.html#precedence) 

对于“**逆变**，正确答案是:

c)行 A 编译；B 线没有

为什么？这里有一个解释:

> Wrapper 相对于 t 是相反的变体。Wrapper 是与 t 的子类型相反的子类型。因此，Wrapper 是 Wrapper 的子类型，因为没有什么是 Any 的子类型。A 行编译。它将一个子类型分配给一个超类型。B 行不编译。它将父类型分配给子类型。

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。