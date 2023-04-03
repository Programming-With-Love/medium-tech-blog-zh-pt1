# 科特林程序员词典:语句 vs 表达式

> 原文：<https://blog.kotlin-academy.com/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0?source=collection_archive---------0----------------------->

![](img/ad62fc54509dc4a78b40ce939b89adaf.png)

S **语句**和**表达式**是两个经常被误解的重要术语。让我们从**表达式**术语开始解释。

# 表示

Kotlin 社区中的**表达式**术语通常与 Kotlin *单表达式* [*函数*](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87) 相关联:

```
fun bigger(a: Int, b: Int) = if(a > b) a else b
```

考虑到这一点，我们应该直观地知道什么是**表情**。问题是直觉可能会误导人。让我们从一个常见的定义开始:

> 编程语言中的**表达式**是一个或多个显式值、常数、变量、运算符和[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)的组合，编程语言对其进行解释和计算以产生另一个值。

因此`1 + 1`是一个**表达式**。同`sumOf(1,2,3)`。注意，一个**表达式**可以包含另一个**表达式**。例如，表达式`sumOf(1, 2*3)`包含表达式`2*3`。一个**表达式**是返回值的代码的每一部分。注意，在 Kotlin 中，每个[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)至少返回`Unit`，因此每个[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)调用都是一个表达式。是不是说什么都是一个**表达式**？肯定不是！这里有几个例子:

*   变量声明是**不是表达式** ( `val a = 10`)
*   变量或属性赋值**不是 Kotlin ( `a = 10`)中的表达式**
*   局部[类](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)声明是**而不是表达式** ( `class A {}`)

不是**表达式**，但都是**语句**。

# 声明

让我们从另一个常见的定义开始解释:

> 在计算机编程中，**语句**是命令式编程语言中最小的独立元素，它表达了要执行的一些动作。

我想这是不清楚的，所以我将展示一个例子。让我们看看下面的代码:

```
val bestUser = users.filter { it.passing }
      .maxBy { it.meanScore }
println("${bestUser.name} ${bestUser.surname}")
```

我在这里看到很多**表达式**，但是只有两个**语句**。首先是处理`users`集合并将结果存储在`bestUser`变量中。第二个**语句**显示该用户的姓名。最简单的启发是，**语句**是代码的一部分，在 Java 中以分号结束。在 Kotlin 中，这通常是一行代码，但是我们也可以在一行中编写多个**语句**(如果我们使用分号的话)或者我们可以使用**多行语句**:

```
val bestUsers = users.filter { it.passing }
      .sortedBy { -it.meanScore }
      .take(10)
print("The best students are: "); println(bestUsers.joinToString());
```

下面是 3 个**语句**。第一步是处理`users`以获得最佳用户，第二步是打印“最佳学生是:”，第三步是打印连接到单个字符串的最佳用户。

注意，一个独立的**表达式**也是一个**语句**。如下图`updateUser`所示[功能](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)调用:

```
updateUser(user)
```

这种独立的表达式称为*表达式语句*。

有趣的观察是，在纯函数式编程中没有**语句**。只有**表情**。

# 你为什么需要它？

现在当你理解了什么是一个**语句**和一个**表达式**时，你就应该明白当你在一本书或一篇文章中描述一些代码时，这些术语是多么有用。让我们看一个例子:

```
fun showUsers(users: List<User>?) {
    users ?: return
    val adapters = users.map { UserAdapter(it, ::onUserClicked) }
    list.adapter = UserListAdapter(adapters)
}
```

> 在上面的代码片段中，我们可以看到`showUsers`是如何定义的。在其主体的第一条**语句**中，检查`users` [**参数**](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929) 是否不是`null`。请注意，虽然 Kotlin 中的[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87) [参数](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)是**只读的**，但这样的断言对于[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)的其余部分来说是智能地将`users`强制转换为不可空的。因此，在第二个**语句**中，我们可以直接使用它，无需任何拆包。注意，当我们在表达式`UserAdapter(it, ::onUserClicked)`上将用户映射到适配器时，我们还提供了引用[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87) `onUserClicked`的[参数](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)。在最后一个**语句**中，我们将`list`的适配器指定为新创建的`UserListAdapter`实例。它包括为所有用户创建的适配器。

请注意，**语句**和**表达式**这两个词帮助我们在描述代码时明确我们的意思。

# Java vs in Kotlin 中的表达式是什么？

注意，在 Kotlin 和 Java 中，什么是什么不是一个**表达式**之间有一些基本的区别。所有的 Kotlin [函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)调用都是**表达式**，因为它们至少返回`Unit`。没有定义任何返回类型[的 Java](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e) [函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)的调用不是**表达式**。Kotlin 值赋值(`a = 1`)在 Kotlin 中是**而不是表达式**，而在 Java 中是因为它返回赋值(在 Java 中你可以做`a = b = 2`或`a = 2 * (b = 3)`)。Java 中控制结构(`if`、`switch`)的所有用法都是**不是表达式**，而 Kotlin 允许`if`、`when`、`try`返回值:

```
val bigger = if(a > b) a else bval color = when {
    relax -> GREEN
    studyTime -> YELLOW
    else -> BLUE
}val object = try {
    gson.fromJson(json)
} catch (e: Throwable) {
    null
}
```

我要感谢德米特里·杰梅罗夫的技术验证。这篇文章是科特林程序员词典的第二部分。要了解最新的新部件，只需关注这个媒体或在 Twitter 上观察我。如果你需要一些帮助，记得[我愿意接受咨询](https://medium.com/@marcinmoskala/ive-just-opened-up-for-online-consultations-640349aaba55)。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的其他出版部分:

*   [形参 vs 实参，类型形参 vs 类型实参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [函数 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [字段对属性](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类 vs 类型 vs 对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机对调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收方类型与接收方对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组成](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)