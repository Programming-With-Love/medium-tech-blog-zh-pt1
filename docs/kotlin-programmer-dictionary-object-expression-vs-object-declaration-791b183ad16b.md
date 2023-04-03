# 科特林程序员词典:对象表达式与对象声明

> 原文：<https://blog.kotlin-academy.com/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b?source=collection_archive---------4----------------------->

![](img/cdd2bb58e698a2a615b88a681c24c179.png)

在[的前一部分](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)中，我们已经描述了什么是[对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)。问题是 Kotlin 引入了一个`object`作为关键字。这在编程社区中引起了一些混乱。因此，我经常听到程序员错误地使用**对象**术语来描述另外两种结构:

*   **对象声明**
*   **对象表达式**

我们分别描述一下。

# 对象声明

下面是一个**对象声明**的例子:

```
object HttpService {
    val api = retrofit.create(Api::class.java) fun post(url: String) = api.post(url)
}
```

**对象声明**是[单例模式](https://en.wikipedia.org/wiki/Singleton_pattern)的实现。在关键字`object`之后，我们实际上定义了类的成员。这个类只能有一个实例，这就是为什么我们使用这个**对象声明**的名称来引用它:

```
HttpService.post("wwww.myurl.com/event")val service: HttpService = HttpService
service.post("wwww.myurl.com/event")
```

尽管名称相同，**对象声明**和由该对象声明创建的对象并不是一回事。他们不应该混淆。关于**对象声明**的更多信息可以在 [Kotlin 参考](https://kotlinlang.org/docs/reference/object-declarations.html#object-declarations)中找到。

## **伴侣对象**

同伴对象是**对象声明**的兄弟。它的工作原理是一样的，但是它从类中取名字:

```
class Connection {
    private constructor() {}

    companion object {
        fun create() = Connection()
    }
}val connection = Connection.create()
```

它的使用方式通常与 Java 中静态字段和属性的使用方式相同，但是尽管它是类的一个实例，但它提供了更多的可能性。我希望在另一篇文章中描述它们。

## 对象表达式

**对象表达式**是创建**对象**的单个实例的结构:

```
val coords = object {
    var x = 10
    var y = 10
}
```

注意，它也生成类型，而我们可以使用这个**对象表达式**中定义的成员:

```
println(coords.x) // Prints: 10
```

它通常被用作 Java 匿名类的替代品:

```
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```

关于对象表达式的更多信息可以在 [Kotlin 参考文献](https://kotlinlang.org/docs/reference/object-declarations.html#object-expressions)中找到。

## 结论

**对象声明**和**对象表达式**都创建了一个单独的对象(虽然**对象声明**创建它很懒散)，但它们不仅仅是对象。它们还指定对象实现并生成类型。这就是为什么它们不应该仅仅作为一个对象被提及。

下面是一些我们如何在句子中使用这些术语的例子:

*   我看到您在`HttpProvider`对象声明中有一个对象
*   您可以使用由对象表达式创建的对象来…

这个帖子是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第六部分。要了解最新的新部件，只需关注此媒体或[在 Twitter 上观察我](https://twitter.com/marcinmoskala)。如果你需要帮助，记得[我愿意接受咨询](https://medium.com/@marcinmoskala/ive-just-opened-up-for-online-consultations-640349aaba55)。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [形参对实参，类型形参对类型实参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表情](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [功能 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [属性对字段](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类 vs 类型 vs 对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收器类型对比接收器对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型对比函数文字对比λ表达式对比匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收器的功能文字与带接收器的功能类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性对协方差对对比方差](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件侦听器与事件处理程序](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [授权 vs .组成](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)