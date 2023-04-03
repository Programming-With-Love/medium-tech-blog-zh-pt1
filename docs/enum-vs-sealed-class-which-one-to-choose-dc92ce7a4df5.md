# 枚举与密封类——选择哪一个？

> 原文：<https://blog.kotlin-academy.com/enum-vs-sealed-class-which-one-to-choose-dc92ce7a4df5?source=collection_archive---------0----------------------->

![](img/99c7fefd737af88c8ec5646f91b205ca.png)

**更新:** [**这里的**](https://kt.academy/article/ek-enum) **是本文的最新版本。**

TL；DR:enum 有类似于`valueOf`、`values`或`enumValues`的支持函数，这使得它们更容易迭代或序列化。就像类一样，它们可以有自定义方法或保存数据，但每个枚举值总是有一个。它们非常适合表示一组常量值。密封类可以保存特定于实例的数据。它们非常适合用一组具体的子类来表示消息或类。

[![](img/3a56ace9079f060f9ee79ad3ed6b6756.png)](https://kt.academy/workshop)

# 列举型别

当我们必须表示一组恒定的可能选项时，一个经典的选择是使用 Enum。例如，如果我们的网站提供了一组具体的支付方式，我们可以使用下面的 enum 类在我们的服务中表示它们:

枚举可以保存始终特定于项目的值:

[Playground](https://play.kotlinlang.org/#eyJ2ZXJzaW9uIjoiMS4zLjYxIiwicGxhdGZvcm0iOiJqYXZhIiwiYXJncyI6IiIsImpzQ29kZSI6IiIsIm5vbmVNYXJrZXJzIjp0cnVlLCJ0aGVtZSI6ImlkZWEiLCJjb2RlIjoiaW1wb3J0IGphdmEubWF0aC5CaWdEZWNpbWFsXG5cbmVudW0gY2xhc3MgUGF5bWVudE9wdGlvbiB7XG4gICAgQ0FTSCxcbiAgICBDQVJELFxuICAgIFRSQU5TRkVSO1xuXG4gICAgdmFyIGNvbW1pc3Npb246IEJpZ0RlY2ltYWwgPSBCaWdEZWNpbWFsLlpFUk9cbn1cblxuZnVuIG1haW4oKSB7XG4gICAgdmFsIGMxID0gUGF5bWVudE9wdGlvbi5DQVJEXG4gICAgdmFsIGMyID0gUGF5bWVudE9wdGlvbi5DQVJEXG4gICAgcHJpbnRsbihjMSA9PSBjMikgLy8gdHJ1ZSwgYmVjYXVzZSBpdCBpcyB0aGUgc2FtZSBvYmplY3RcblxuICAgIGMxLmNvbW1pc3Npb24gPSBCaWdEZWNpbWFsLlRFTlxuICAgIHByaW50bG4oYzIuY29tbWlzc2lvbikgLy8gMTBcblxuICAgIHZhbCB0ID0gUGF5bWVudE9wdGlvbi5UUkFOU0ZFUlxuICAgIHByaW50bG4odC5jb21taXNzaW9uKSAvLyAwLCBiZWNhdXNlIGBjb21taXNzaW9uYCBpcyBwZXItaXRlbVxufSJ9)

让这样的值可变是不好的，因为它们在每个项目中都是静态的。尽管该功能通常用于为每个项目附加一些常量值。这些常量值可以在每个项创建期间使用主构造函数附加:

科特林枚举甚至可以有方法。它们的实现也是特定于项目的。当我们定义它们时，enum 类本身(像这里的`PaymentOption`一样)需要定义一个抽象方法，并且每一项都必须覆盖它:

虽然此选项很少使用，因为使用函数类型的主构造函数参数更方便:

更方便的选择是定义一个扩展函数:

enum 的强大之处在于那些项目是特定的和不变的。因此，我们可以使用`values()`功能获取所有项目，或者使用`enumValueOf`功能按类型获取。我们也可以使用`valueOf(String)`从`String`读取 enum，或者使用`enumValueOf`按类型读取。

因此，遍历枚举值是容易的，它们的序列化/反序列化是简单而有效的(因为它们通常只由名称表示),并且被大多数序列化库自动支持(如 Gson、Jackson、Kotlin 序列化等)。).它们也有序数，并自动实现`toString`、`hashCode`和`equals`。因此，枚举非常适合表示一组具体的常量值。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

# 密封类

表示一组具体值的另一种方式是密封类。密封类是抽象类，在同一个文件中定义了具体数量的子类。

sealed modifier 所做的是不可能在文件之外定义这个类的另一个子类。由于这个原因，我们可以通过分析单个文件来确定一个密封类的子类。Kotlin 编译器也知道这一点，因此在某些情况下，如`when`，它可以建议选项，并理解所有的可能性都包括在内。(要了解`out`修改器，参见[这篇文章](/kotlin-generics-variance-modifiers-36b82c7caa39)。`Nothing`是此处所述[所有类型的一个子类型。)](/the-beauty-of-kotlin-typing-system-7a2804fe6cf0)

密封类非常适合表示和类型(范畴理论中的余积)——一组选择，就像最普通的一个:`Either`，它有`Left`或`Right`，但不能两者都有。

在我们的应用程序中，当我们处理一组可选类时，我们使用密封类。例如，API 告诉我们应该显示哪种广告:

注意，`FacebookAd`和`GoogleAd`都不保存数据，所以为了不在每次需要时都创建新的实例，我们对它们进行了对象声明并重用它们。密封类只能有对象声明子类，当以这种方式使用时，它非常类似于枚举:

虽然**我们不这样使用密封类**因为枚举更适合这种情况。

相对于 enum，密封类的优势在于子类可以保存特定于实例的数据。例如，当我们向应用程序的另一部分通知所选择的支付选项时，我们可以传递所选择的支付类型和特定于支付的数据，这些数据是后续处理所需要的。

密封类的一个很好的例子是表示各种各样的事件或消息，因为我们既有事件是什么的信息，又有每个事件可以保存的数据。

# 摘要

*   枚举类表示一组具体的值，而密封类表示一组具体的类。由于那些类可以是对象声明，我们可以在一定程度上使用密封类来代替枚举，但不能反过来。
*   枚举类的优点是它们可以被序列化和反序列化。他们有方法`values()`和`valueOf`。我们还可以使用`enumValues`和`enumValueOf`函数按类型获取枚举值。枚举有序数，我们可以通过每一项来保存常量数据。它们非常适合表示一组恒定的可能值。
*   密封类的优点是它们可以保存特定于实例的数据。每个项目可以是一个类，也可以是一个对象(使用对象声明创建)。它们代表一组可选类(和类型、余积)。它们对定义替代项很有用，`Result`要么是`Success`要么是`Failure`，`Tree`要么是`Leaf`要么是`Node`，或者 JSON 值要么是列表、对象、字符串、布尔、int 或 null。它们对于定义一组可能发生的事件或消息也非常有用。

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)