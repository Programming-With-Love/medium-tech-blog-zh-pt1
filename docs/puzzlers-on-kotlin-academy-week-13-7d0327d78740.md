# 卡帕头的拼图。学院，第 13 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-13-7d0327d78740?source=collection_archive---------3----------------------->

提高你的 Kotlin 解决难题的技能！

![](img/b9360b8e801d8616b01d9e254b8246c2.png)

## 你好布洛克斯

```
fun hello(block: String.() -> Unit) {
    "Hello1".block()
    block("Hello2")
}

fun main(args: Array<String>) {
    hello { print(this) }
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/helloBlock/HelloBlock.kts)

它将显示什么？一些可能性:

a)hello 1
b)hello 2
c)hello 1 hello 2
d)不会编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-65)或阅读这篇文章直到最后，查看答案和解释。

## 我就是这个

```
data class IAm(var foo: String) {
  fun hello() = foo.apply {
    return this
  }
}

fun main(args: Array<String>) {
    println(IAm("bar").hello())
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/iAmThis/IAmThis.kts)

它将显示什么？一些可能性:

a)IAm
b)IAm(foo = bar)
c)bar
d)不会编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-66)或阅读本文直到最后，查看答案和解释。

## 过度伸展

```
operator fun String.invoke(x: () -> String) = this + x()
fun String.z() = "!$this"
fun String.toString() = "$this!"

fun main(args: Array<String>) {
    println("x"{"y"}.z())
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/overextension/Overextension.kts)

它将显示什么？一些可能性:

答)！x
b)！xy
c)！xy！
d)不会编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-67)或阅读本文直到最后，查看答案和解释。

# 答案和解释

对于“**你好街区**”的正确答案是:

c) Hello1Hello2

为什么？这里有一个解释:

> 扩展函数被编译成以 receiver 作为第一个参数的函数，这同样适用于扩展 lambds。出于某种原因，只有扩展 lambdas 可以用 receiver 作为参数调用。

对于“**我就是这个**”正确答案是:

c)酒吧

为什么？这里有一个解释:

> 这在扩展函数中可能是偷偷摸摸的，从 lambda 返回(如果允许的话)会影响外部函数，函数返回类型推断允许遗漏错误，但编译器可能会在使用时捕捉到它

对于“**过度伸展**”的正确答案是:

b)！正常男性染色体组型

为什么？这里有一个解释:

> 实方法优先于扩展

*   当新方法出现时，源代码级的兼容性可能会被破坏
*   已经编译的代码仍将按预期工作 toString()可以声明扩展，但不能被调用*会产生'扩展被隐藏'警告

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？访问[我们的网站](https://www.kt.academy/)，看看我们能为您做些什么。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。