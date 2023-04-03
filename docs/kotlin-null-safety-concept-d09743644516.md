# 科特林:零安全概念

> 原文：<https://medium.com/globant/kotlin-null-safety-concept-d09743644516?source=collection_archive---------0----------------------->

*   在 Kotlin 中，默认情况下，每种类型都不是可空的。
*   假设在字符串类型的变量名中，我们试图给 null 赋值，它将给出一个错误，因为“ **Null 不能是非 Null 类型字符串**的值”。

```
var name: String = null
```

*   但是如果我们想在代码中使用 null，我们该怎么办呢？
*   所以我们能做的就是使用**？**在数据类型字符串后面，然后我们可以将 null 赋给变量名。

```
var name: String? = null
```

*   那么**弦呢？**是可空的字符串数据类型，可以是字符串，也可以为空。
*   现在，如果我们必须在代码中赋值 null，可能在代码中的某个地方，我们不再确定对象是 null。
*   如果现在我们试图执行变量名称的长度，这将给出一个错误。

```
name.length
```

*   这是因为只有安全(？。)或不可空的断言(！！。)在 String 类型的可空接收器上允许调用？
*   在这种情况下，Kotlin 不会给出空指针异常，但是编译器会认为这是无效的。
*   为此，我们可以遵循两种方法:

```
(1.) name!!.length
```

*   首先是使用(**！！这将会告诉编译器你确定字符串对象不为空。**
*   所以我们不应该使用它，除非你确定对象不能为空。这将导致空指针异常。
*   所以最好的处理方法是使用一个安全的调用操作符"**？**”

```
(2.) name?.length
```

*   不。在这种情况下，如果 var 被赋值为 null，它将给出 null 长度，但如果 var 被赋值为某个字符串值，它将给出它的长度。
*   例如:

```
var name : String? = null
name?.length
//this will give output as null
```

*   但是如果在这种情况下，var 用字符串值赋值比:

```
var name : String? = "Adam"
name?.length
//this will give output as 4
```

*   在下一篇博客中，我们将介绍一些更基本的科特林函数的概念。

快乐阅读:)