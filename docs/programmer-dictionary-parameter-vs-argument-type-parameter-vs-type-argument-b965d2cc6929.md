# 程序员字典:形参与实参，类型形参与类型实参

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929?source=collection_archive---------2----------------------->

![](img/698c7ab4b631edc22704366682a9e0a3.png)

**参数**和**自变量**经常被混淆，但它们是完全不同的概念。我们来讨论一下它们是什么，有什么区别。我们也会明白什么是**类型参数**和**类型实参**。

# 参数 vs 变元

**参数**是在[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)定义中定义的变量，而**自变量**是传递给[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)的实际值。为了理解其中的区别，我们先来看一个例子[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)及其用法:

```
fun randomString(length: Int): String {
    // ....
}randomString(10)
```

在这个例子中`length`是一个**参数**，而`10`(用于[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)调用)是一个**参数**。以下是常见的定义:

> **参数**是在[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)声明中定义的变量。**参数**是传递给[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)的该变量的实际值。

对话示例:

*   `randomString` [功能](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)抛出错误。你传递给`length` **参数**的**参数**是什么？
*   是`-1`
*   这就是原因。`randomString`没有为`length` **参数**的负值做准备！

# 类型形参与类型实参

形参和实参的区别是通用的，它适用于所有类型的[函数](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87) : [方法](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)，构造函数等。它也适用于通用的措辞。让我们讨论一下示例泛型[类](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e):

```
class Box<T>val a: Box<Int> = Box()
```

这里的`Box`是泛型类，定义了`T` **类型参数**。在使用中，我们将`Int`指定为**类型实参**(此处[见](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)为什么是类型，而不是类)。常见定义如下:

> ***类型参数*** 是泛型中声明的类型的蓝图或占位符。 ***类型参数*** 是用于参数化泛型的实际类型。

感谢[德米特里·杰梅罗夫](https://twitter.com/intelliyole)的打样。

这篇文章是科特林程序员词典的第一部分。要了解最新的新部件，只需关注这个媒体或在 Twitter 上观察我。如果你需要一些帮助，记得[我愿意接受咨询](https://medium.com/@marcinmoskala/ive-just-opened-up-for-online-consultations-640349aaba55)。

喜欢的话记得鼓掌。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的其他出版部分:

*   [语句 vs 表达式](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [函数 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [字段对属性](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类对类型对对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收器类型与接收器对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组成](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)