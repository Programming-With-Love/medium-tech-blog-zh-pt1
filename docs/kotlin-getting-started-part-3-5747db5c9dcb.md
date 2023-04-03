# 科特林—入门第 3 部分

> 原文：<https://medium.easyread.co/kotlin-getting-started-part-3-5747db5c9dcb?source=collection_archive---------3----------------------->

![](img/7ee6f6d744cc61f21ddd94e2a6be77f2.png)

Kotlin Academy Copyright

## 决定来了！

该系列由以下部分组成:

1.  [第一部分](https://medium.com/@mochadwi/kotlin-getting-started-4e1520c7536f)
2.  [第二部](https://medium.com/@mochadwi/kotlin-getting-started-part-2-285099c77b32?source=friends_link&sk=f2b3f607a0667375e8f6c454dc3495fd)
3.  这篇文章

是的，现在到了做决定的时候了。你站在光明面还是黑暗面？

像任何编程语言一样，在 Kotlin 中，if 语句仍然是相同的，但是增加了一些功能。

# 决策

## 逻辑语句

```
val age = 17
println(16 > age && 16 < age) // meh
println(!(16 > age)) // meh
println(age > 18 && age < 58) // meh
println(age in 19..57) // nice, range check (inclusive end)
```

## 何时(无开关箱)

```
when (age) {
    17 -> println(true)
    else -> println(false)
} // mehval msg = when (age) {
    17 -> "true"
    else -> "false"
} // hm, able to return value, same with "if"when {
    age > 17 -> println(true)
    "17" == "$age" -> println(true)
    else -> println(false)
} // hm, able to check for different data type of caseswhen {
    age in 18..57 -> println(true)
    else -> println(false)
} // support range checkval msg = when (age) {
    17 -> {
       // support multiple line statement
       "skip here, to prepare something"
       "true" // return this
    }
    else -> "false"
} // hm, return the last statementwhen (age) {
    17, 18 -> "true"
    else -> "false"
} // hm, support "," (or) operator inside condition cases
```

好了，今天到此为止！

***快乐，编码的家伙们！！！***