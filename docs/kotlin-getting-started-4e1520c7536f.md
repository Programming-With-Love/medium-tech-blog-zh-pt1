# Kotlin —入门第 1 部分

> 原文：<https://medium.easyread.co/kotlin-getting-started-4e1520c7536f?source=collection_archive---------1----------------------->

![](img/7ee6f6d744cc61f21ddd94e2a6be77f2.png)

## 变量游戏攻略！

之前的一篇关于为什么你应该学习 kotlin 的文章[在这里](https://medium.com/@mochadwi/kotlin-getting-started-part-0-c3ac0c3dd960)

欢迎来到 Kotlin，有了这些语言，你编写代码的效率会更高😆

> 嘶，没有分号！

好了，我们开始吧！

# 变量

## 含蓄的

```
var name = “mochadwi” // auto-detect as a String
```

## 明确的

```
var name:String = “mochadwi” // String sets as data type
```

## 最终或常量

```
val name = “mochadwi”name = “new name” // errors
```

## 输入数据— readLine()

```
print(“Enter name: ”)var name = readLine() // wait for user inputs, & cast it to String
```

## 空检查器(类型安全)

```
// with "?" null is allowed, otherwise it compile-errors
var name:String? // if you declare this as global variables// ..... your code
name = "assign different string here"// Throws an error or exception with "!!"
var aget:Int = readLine()!!.toInt()
```

好了，今天到此为止！

下一篇继续这次科特林之旅[这里](https://medium.com/@mochadwi/kotlin-getting-started-part-2-285099c77b32)

***快乐，编码的家伙们！***