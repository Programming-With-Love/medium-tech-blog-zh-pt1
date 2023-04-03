# 卡帕头的拼图。学院，第 14 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-14-3a377e75672e?source=collection_archive---------1----------------------->

每周一套的 Kotlin 字谜游戏正等着你——挑战自我！

![](img/7958d286c607a0e8e170361c4a82e6ab.png)

## 懒惰代表

```
class Lazy {
    var x = 0
    val y by lazy { 1/x }

    fun hello() {
        try {
            print(y)
        } catch (e: Exception) {
            x = 1
            print(y)
        }
    }
}

fun main(args: Array<String>) {
    Lazy().hello()
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/delegates/lazy/Lazy.kts)

它将显示什么？一些可能性:

a) 0
b) 1
c)南
d)算法异常

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-62)或阅读这篇文章直到最后，查看答案和解释。

## 偷偷返回

```
fun numbers(list: List<Int>) {
    list.forEach {
        if (it > 2) return
        print(it)
    }
    print("ok")
}

fun main(args: Array<String>) {
    numbers(listOf(1, 2, 3))
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/sneakyReturn/SneakyReturn.kts)

它将显示什么？一些可能性:

a)123 ok
b)12ok
c)12
d)无限循环

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-68)或阅读本文直到最后，查看答案和解释。

## 两辆兰博达

```
typealias L = (String) -> Unit

fun foo(one: L = {}, two: L = {}) {
    one("one")
    two("two")
}

fun main(args: Array<String>) {
    foo { print(it) }
    foo({ print(it) })
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/twoLambdas/twoLambdas.kts)

它将显示什么？一些可能性:

a)一个
b)两个两个
c)一个两个
d)以上都不是

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-69)或阅读本文直到最后，查看答案和解释。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

# 答案和解释

对于“**偷懒放权**”的正确答案是:

b) 1

为什么？这里有一个解释:

> 惰性委托可以被多次调用，直到它实际返回一个值。委托异常被传播到 getter 之外

对于“**偷偷返回**”的正确答案是:

c) 12

为什么？这里有一个解释:

> 在 Kotlin 中，return 从最近的函数声明返回与 Java 不同，您不能从 lambda 返回——lambda 返回一个表达式在 lambda 中使用 return 只可能在内联函数中使用这一开始会令人困惑，但后来变得非常简单

更多请看这里:[https://github . com/angryziber/kot Lin-puzzlers/blob/master/src/functions/sneaky return/keyword hell . kt](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/sneakyReturn/KeywordHell.kt)

对于“**二λs**”的正确答案是:

d)以上都不是(二选一)

为什么？这里有一个解释:

> 括号中的 Lambda 作为第一个参数应用，没有括号的 Lambda 被定义为作为最后一个参数应用

*   这对 DSL 来说非常好
*   但是当与缺省参数结合使用时可能会令人困惑。不要将许多 lambdas 作为参数，如果您仍然这样做，请避免使用缺省值

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。