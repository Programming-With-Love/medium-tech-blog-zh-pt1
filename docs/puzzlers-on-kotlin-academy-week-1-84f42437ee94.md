# 卡帕头的拼图。学院，第一周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-1-84f42437ee94?source=collection_archive---------2----------------------->

![](img/c47c9538dab8e8568d2b0070ea080e04.png)

上周，我们开始在 [Kt 上发布谜题。学院门户](http://portal.kotlin-academy.com/#/)。现在我们已经有三个了。这就是为什么我们可以在这里向您展示它们。

我们将从科特林字谜的绝对经典开始。接下来，我们将检查更多的基础知识，最后我们将有原创和更先进的谜题，可能会让你惊讶。玩得开心:)

# 类 Scala 函数

```
fun hello() = {
    println("Hello, World")
}hello()
```

它将显示什么？一些可能性:
a .不编译
b .打印“Hello，World”
c .什么都不做
d .别的什么

答案不是那么简单，但要看到它，你需要查看[这个链接](http://portal.kotlin-academy.com/#/?tag=puzzler-1)或本文的结尾，因为我们不想破坏它。

# 缩进修剪

```
val world = "multiline world"
println("""
    Hello
    \$world
""".trimIndent()
```

它显示什么？一些可能性:
a)

```
Hello
$world
```

b)

```
 Hello
    $world 
```

c)

```
Hello
\multiline world
```

d)无法编译

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/1-hello-multiline-world.kts)

如果你知道`trimIndent`和三重引号字符串，那么答案应该是清楚的。直到你还不够了解它；)如果你不确定，请在本文末尾的处查看答案[。](http://portal.kotlin-academy.com/#/?tag=puzzler-3)

# If-else 链接

```
fun printNumberSign(num: Int) {
    if (num < 0) {
         "negative"
    } else if (num > 0) {
         "positive"
    } else {
         "zero"
    }.let { print(it) }
}printNumberSign(-2)
print(",")
printNumberSign(0)
print(",")
printNumberSign(2)
```

它显示什么？一些可能性:
a)负、零、正
b)负、零、
c)负、正
d)、零、正

作者:[凯文·莫斯特](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/syntax/weirdChaining/WeirdChaining.kts)

这个比较棘手，所以要三思。当你准备好了，在这里或下面勾选答案[。](http://portal.kotlin-academy.com/#/?tag=puzzler-4)

# 答案和解释

对于**“类 Scala 函数”**正确答案是:

**c .什么都没有**

为什么？这里有一个解释:

> hello 是一个函数，它返回使用 lambda 表达式创建的函数。只是返回函数。要打印文本，我们需要调用它:hello()() // Prints: Hello，World

对于**“缩进修剪”**正确答案是:

c)

```
Hello
\multiline world
```

为什么？这里有一个解释:

> 原始字符串由三重引号(`”””`)分隔，不包含转义，并且可以包含换行符和任何其他字符。虽然美元不能在那里使用，即使有转义符\。如果我们想用$，那么我们需要用${'$'}来代替

对于**“If-else 链接”**，正确答案是:

d)、零、正

为什么？这里有一个解释:

> 请记住，`else if`构造实际上只是用一个“单行”`if`调用`else`，这是表达式的其余部分。在 Kotlin 中，函数在 if-else 阻塞之前被解析，所以`.let { print(it) }`只适用于最后的`else if`。所以在这种情况下，不会使用第一个`if`语句的结果，函数会立即返回。为了避免这种情况，您可以将整个`if … else …`放在括号中，然后在其上链接`.let`

下周谜题:

[](/puzzlers-on-kotlin-academy-week-2-5fdabcc0d3bb) [## Kot 上的谜题。学院，第 2 周

### 科特林谜题的新电池时间。本周，我们更多地关注教学和展示令人惊讶的事实…

blog.kotlin-academy.com](/puzzlers-on-kotlin-academy-week-2-5fdabcc0d3bb) 

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/#/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。