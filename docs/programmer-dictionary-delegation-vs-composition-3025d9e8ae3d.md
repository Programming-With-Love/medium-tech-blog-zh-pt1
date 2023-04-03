# 程序员词典:委托 vs 组合

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d?source=collection_archive---------2----------------------->

当我在 Kotlin 的 [Android 开发中描述](https://www.packtpub.com/application-development/android-development-kotlin) [*类委托*](https://kotlinlang.org/docs/reference/delegation.html#class-delegation) 时，我参考了像《有效的 Java》这样的好书，这些书展示了这种解决方案相对于继承的优势。尽管“有效的 Java”读者可能会感到困惑，因为这本书根本没有使用“委托”这个词。它在描述“作文”的优点。那么它的参数如何引用*类委托*特性呢？

答案是:完全正确。所有给出的例子都同时显示了委托和组合。实际上，*委托模式*需要构图！我们可以说*委托模式*是我们使用组合的一种方式。这些术语来自不同的概念，我们应该从这些概念出发来理解它。

组合术语用于对象建模。它表示对象之间的“有-有”关系。它通常与继承和聚合相对。当`Car`是一个对象时，我们说它组成了`Engine`。从实用的角度来看，我们可以说类中的每一个只读属性都是一个复合的例子。(读写属性很可能是聚合的一个例子。)

授权用于职责方面。当我们需要做某件事的时候，我们可以把这个责任委托给另一个对象。它通常与分派或转发相对。

![](img/f8d3ee6fdbf6515ede5b8dc662102c7b.png)

Image from [Diplo](http://www.diplomacy.edu/)

基于委托思想，*委托模式*诞生了。在*委托模式*中，类将 delegate 作为只读属性，并将一些方法的调用传递给它。这里有一个例子:

```
**class** **Rectangle**(**val** height: Double, **val** width: Double) {
    **fun** area() = height * width
}

**class** **Square(val** a: Double**)** {
    val rectangle = Rectangle(a, a)
    **fun** area() = rectangle.area()
}
```

这是*授权模式*的一个例子。让我们看看如何使用继承来代替:

```
**open class** **Rectangle**(**val** height: Double, **val** width: Double) {
    **fun** area() = height * width
}**class** **Square(val** a: Double**):** Rectangle(a, a)
```

当我们看继承用法时，它可能看起来像更好的实现。首先，它更短更简单。其次，我们有多态行为，允许我们接受`Square`和`Rectangle`:

```
**fun** printSize(shape: Rectangle) {
    *println*(**"Size is ${**shape.area()**}"**)
}

// Usage
*printSize*(Rectangle(10.0, 20.0)) // Size is 200.0
*printSize*(Square(10.0)) // Size is 100.0
```

为了在*委托模式*中有类似的行为，我们需要引入一些对委托和委托的类通用的接口:

```
**interface Shape**() {
    **fun** area(): Double
}**class** **Rectangle**(**val** height: Double, **val** width: Double): Shape {
    **override fun** area() = height * width
}

**class** **Square(val** a: Double**)**: Shape {
    val rectangle = Rectangle(a, a)
    **override fun** area() = rectangle.area()
}**fun** printSize(shape: Shape) {
    *println*(**"Size is ${**shape.area()**}"**)
}

// Usage
*printSize*(Rectangle(10.0, 20.0)) // Size is 200.0
*printSize*(Square(10.0)) // Size is 100.0
```

这就是*委托模式*最常见的实现方式。请注意，在这里，而且在大多数情况下，object 将某个接口中指定的方法委托给一个委托。这就是为什么接口可以用来指定我们想要委托的方法。这就是*类委托*背后的思想，它需要一个接口来指定哪些方法应该被委托:

```
**interface Shape**() {
    **fun** area(): Double
}**class** **Rectangle**(**val** height: Double, **val** width: Double): Shape {
    **override fun** area() = height * width
}

**class** **Square(val** a: Double**)**: Shape by Rectangle(a, a)
```

现在我们有了简短的语法和多态的行为。但是为什么我们应该选择这种实现而不是使用继承的实现呢？我在本讲座开始的和 Kotlin 书中的 [Android Development 中给出了很长的答案。一个简短的回答是:因为从面向对象设计的角度来看，`Square`并不是真正的`Rectangle`。如果我们为`Rectangle`写测试，他们不会通过`Square`！`Square`未延长`Rectangle`。这是它的特例。继承的使用打破了面向对象设计的原则。特别是](https://www.packtpub.com/application-development/android-development-kotlin)[利斯科夫替代原理](https://en.wikipedia.org/wiki/Liskov_substitution_principle)。在`Square`中我们还是可以用`Rectangle`，但是要用复合而不是继承。在这种情况下，*委托模式*，组合的具体实现，完美地匹配了我们的需求。

以下是*类委托*背后的其他论点和理由:

这个帖子是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第十六部分。要了解最新的新部件，只需关注这个媒体或在推特上观察我。如果你需要帮助，记住我随时准备接受咨询。

[![](img/018370a2476e1ce49e6d3299428b4f2a.png)](https://www.kt.academy/#workshops-offer)

## 学到了什么？单击👏说“谢谢！”并帮助他人找到这篇文章。

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 在 medium 上关注我们。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [实参对形参，类型实参对类型形参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表情](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [功能 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [字段对属性](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类 vs 类型 vs 对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收方类型与接收方对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数字面 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)