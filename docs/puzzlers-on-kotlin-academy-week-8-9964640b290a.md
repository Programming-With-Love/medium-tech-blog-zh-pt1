# 卡帕头的拼图。学院，第 8 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-8-9964640b290a?source=collection_archive---------4----------------------->

是时候买一套新的 Kt kot Lin 拼图了。学院！玩得开心；)

![](img/022cefed4e48c1e28b686d73cdaa1d03.png)

## 子应用

```
open class Node(val name: String) {
    fun lookup() = "lookup in: $name"
}

class Example : Node("container") {
    fun createChild(name: String): Node? = Node(name)

    val child1 = createChild("child1")?.apply {
        println("child1 ${lookup()}")
    }
    val child2 = createChild("child2").apply {
        println("child2 ${lookup()}")
    }
}

fun main(args: Array<String>) {
    Example()
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/x-child-apply.kts)

它将显示什么？一些可能性:

a)在 child1 中查找 child 1；子 2 查找在:子 2
B)子 1 查找在:子 1；在容器中查找 child2)在容器中查找 child1child2 查找 in: child2
D)以上都不是

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-57)或阅读这篇文章直到最后，查看答案和解释。

## 负数

```
fun main(args: Array<String>) {
    print(-1.inc())
    print(", ")
    print(1 + -(1))
}
```

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com)

它将显示什么？一些可能性:

a) 0，0
b)不会编译到第 4 行
c) 0，2
d) -2，0

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-55)或阅读本文直到最后，查看答案和解释。

## 复制

```
data class Container(val list: MutableList<String>)

fun main(args: Array<String>) {
    val list = mutableListOf("one", "two")
    val c1 = Container(list)
    val c2 = c1.copy()
    list += "oops"
    println(c2.list.joinToString())
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/tree/master/src/data)

它将显示什么？一些可能性:

a)一个，两个
b)一个，两个，哎呀
c)不支持操作异常
d)将不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-51)或阅读这篇文章直到最后，查看答案和解释。

## 子类型和泛型:列表

```
sealed class LinkedList<T>

data class Node<T>(
    val payload : T, 
    var next : LinkedList<T> = EmptyList
) : LinkedList<T>()

object EmptyList : LinkedList<Nothing>()
```

作者:[艾伦·凯恩](https://medium.com/@adcaine)

它将显示什么？一些可能性:

a)看起来很棒。代码将按原样编译。
b)密封类不能有类型参数
c)写密封类`LinkedList<in T>`d)写密封类`LinkedList<out T>`

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-76)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于“**子申请**”的正确答案是:

b)在 child1 中查找 child 1；在容器中查找 child2

为什么？这里有一个解释:

> `createChild`返回可空对象，所以在`child2`的`apply`中，我们的扩展接收者(上下文)是`Node?`。我们不能不拆包就直接打电话给`lookup`。如果要做，就要用`this?.lookup()`。因为我们没有，编译器搜索它可以使用的`lookup`，并在分派接收器中找到它(`Example`上下文)。

对于“**负数**，正确答案是:

d) -2，0

为什么？这里有一个解释:

> 在这两种情况下，我们对类型`Int`使用`unaryMinus`操作。当您键入`-1`时，它与`1.unaryMinus()`相同。这就是`1 + -(1)`正常工作的原因。`-1.inc()`返回-2，因为`inc`用在运算符之前。这个表达式相当于`1.inc().unaryMinus()`。要修复它，您应该使用括号`(-1).inc()`。

对于“**复制**”的正确答案是:

b)一，二，哎呀

为什么？这里有一个解释:

> 数据类的 copy()方法进行浅层克隆，即只复制对字段的引用。使数据类不可变以避免这个问题。

对于“**子类型和通用类型:列表**”，正确答案是:

d)编写密封类`LinkedList<out T>`

为什么？这里有一个解释:

> 按照目前的写法，代码不会编译。`Node<T>`的下一个属性不能有默认值`EmptyList`。`LinkedList<T>`相对于`T`是不变的。`EmptyList`是一个`LinkedList<Nothing>`。`LinkedList<Nothing>`是一个与`T`类型无关的独特类型。
> 
> 相反，使 LinkedList 相对于`T`协变。在这种情况下，`LinkedList<Nothing>`是每个`LinkedList`的子类型，因为`Nothing`是所有 Kotlin 类型的子类型。
> 
> 密封类可以有类型参数。Kotlin 对象不能有类型参数。所以，答案 b)是错的。
> 
> 答案 c)不对。如果 LinkedList 相对于 T 是反变的，那么 LinkedList 将是所有 linked list 的超类型。相反，LinkedList 必须是所有 linked list 的子类型。

了解更多:[https://blog . kot Lin-academy . com/kot Lins-nothing-its-useful-in-generics-5076 a457 f 7](/kotlins-nothing-its-usefulness-in-generics-5076a6a457f7)

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。