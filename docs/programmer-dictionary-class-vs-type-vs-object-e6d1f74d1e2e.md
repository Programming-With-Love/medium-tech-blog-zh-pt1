# 程序员词典:类对类型对对象

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e?source=collection_archive---------1----------------------->

**类**和**类**经常互换使用，但它们具有不同的含义。它们是什么？它们是如何与一个**对象**相关联的？在本文中，我们将探讨它们之间的区别和联系。

![](img/cdb556c9909926cf107024e6790754cf.png)

# 对象与类

对于大多数程序员来说，**对象**和**类**的区别应该是直观的:

> **类**是一个**蓝图或模板**，从其创建**对象**。
> T22 对象是**类**的**实例**。

这里有一个简单的例子:

```
class Aval a = A()
```

在上面的例子中`A`是一个**类**，但是`a`指向一个**对象**。**类**是使用以下定义的:

*   `class`关键词
*   [*对象声明*](http://Object expression vs Object declaration)
*   [*对象表达式*](http://Object expression vs Object declaration)
*   [*伴侣对象*](http://Object expression vs Object declaration)

所有**对象**都是使用这些模板创建的实例。我们可以为一个使用`class`关键字定义的**类**创建多个**对象**，但是对于*对象声明、对象表达式、*和*伴随对象*，只创建一个**对象**。

[![](img/3a56ace9079f060f9ee79ad3ed6b6756.png)](https://kt.academy/workshop)

# 类别与类型

很简单，但是**级**和**型**的区别呢？人们经常在问:“`String`是一个类型还是一个阶层？”。常见的回答是“都是因为**每个类都是一个类型**”。这不是真的。我们需要区分两件不同的事情:

*   分类器，即类和接口
*   类型

分类器生成一组类型。让我们看看这个例子:

```
class Aval a = A()
```

我们可以说在上面的代码片段中我们看到了**类** `A`的定义，但是`a`的**类型**是`A`。因此`A`既是**类**的名称，也是**类**的名称。**类**是一个**对象**的模板，但是具体的**对象**有一个**类型**。其实每个[表情](/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)都有一个**类型**！下面是对**类**和**类型**定义的一个很好的简化:

> 一个 ***类型*** 概括了一组具有相同特征的**对象**的共同特征。我们可以说一个**类型**是一个抽象接口，它指定了一个**对象**如何被使用。
> 
> 一个**类**代表一个**类型**的实现。它是一个具体的数据结构和方法的集合。

我们已经说过，每个分类器都会生成一组**类型**。在大多数情况下，它生成一个单一的**类型**，但是一个单一的**类**生成多个**类型**的例子是一个通用的**类**:

```
class Box<T>
```

`Box`是一个**类**，而`Box<Int>`、`Box<A>`、`Box<List<String>>`是由类属**类** `Box`生成的**类型**。注意，Java 中的原语和数组没有类或接口，但它们是**类型**。

所有这些术语都来自于[范畴理论](https://en.wikipedia.org/wiki/Category_theory)，它高度适应于[理论计算机科学](https://en.wikipedia.org/wiki/Theoretical_computer_science)。如果需要更多，这里的是一篇关于**类型**和**类**区别的很棒的大学文章。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

本帖是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第五部分。要了解最新的新部件，只需关注这个媒体或[在 Twitter 上观察我](https://twitter.com/marcinmoskala)。如果你需要一些帮助，那么请记住[我愿意接受咨询](https://medium.com/@marcinmoskala/ive-just-opened-up-for-online-consultations-640349aaba55)。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [形参 vs 实参，类型形参 vs 类型实参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表达式](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [功能 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [属性对字段](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收方类型与接收方对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 方差](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组合](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)