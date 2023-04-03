# 程序员词典:扩展接收器与调度接收器

> 原文：<https://blog.kotlin-academy.com/programmer-dictionary-extension-receiver-vs-dispatch-receiver-cd154e57e277?source=collection_archive---------0----------------------->

[接收器的概念之前已经解释过了](https://medium.com/p/b085b1620890)，所以在进一步阅读之前，请确保您知道什么是接收器。

![](img/1b2f13f30456c4051b2ba7bdc1a0208f.png)

**分机接收者**是与科特林分机密切相关的接收者。Extension receiver 表示我们为其定义扩展的对象。要定义扩展，我们必须在它前面加上*接收器类型*(通常是类或接口的名称)。扩展接收器是在其上调用扩展的对象的实例。再来看 *Ball.bounce* 扩展函数:

```
fun String.firstChar(): Char = this.get(this.length — 1)fun String.lastCharacter() {
   println(“Receiver type is ${this.javaClass}, Reciver object is ${this.name}”)
}val ball = Ball(“Golf ball”)
ball.bounce() 
// prints: Receiver type is Ball, Receiver object is Golf ball
```

上述 *Ball* 类的实例可以作为*扩展接收器*在 *bounce* 扩展体内访问。接收器类型指定接收器对象的类型(*球*)，而接收器对象是接收器类的实例(高尔夫球/网球)。请注意，在扩展的情况下，receiver 对象的行为与任何其他对象一样，因此我们不能访问具有私有或受保护可见性的成员。

**调度接收者**是一种特殊的接收者，当扩展被声明为成员时就存在。它表示在中声明扩展的类的实例。假设我们将为 *Person* 类定义 *uploadToBackend* 扩展。请注意，我们将在网络类内部定义此接收器:

```
class Person {
   fun move() {}
}class NetworkRepository {
   fun loadData() {} fun Person.uploadToBackend() {
      move() //method from extension receiver
      loadData() //method from extension dispatch receiver
   }
}
```

在一个 *uploadToBackend* 扩展方法的主体中，我们可以从 *Person* 类和 *NetworkRepository* 类中调用方法。这背后的原因是，我们的*uploadtobacnd*方法有两个接收器。第一个是扩展接收器，表示调用扩展的类的实例。第二个是 dispatch receiver，表示声明扩展的类的实例。注意，在上面的例子中，两个接收者都是隐式接收者——我们在两个不同的对象上调用两个不同的方法，而没有指定接收者。

为了调用 *Person* 类的扩展方法，我们需要*数据库*类型的扩展接收器。

```
class Person {
   fun move() {}
}class Database (al person:Person) {
   fun loadData() {} fun doSomething() {
      person.uploadToBackend(); // We can access extension here
   }

   fun Person.uploadToBackend() {
      move()
      loadData()
   }
}val person = Person()
person.uploadToBackend() // Compilation error
```

当我们在*数据库*类之外调用*uploadtobanekd*方法时，编译器将报告错误(在没有*数据库*类型的接收器的上下文中)。我们可以在*数据库*类中调用这个扩展，因为合适的接收器是可用的。

但是如果两种方法有相同的签名会发生什么呢？让我们将 move 方法添加到数据库类中。

```
class Person {
   fun move() {}
}class NetworkRepository (val person:Person) {
   fun loadData() {}
   fun move() {} fun doSomething() {
      person.uploadToBackend();
   } fun Person.uploadToBackend() {
     // calls method defined in Person class
     move() // calls method defined in NetworkRepository class         
     this@NetworkRepository.move()
 }
}
```

默认情况下，在 Person 类中定义的 move 方法优先，但是如果我们想要访问*网络存储库*中的方法，我们需要使用显式接收器( *this@NetworkRepository* )。

本帖是[科特林程序员词典](https://medium.com/kotlin-academy/kotlin-programmer-dictionary-2cb67fff1fe2)的第九部分。要保持最新的新零件，只需遵循此介质。为了与我的文章保持联系，[在 Twitter 上观察我](https://twitter.com/igorwojda)。这里提到我的关键是 [@IgorWojda](https://twitter.com/igorwojda) 。

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
*   [隐式接收者 vs 显式接收者](/programmer-dictionary-implicit-receiver-vs-explicit-receiver-da638de31f3c)
*   [接收方类型与接收方对象](/programmer-dictionary-receiver-type-vs-receiver-object-575d2705ddd9)
*   [函数类型 vs 函数文字 vs Lambda 表达式 vs 匿名函数](/kotlin-programmer-dictionary-function-type-vs-function-literal-vs-lambda-expression-vs-anonymous-edc97e8873e)
*   [高阶函数](/programmer-dictionary-higher-order-function-9cadb07df94e)
*   [带接收方的函数文字与带接收方的函数类型](/programmer-dictionary-function-literal-with-receiver-vs-function-type-with-receiver-cc21dba0f4ff)
*   [不变性 vs 协方差 vs 方差](/kotlin-generics-variance-modifiers-36b82c7caa39)
*   [事件监听器 vs 事件处理器](/programmer-dictionary-event-listener-vs-event-handler-305c667d0e3c)
*   [代表团 vs 组合](/programmer-dictionary-delegation-vs-composition-3025d9e8ae3d)