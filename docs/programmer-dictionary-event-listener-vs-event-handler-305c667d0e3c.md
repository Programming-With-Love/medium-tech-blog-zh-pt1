# 程序员词典:事件侦听器与事件处理程序

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c?source=collection_archive---------4----------------------->

事件侦听器和事件处理程序是两个容易引起混淆的术语。尤其是在科特林，他们之间的界限很模糊。在这里，我试图使它变得简单。

![](img/5cd3501a661bac746898a5da30f1e343.png)

Instance of `DogListener`

侦听器和处理程序的一般定义比它在 Android 中的使用要广泛得多。以下是流行的定义:

> 一个**监听器**监听一个将要被触发的事件。
> 
> **处理程序**负责处理事件。

这可能会令人困惑，因为通常是同一个对象监听事件并处理它们。虽然通常假设当我们将匿名类设置为侦听器时，它的方法是实际的处理程序:

```
cancelImage.setOnClickListener(**object** : View.OnClickListener { //1
    **override fun** onClick(v: View?) { // 2
        dismiss()
    }
})
```

1.  匿名类在这里被用作**监听器**
2.  方法`onClick`在这里是事件**处理程序**

我们可以使用命名类作为监听器:

```
**class** OnCancelSnackListener(
    **val snackbar**: Snackbar
): View.OnClickListener {
    **override fun** onClick(v: View?) {
        **snackbar**.dismiss()
    }
}
```

用法:

```
cancelImage.setOnClickListener(OnCancelSnackListener(**this**))
```

侦听器通常是对象，所以它们通常以`Listener`为后缀。处理程序通常没有后缀，但是它们通常以`on`作为前缀(`onClick`、`onSwipe`等)。).请注意，当我们使用 lambda 表达式设置监听器时，问题会更大:

```
cancelImage.setOnClickListener **{** dismiss() **}**
```

这个 lambda 表达式是什么？倾听者还是处理者？从形式上来说，它指定了事件应该如何处理。侦听器对象是由 Kotlin 在幕后生成的。这是否意味着当我们命名接受处理程序的函数时，我们应该使用`Handler`后缀(比如`setOnClickHandler`)来命名它？一点也不。虽然您可以在一些 JS 库中找到这样的方法，比如 ExJS:

```
handler: function() {
}listeners: {
   'click': function() {
   }
}
```

由于历史原因，Java 中的惯例，我相信大多数其他语言，是使用`Listener`后缀！您可以在 Kotlin stdlib 和 JetBrains Kotlin 代码中找到 Kotlin 也采用了该约定。因此，您可以放心地定义以下功能:

```
fun setOnLoadedListener(handler: ()->Unit) {
   // Code
}fun addOnFlingListener(handler: ()->Unit) {
   // Code
}
```

您还可以定义以下功能:

```
fun setOnLoadedListener(listener: ()->Unit) {
   // Code
}fun addOnFlingListener(listener: ()->Unit) {
   // Code
}
```

请记住，为项目陈述一个约定并尊重它是一个很好的实践。

[![](img/018370a2476e1ce49e6d3299428b4f2a.png)](https://www.kt.academy/#workshops-offer)

这个帖子是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第十五部分。要了解最新的新部件，只需关注此媒体或[在 Twitter 上观察我](https://twitter.com/marcinmoskala)。如果你需要帮助，记住我随时准备接受咨询。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [实参 vs 形参，类型实参 vs 类型形参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表情](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [函数 vs 方法 vs 过程](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [字段对属性](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类 vs 类型 vs 对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收者类型与接收者对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [代表团 vs 组合](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)

![](img/f36a792ac0eb95fc577e6f4125dba956.png)