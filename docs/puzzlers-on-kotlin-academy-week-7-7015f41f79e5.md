# 卡帕头的拼图。学院，第 7 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-7-7015f41f79e5?source=collection_archive---------3----------------------->

没有一种编程语言是完美的。因此 Kotlin 也有它的问题(多亏了它运行的 JVM)。本周我们展示了 Kotlin 中的两个错误，每个高级程序员都应该知道。尤其是“覆盖父级中使用的属性”是一个非常重要的难题。

知道和理解这样的情况是很重要的，因为否则，你可能会在你的项目中碰到一些这样的情况。这就是为什么如果你想成为一名 Kotlin 开发者，字谜游戏是一个重要的知识来源。要始终保持最新，请跟踪 [Kt。学院门户](http://portal.kotlin-academy.com)并登录[我们的简讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/074f8355d7ad9ca42e59616cc24acde2.png)

# 整理

```
fun main(args: Array<String>) {
    val list = arrayListOf(1, 5, 3, 2, 4)
    val sortedList = list.sort()
    print(sortedList)
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/tree/master/src/collections/sorting)

它将显示什么？一些可能性:

a) [1，5，3，2，4]
b) [1，2，3，4，5]
c)科特林。单元
d)不会编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-50)或阅读本文直到最后，查看答案和解释。

# 集合相等

```
fun main(args: Array<String>) {
    println(listOf(1, 2, 3) == listOf(1, 2, 3))
    println(listOf(1, 2, 3).asSequence() == listOf(1, 2, 3).asSequence())
    println(sequenceOf(1, 2, 3) == sequenceOf(1, 2, 3))
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/7-collection-equality.kts)

它将显示什么？一些可能性:

a)真实；真实；true
b)true；真实；false
c)true；假的；true
d)true；假的；false
e)false；假的；错误的

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-56)或阅读本文直到最后，查看答案和解释。

# 好孩子有很多名字

```
open class C {
    open fun sum(x: Int = 1, y: Int = 2): Int = x + y
}

class D : C() {
    override fun sum(y: Int, x: Int): Int = super.sum(x, y)
}

fun main(args: Array<String>) {
    val d: D = D()
    val c: C = d
    print(c.sum(x = 0))
    print(d.sum(x = 0))
}
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/functions/goodChildHasManyNames/GoodChildHasManyNames.kts)

它将显示什么？一些可能性:

a) 22
b) 11
c) 21
d)将不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-64)或阅读本文直到最后，查看答案和解释。

# 覆盖父级中使用的属性

```
open class Parent(open val a: String) {
    init { print(a) }
}

class Children(override val a: String): Parent(a)

fun main(args: Array<String>) {
    Children("abc")
}
```

它将显示什么？一些可能性:

a) abc
b)未解析的引用:a
c)无，它不会编译
d)空

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-53)或阅读本文直到最后，查看答案和解释。

# 循环宾语结构

```
open class A(val x: Any?)

object B : A(C)
object C : A(B)

fun main(args: Array<String>) {
    println(B.x)
    println(C.x)
}
```

作者:[黑川浩](https://github.com/angryziber/kotlin-puzzlers/blob/master/src/types/cyclicObjectConstructions/cyclicObjectConstructions.kts)

它将显示什么？一些可能性:

a)空；null
b)C @ 1544 BF 85；null
c)异常初始化错误
d)将不编译

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-70)或阅读本文直到最后，查看答案和解释。

# 答案和解释

对于**排序**，正确答案是:

c)科特林。单位

c)科特林。单位

c)科特林。单位

为什么？这里有一个解释:

> 有两个函数 sort()——对可变集合进行排序——复制集合(并返回排序后的结果)

对于“**集合相等**”的正确答案是:

d)真实；假的；错误的

为什么？这里有一个解释:

> 相等适用于列表，因为`equals`是为在幕后实现的`ArrayList`而实现的。`Sequence`没有`equals`方法，所以使用默认的方法来比较两个对象的地址。

对于“**好孩子有很多名字**”正确答案是:

c) 21

为什么？这里有一个解释:

*   开放函数是多态的，由 JVM 在运行时解析
*   命名参数是静态的，在编译时解析
*   重写时最好不要重新排列参数名，至少有一个警告！

对于“**覆盖父级**中使用的属性”，正确答案是:

d)空

为什么？这里有一个解释:

> 这是 Kotlin 实现中最大的已知问题。要理解它，请参见代码的简化 Java 表示:

```
public static class Parent {
    private final String a;public String getA() {
        return this.a;
    }Parent(String a) {
        super();
        this.a = a;
        System.out.print(this.getA());
    }
}public static final class B extends Parent {
    private final String a;
    public String getA() {
        return this.a;
    } B(String a) {
        super(a);
        this.a = a;
    }
}
```

> 正如你所看到的，为了得到 a，我们使用了引用`a`的`getA`方法。唯一的问题是它在`Child`中被覆盖，所以它实际上从`Child`中引用了`a`，而此时还没有设置。这是因为 parent 总是首先被初始化。

在 Kotlin/JS 中，我们也有类似的问题，因此我们有了`undefined`。

对于“**循环宾语结构**，正确答案是:

b)C @ 1544 BF 85；空

为什么？这里有一个解释:

> 单例对象的初始化顺序是通过尝试根据它们的依赖关系对它们进行拓扑排序来确定的。在初始化循环的情况下，完整的拓扑排序是不可能的，并且有可能观察到循环中涉及的对象的值为空。如果的构造函数采用不可为空的参数，如 open class A(val x: Any)，它将引发异常。参见[http://jetbrains.github.io/kotlin-spec/#_singleton_objects](http://jetbrains.github.io/kotlin-spec/#_singleton_objects)

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。