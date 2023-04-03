# 程序员词典:隐式接收者与显式接收者

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c?source=collection_archive---------3----------------------->

之前已经解释过[接收器的概念](https://medium.com/p/b085b1620890)，所以在进一步阅读之前，确保你知道什么是接收器。

![](img/7f80e740cf35649b41781eb7ddc10003.png)

这两个术语可能看起来有点陌生，但是您可能已经多次使用过接收器了。许多开发人员可能不知道实际名称，但他们肯定熟悉这些概念。让我们直接看一个例子。

```
val guitar = Guitar()
guitar.playTone()
```

在上面的例子中*吉他*是**显式接收器**。但是，有些情况下，我们可以在不指定接收者的情况下访问成员:

```
class Guitar {
  fun playTone() { } fun playSong() {
     playTone()
  }
}
```

从类内部，我们可以简单地通过使用成员名( *playTone* 方法)来访问这个类中定义的成员。在幕后，receiver 对象(类的实例)存储对这些成员的引用。在上面的例子中，我们正在使用一个**隐式接收器**调用 *playTone* 方法(我们正在隐式访问接收器，而没有指定接收器名称)。我们也可以显式地引用类内部的接收者，我们将在下面的例子中通过使用 *this* 关键字来这样做:

```
class Guitar {
   fun playTone() { } fun playSong() {
      this.playTone() // explicit receiver
      playTone() // implicit receiver
   }
}
```

在我们的示例中，receiver 是 *Guitar* 类的实例，在该类中声明了方法。在这种情况下，两个调用是等效的，但是我们使用了一个显式的接收者，而不是隐式的。

> 如果我们在访问一个对象的成员时显式地引用它，我们说**接收者是显式的**。如果我们在访问对象的成员时不显式地引用对象，我们说**接收者是隐式的**。

通常我们不使用`this`作为显式的接收者，因为我们不想产生额外的样板文件。然而，很少有显式接收器很方便的情况。最简单的例子是访问与方法参数同名的字段:

```
class Person() {
   private var age: Int = 18 fun setName(age: Int) {
      this.age = age
   }
}
```

我们有两个同名的变量(类中声明的*年龄*字段和方法头中定义的*年龄*参数)。没有接收方的变量总是引用最内部的封闭范围(在这种情况下是方法体)。方法参数声明隐藏了字段声明。如果不使用显式接收器( *this* )，我们将无法访问 *age* 字段，因为在此范围内存在另一个同名的变量声明(方法参数变量隐藏了一个字段)。

当我们想要访问在父类中实现的方法而不是在子类中覆盖的方法时，我们也使用显式的接收器( *super* 关键字)。让我们考虑这个例子，其中两个接口用相同的名称和默认实现定义成员:

```
interface A {
   fun doSomething() {
      println("A is doing something")
   }
}interface B {
   fun doSomething() {
      println("B is doing something")
   }
}class Test : A, B {
   override fun doSomething() {
      doSomething() // infinitely recursive call
   }
}
```

注意，默认情况下，*测试*类中定义的 *doSomething* 方法将被调用，导致无限递归调用。为了解决这个问题，我们需要使用显式接收器( *super* )来告诉 Kotlin 编译器我们想要调用的方法。两个接口都默认实现了 *doSomething* 方法，所以我们还需要用接口名来限定显式接收者:

```
class Test : A, B {
   override fun doSomething() {
      super<A>.doSomething() // explicit receiver
      super<B>.doSomething() // explicit receiver
   }
}
```

总结一下。如果调用一个方法而没有显式地命名一个接收者对象，我们说接收者是隐式的，否则我们说接收者是显式的。

我要感谢[德米特里·杰梅罗夫](https://twitter.com/intelliyole)的校对。这篇帖子是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第八部分。要保持最新的新零件，只需遵循此介质。为了与我的文章保持联系，[在 Twitter 上观察我](https://twitter.com/igorwojda)。这里提到我的关键是 [@IgorWojda](https://twitter.com/igorwojda) 。

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

以下是《科特林程序员词典》的其他部分:

*   [形参 vs 实参，类型形参 vs 类型实参](https://medium.com/kotlin-academy/programmer-dictionary-parameter-vs-argument-type-parameter-vs-type-argument-b965d2cc6929)
*   [语句 vs 表达式](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-statement-vs-expression-e6743ba1aaa0)
*   [功能 vs 方法 vs 程序](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-function-vs-method-vs-procedure-c0216642ee87)
*   [属性对字段](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)
*   [类对类型对对象](/programmer-dictionary-class-vs-type-vs-object-e6d1f74d1e2e)
*   [对象表达式 vs 对象声明](/kotlin-programmer-dictionary-object-expression-vs-object-declaration-791b183ad16b)
*   [接收器](/programmer-dictionary-receiver-b085b1620890)
*   [分机接收机 vs 调度接收机](/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277)
*   [接收者类型与接收者对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 逆变](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组成](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)