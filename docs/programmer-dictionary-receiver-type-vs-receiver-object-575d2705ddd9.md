# 程序员字典:接收器类型与接收器对象

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9?source=collection_archive---------0----------------------->

![](img/a56cad8683f11097c8601adbc7b46372.png)

在前面的部分，我们已经描述了[对象和类型](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)之间的区别。扩展功能中的[接收器也有类似的区别。让我们看看这个例子:](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)

```
fun String.isBlank() = trim() == “”“Marcin”.isBlank()
```

在上面的用法中,`“Marcin”`是传递给扩展函数的对象。在这个函数中，它被称为**接收器对象**。接收器对象是一种不同的参数。函数将扩展类型定义为`String`，所以在这个函数内部`String`被称为**接收器类型**。

请注意，正确的术语是**接收器类型**，而不是接收器类别。这很重要，因为扩展函数实际上是对[类型的扩展，而不是对](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)类的扩展。这是方法和扩展函数的最大区别之一。得益于此，我们可以定义由类生成的可空类型或泛型类型的扩展:

```
fun String?.isNullOrBlank() = this == null || trim() == “”fun List<Long>.sum(): Long {
    var sum = 0L
    for(i in this) sum += i
    return sum
}
```

这篇帖子是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第十部分。要保持最新的新零件，只需遵循此介质。为了与我的文章保持联系，[在 Twitter 上观察我](https://twitter.com/marcinmoskala)。这里提到我的关键是 [@MarcinMoskala](https://twitter.com/marcinmoskala) 。

如果你需要帮助，记得[我随时欢迎咨询](https://medium.com/@marcinmoskala/ive-just-opened-up-for-online-consultations-640349aaba55)。

**喜欢的话记得** **拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [形参 vs 实参，类型形参 vs 类型实参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表达式](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [函数 vs 方法 vs 过程](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [地产 vs 地产](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类 vs 类型 vs 对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机对调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组成](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)