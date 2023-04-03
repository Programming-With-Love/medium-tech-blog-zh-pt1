# 程序员词典:接收器

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-receiver-b085b1620890?source=collection_archive---------5----------------------->

![](img/27464c2b8b01da3635b3408ab8c65382.png)

术语“接收者”和“发送者”来自发送信件的隐喻。我们可以把发送者看作是调用方法的对象(邮递员发送电子邮件)，接收者是方法被调用的对象(收到信的人)。让我们看一个简单的例子:

```
class Car {
    private val engine = Engine()fun startEngine() {
        engine.start()
    }
}
```

在上面的例子中，*引擎*类的实例是**接收者**，而*汽车*类的实例是**发送者**。

> 在底层，当一个对象接收到一个消息时，它决定请求什么方法，并将控制权传递给该方法

在 Kotlin 中，当讨论带有接收器和扩展的 Lambdas 时，我们主要讨论接收器。接收器是一个通用术语，但也有一些与接收器相关的更具体的术语:

*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机对调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)

Kotlin 中的任何代码块都可能有一个或多个接收者。它使接收器的功能和属性在该代码块中可用，而无需对其进行限定。

# 参考

[发送者/接收者对象](http://esug.org/data/Old/ibm/tutorial/OOP.HTML)

这个帖子是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第七部分。要保持最新的新零件，只需遵循此介质。为了与我的文章保持联系，[在 Twitter 上观察我](https://twitter.com/igorwojda)。这里提到我的关键是 [@IgorWojda](https://twitter.com/igorwojda) 。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [形参 vs 实参，类型形参 vs 类型实参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表情](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [功能 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [属性对字段](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类 vs 类型 vs 对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收器类型与接收器对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组成](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)