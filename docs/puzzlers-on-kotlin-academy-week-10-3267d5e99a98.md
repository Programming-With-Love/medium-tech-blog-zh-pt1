# 卡帕头的拼图。学院，第 10 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-10-3267d5e99a98?source=collection_archive---------2----------------------->

你准备好接受新的 Kotlin 拼图游戏了吗？他们在这里；)

![](img/e324b4a441dc9909ce3713990ea24999.png)

## 整数加正

```
fun main(args: Array<String>) {
    var i = 0
    println(i.inc())
    println(i.inc())

    var j = 0
    println(j++)
    println(++j)
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/x-int-plusplus.kts)

它将显示什么？一些可能性:

a) 0，1，0，1
b) 0，1，0，2
c) 1，1，0，2
d) 1，2，0，1

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-59)或阅读这篇文章直到最后，查看答案和解释。

## 函数文本中的返回

```
fun f1() {
    (1..4).forEach {
        if (it == 2) return
        print(it)
    }
}

fun f2() {
    (1..4).forEach(fun(it) {
        if (it == 2) return
        print(it)
    })
}

fun main(args: Array<String>) {
    f1()
    f2()
}
```

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

它将显示什么？一些可能性:

a)134134
b)1134
c)1341
d)不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-60)或阅读这篇文章直到最后，查看答案和解释。

## 带有标签的 WTF

```
fun main(args: Array<String>) {
    val j = wtf@{ n: Int -> wtf@(wtf@n + wtf@2) }(10)
    print(j)
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/x-return-of-label.kts)

它将显示什么？一些可能性:

a)不会编译
b) 10
c) 2
d) 12

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-61)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于“ **Int plus-plus** ”，正确答案是:

c) 1，1，0，2

为什么？这里有一个解释:

> 答案从 C++开始就在意料之中。前缀位置的++运算符(++j)增加数字并返回新值，如果是后缀位置，它也增加属性但返回以前的值。
> 
> 棘手的部分是引用 Kotlin 函数`inc`的前缀和后缀。这就是为什么不太清楚的原因。虽然 Kotlin 对前缀位置有特殊的支持，这在文档中有描述:[https://kot linlang . org/docs/reference/operator-overloading . html # increments-and-decrements](https://kotlinlang.org/docs/reference/operator-overloading.html#increments-and-decrements)

对于函数文字中的“**Return**”，正确答案是:

b) 1134

为什么？这里有一个解释:

> 当我们想在 lambda 表达式中使用`return`时，我们需要使用类似`return@forEach`的标签。否则不予受理。这里由于 for-each 是内联函数，允许非局部返回，所以这个`return`完成了`f1`函数。

对于带有标签的“ **WTF”，正确答案是:**

d) 12

为什么？这里有一个解释:

> 它只是一个带有许多标签的普通 lambda 表达式

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。