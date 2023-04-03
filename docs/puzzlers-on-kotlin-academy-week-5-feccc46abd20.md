# 卡帕头的拼图。学院，第 5 周

> 原文：<https://blog.kotlin-academy.com/puzzlers-on-kotlin-academy-week-5-feccc46abd20?source=collection_archive---------0----------------------->

![](img/a4e38c4eb070575e92e02c1bb1cf19c8.png)

时间为新电池的科特林字谜从 Kt。学院！玩得开心；)

## 地图默认值

```
val map = mapOf<Any, Any>()
      .withDefault{ "default" }
println(map["1"])
```

作者:[安东·凯克斯](https://github.com/angryziber/kotlin-puzzlers/tree/master/src/collections/mapDefault)

它将显示什么？一些可能性:

*a)默认
b)无
c)空
d)不会编译*

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-49)或阅读本文直到最后，查看答案和解释。

## 空的

```
fun main(args: Array<String>) {
    val s: String? = null
    if (s?.isEmpty()) println("is empty")
    if (s.isNullOrEmpty()) println("is null or empty")
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/5-null-empty.kts)

它将显示什么？一些可能性:

*a)空是 null 或空
b)空是 null 或空
c)什么都不打印
d)不编译*

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-44)或阅读这篇文章直到最后，查看答案和解释。

## 列表还是不列表

```
// Kotlin/JVM
fun main(args: Array<String>) {
    val x = listOf(1, 2, 3)
    print(x is List<*>)
    print(x is MutableList<*>)
    print(x is java.util.List<*>)
}
```

作者:[德米特里·坎达洛夫](https://github.com/dkandalov/kotlin-puzzlers/blob/master/puzzlers/6-list-or-not.kts)

它将显示什么？一些可能性:

*a)真的假的真的
b)假的假的真的
c)真的真的真的真的
d)真的假的假的假的*

使用[此链接](http://portal.kotlin-academy.com/#/?tag=puzzler-45)或阅读这篇文章直到最后，查看答案和解释。

# 答案和解释

对于“**地图默认**”的正确答案是:

*c)空值*

为什么？这里有一个解释:

> Map.withDefault()具有误导性，只能与委托[https://youtrack.jetbrains.com/issue/KT-11851](https://youtrack.jetbrains.com/issue/KT-11851)一起使用

对于“ **Null 空**”的正确答案是:

*d)不编译*

为什么？这里有一个解释:

> 当`s == null`时，表达式`s?.isEmpty()`返回 null，所以它的类型是`Boolean?`。因此我们不能在`if`中直接使用它。修复:

```
fun main(args: Array) {
   val s: String? = null if (s?.isEmpty() == true) 
   println(“is empty”) 
   if (s.isNullOrEmpty()) println(“is null or empty”) 
}
```

对于“**列出还是不列出**”的正确答案是:

*c) true true true*

为什么？这里有一个解释:

> 在引擎盖下，`listOf`返回 Java `ArrayList`，也就是`MutableList`和`java.util.List`。

你对谜题有自己的想法吗？提交给 [Kt。学院门户。](http://portal.kotlin-academy.com)

你希望未来有更多的谜题吗？轨道 [Kt。学院门户](http://portal.kotlin-academy.com)或[订阅我们的邮件列表](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。

[![](img/a067d0a925b721e9a82bf36c2cffd874.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。