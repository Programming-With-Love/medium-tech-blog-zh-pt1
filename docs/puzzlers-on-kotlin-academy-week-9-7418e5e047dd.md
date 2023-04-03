# 卡帕头的拼图。学院，第 9 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-9-7418e5e047dd?source=collection_archive---------7----------------------->

卡帕头的新谜题时间到了。学院！挑战自我，玩得开心；)

![](img/473dccd7211571c411b4891a02065b42.png)

# 功能名称

```
fun ``() {}
fun ` `() {}
fun `everything works.`() {}
fun `1/1/ is ok; 1/0 is an error`() {}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/x-function-names.kts)

它将显示什么？一些可能性:

a)好的；ok；ok；ok
b)错误；ok；ok 错误
c)错误；ok；错误；错误
d)错误；错误；错误；错误

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-58)或阅读本文直到最后，查看答案和解释。

# 协方差

```
class Wrapper<out T>

val instanceVariableOne : Wrapper<Nothing> = Wrapper<Any>()//Line A
val instanceVariableTwo : Wrapper<Any> = Wrapper<Nothing>()//Line B
```

作者:[艾伦·凯恩](https://medium.com/@adcaine)

它将显示什么？一些可能性:

A)A 行和 B 行都编译
B)A 行和 B 行不编译
c)A 行编译；B 行不
d)B 行编译；线 A 没有

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-74)或阅读这篇文章直到最后，查看答案和解释。

# 接收器战争

```
fun foo() {
    print("Top-level rule")
}

class Foo {
    fun foo() {
        print("Extension receiver rule")
    }
}

class Test {
    fun foo() {
        print("Dispatch receiver rule")
    }

    fun Foo.foo() {
        print("Member extension function rule")
    }

    fun Foo.test() {
        foo()
    }

    fun testFoo() {
        Foo().test()
    }
}

fun main(args: Array<String>) {
    Test().testFoo()
}
```

作者:[马尔钦·莫斯卡拉](http://marcinmoskala.com/)

它将显示什么？一些可能性:

a)顶级规则
b)扩展接收方规则
c)调度接收方规则
d)成员扩展功能规则

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-79)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于“**函数名**”的正确答案是:

c)误差；ok；错误；错误

为什么？这里有一个解释:

> 1:声明必须有一个名称 2:确定 3 和 4:名称包含非法字符“、”和“；”和“/”

对于“**协方差**，正确答案是:

d)B 行编译；线 A 没有

为什么？这里有一个解释:

> 包装器相对于`T`是协变的。`Wrapper<Nothing>`是`Wrapper<Any>`的一个亚型，因为`Nothing`是`Any`的一个亚型。`Wrapper`的子类化方式与`T`的子类化方式相同。B 线很好。A 行不编译。它将作为父类型分配给子类型。

对于“**接收机大战**”的正确答案是:

b)分机接收规则

为什么？这里有一个解释:

> 当我们有扩展接收器(Foo)时，它的方法优先于分派接收器函数(来自同一类的方法)。
> 
> 没有办法打印“成员扩展功能规则”！当方法和扩展函数发生冲突时，方法总是胜出！

你对谜题有自己的想法吗？提交给 [Kt。学院门户](http://portal.kotlin-academy.com/)。

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com/)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

![](img/a067d0a925b721e9a82bf36c2cffd874.png)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。