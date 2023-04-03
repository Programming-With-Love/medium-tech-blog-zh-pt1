# 面向企业 Bee 的高级 FP:type classes

> 原文：<https://medium.com/google-developer-experts/advanced-fp-for-the-enterprise-bee-typeclasses-2addc232ae23?source=collection_archive---------0----------------------->

![](img/42013cc511cb741e4d12c0ae25a15d86.png)

Separate Frames from a Beehive

# 介绍

到目前为止，在我们的系列中，我们已经看到了 FP 库(像 [Arrow](https://arrow-kt.io/) 和 [Cats](https://typelevel.org/cats/) )向现有类型添加了额外的操作。这是通过**类型类**完成的。Typeclass(如果您愿意，也可以称为 Type Class)是将一组函数与一组字段关联起来的另一种方式。

Typeclasses [是 Haskell](http://learnyouahaskell.com/types-and-typeclasses) 的组成部分，Swift 为[提供了一个类似的特性，称为协议](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)。它们在 Kotlin 或 Scala 中并不直接可用，但这些语言为我们提供了模拟它们所需的工具。

本文将解释什么是 Typeclasses，为什么它们有用，以及如何用 Kotlin 和 Scala 编写它们。然后我们将回顾来自 Arrow 库的一个 Typeclasses 的实际例子。

# 普通班有什么不好？

在我们开始之前，让我们花一分钟来检查一下传统课程有什么问题。我们将回顾编写传统 C++/Java/C#风格的类的标准理由，然后看看我们能否反驳它们。如果你已经确信了类型类的价值，可以跳过这一节。

**类协同定位数据和函数**。这个论点似乎是无可辩驳的。为什么不想把数据和使用这些数据的函数放在同一个地方呢？毕竟，任何被诅咒维护写得很差的 C 代码的人都知道当情况并非如此时的悲惨结果。

然而，正如我第一次从阅读 Effective C++中学到的，一个类的[接口超出了类型](https://www.fluentcpp.com/2017/06/20/interface-principle-cpp/)的定义。您总是会有助手函数，它们接受该类型的一个实例并执行额外的工作。即使你的类是完美的，你仍然需要帮助函数来实现可选的和/或上下文相关的功能。

这就是为什么微软在设计 LINQ 查询语法的时候给 C# 增加了扩展方法。其他类型语言，[包括 Kotlin](https://kotlinlang.org/docs/reference/extensions.html) ，也是这么做的。

**类防止状态**的意外变化。这又是一个非常有力的论点。对对象中的字段进行不受控制的访问使得管理状态变得不切实际，维护也成了一场噩梦。然而，在纯 FP 中，我们要求我们的数据是不可变的，所以不受控制的变化不会成为问题。

**类抽象数据的格式**。这里的论点是，您想要自由地更改字段中使用的数据类型。例如，如果一个数字需要从 *int* 改为 *long* 或 *float* ，你可以这样做，而不会影响到你的代码库。

这是非常可取的，但不需要 OO。您所需要的只是一组函数，它们组成了底层数据的接口。例如，您可以使用一组 PL/SQL 存储过程来隐藏 Oracle 中数据库表的底层格式。

**类支持继承和多态**。当我学习编码时，继承和多态非常流行。它们是面向对象的杀手级特性。不幸的是，现实世界几十年的使用表明，实现继承往往比它的价值更麻烦。多态仍然非常有用，尤其是通过接口。但是，正如我们将看到的，Typeclasses 至少也可以提供多态性。

# 什么是类型类？

Typeclassses 的一个简单用例是支持到外部格式的转换。假设我们有时希望封送和反封送实体对象的内容。根据使用实体对象的上下文，格式可以是 XML 或 JSON。

这里有一个接口来表示我们需要的功能:

我们在类型参数 t 上定义了两个扩展方法。*编组*方法将把对象状态转换成某种文本格式，而*解组*方法将做相反的事情。

*unmarshal* 方法在类的层次上声明更合适，但是出于示例的目的，我们将使它成为一个实例方法。

接下来，我们创建一些简单的实体类型:

定义了接口和实体类型之后，我们可以为每种类型创建一个实现。下面是一些简单的*编组器*对象，用于将实例与 JSON 相互转换的*人员*和*实体*类型:

通过周围的 *JsonMarshalling* 对象和用于 Person 和 Order 的访问器函数*，实现的细节被封装。*

我们重复通过 XML 进行编组的过程。正如您所料，代码的结构是相同的:

我们现在需要的一切都准备好了。在下面的演示中，我们导入了 *JsonMarshalling* 类型，并使用访问器函数来获得我们的*编组器*接口的实现。当我们转换样本 *Person* 和 *Order* 对象中的数据时，我们需要扩展方法在范围内。由于内置的 *run* 方法，这很容易做到。

这是产生的输出。如您所见，我们已经成功地在 JSON 之间进行了封送:

```
{ “person”: Jane }
Person called Dave
{ “order”: 123.45 }
Order of value 456.78
```

鉴于 Typeclasses 都是关于可选功能的，切换到 *XmlMarshalling* 实现应该很容易:

现在，我们可以在 XML 之间来回编组:

```
<person>Jane</person>
Person called Dave
<order>123.45</order>
Order of value 456.78
```

使用简单的扩展函数也可以获得相同的结果。但是编译器不能检测一个类型是否在没有`unmarshal`的情况下实现了`marshal`，反之亦然。Typeclasses 为我们提供了一种将相关扩展捆绑到单一抽象中的方法。这提高了我们设计的质量和最终代码的可维护性。

# Scala 比较

我们已经看到，我们可以通过添加扩展方法集来成功地增强现有类型。但是有相当多的管道工程要做。在 Scala 中，这还会更简单吗？让我们移植示例并找出…

这是我们原始接口的 Scala 版本:

以及相应的实体类型:

当我们实现接口时，差异就出现了:

我们已经将这些实例标记为*隐式*。当客户端代码请求时，这允许编译器代表我们使用它们。隐式声明是 Scala 的一个“链锯特性”,在版本 3 中被重新设计。

另一个主要区别是 Scala 2 不直接支持扩展方法。相反，隐式声明为我们做了所有繁重的工作。

我们定义了一个名为 *JsonMarshallingOps* 的隐式类型，它接受一个 T 并提供*编组*和*解组*方法。这些将工作委托给*编组器*实例，因为它们本身被声明为*隐式*，所以将被编译器发现:

当我们在 Person 对象上调用 *marshal* 时，编译器将:

*   创建一个 *JsonMarshallingOps* 对象*，*通过*值*传入人员
*   找到*编组器【T】*的实例，以满足隐式参数列表
*   插入对 *JsonMarshallingOps* 的 *marshal* 方法的调用

这似乎非常复杂。但是当我们检查客户端代码时，我们只需要执行导入:

```
{ "person": Jane }
Person called Dave
{ "order": 123.45 }
Order of value 456.78
```

总之，Scala 实现比 Kotlin 更复杂。然而，这种复杂性可以更容易地抽象成一个库。

# 箭头中的 Typeclasses 示例

已经了解了两种语言中 Typeclass 模式的本质，让我们来看一个来自 Arrow 源代码的实际例子。我们可以从我们的第一篇文章中获取*遍历*操作符[，并尝试理解它是如何工作的。](/google-developer-experts/advanced-fp-for-the-enterprise-bee-traverse-b5e4e8b7b8e4)

这是我们遇到的第一个声明(包名简化):

```
fun <G, A, B> List<A>.traverse(
    arg1: Applicative<G>, 
    arg2: Function1<A, Kind<G, B>>): Kind<G,Kind<ForListK, B>> =   arrow.**[omitted]**.List.traverse().*run* {arrow.**[omitted]**.ListK(this@traverse)
                   .*traverse*<G, A, B>(arg1, arg2) 
                        as Kind<G,Kind<ForListK, B>>
  }
```

我们知道，*遍历*的第一个参数是一个*适用的*，第二个是一个函数。*应用*的类型参数对应函数返回的类型。

从之前的文章中我们还知道，*类*类型在 Kotlin 中被用来模拟更高级的类型。所以当我们看到`Kind<G, B>`时，我们知道 G 是容器类型，B 是内容。在函数返回一个`Option<Person>`的情况下，G 就是`Option`，B 就是`Person`。

Typeclass 功能从调用`List.traverse()`开始。如果我们深入下去，我们会看到这个函数总是返回一个`ListKTraverse`的实例。

这个类型为列表声明了一组扩展函数:

```
interface ListKTraverse : Traverse<ForListK> {

  override fun <G, A, B> Kind<ForListK, A>.traverse(
    AP: Applicative<G>, 
    f: (A) -> Kind<G, B>): Kind<G, ListK<B>> = *fix*().traverse(AP, f)

  **//other declarations omitted**
}
```

针对这种类型的实例调用了 *run* 实用函数，该实例又被传递给新创建的`ListK`实例。正是在这里，我们最终找到了遍历的实现:

```
fun <G, B> traverse(
    GA: Applicative<G>, 
    f: (A) -> Kind<G, B>): Kind<G, ListK<B>> = foldRight(Eval.now(GA.just(*emptyList*<B>().*k*()))) { a, eval ->
        GA.*run* { 
            f(a).*apEval*(eval.map { 
                it.*map* { xs -> { a: B -> (*listOf*(a) + xs).*k*() } } 
            }) 
        }
    }.value()
```

这肯定是一个很大的管道让我们的头转来转去。但是如果我们后退一步，整体结构与我们最初的(简单的)例子相似，只是增加了一个额外的扩展方法来避免客户端代码调用 *run* 。

# 用箭头组合我们自己的类型类

为了巩固我们到目前为止所学的知识，让我们从 HKT 的文章中拿一个竞赛的例子，并将其扩展到使用一个类型类。

正如我们所知，第一步是定义一个接口:

我们声明两个扩展函数，*判断公平*和*判断不公平*。因为我们希望它们适用于任何带有两个类型参数的类型，所以我们使用 *Kind2* 类型来模拟一个更高级的 Kind 类型。

下一步是声明一些箭头类型的实现:

完成这些后，我们可以对一些示例对象运行我们的扩展函数:

这给了我们预期的输出:

```
winner
loser
loser
winner
winner
loser
loser
```

# 类型类和表达式问题

我们已经看到，Typeclasses 提供了一种在现有类型上“添加”额外功能的方法。因此，类型类是表达式问题的一种可能的解决方案。这个问题源于以下观察结果:

*   面向对象的语言使得扩展现有的类型层次变得容易。他们通过子类化和(常规)多态来实现这一点。
*   函数式语言使得向现有的一组类型添加额外的操作变得容易。它们通过模式匹配来实现这一点，即优雅而有效地切换参数的实际类型。
*   没有一种方法可以两全其美。OO 不能很好地处理向已建立的接口添加新操作的问题。FP 不能处理将新类型添加到已建立的层次结构中。

Typeclasses 代表了一个中间地带，我们可以用最少的重构来扩展层次和操作。当然，如果接口是稳定的，那么额外的努力就是开销。所以，就像大多数“A 或 B”问题一样，我们发现常规类和类型类都更好。

# 结论

Typeclasses 为我们提供了关联函数和数据的另一种方法。它们提供了普通课程的许多优点，但也允许我们有选择地包含我们需要的内容。当我们增强现有类型，或者需要在实现之间切换时，这是非常好的。Kotlin 和 Scala 都提供了足够的功能来构造作为“二等公民”的类型类，但是这项工作更适合库作者。

# 谢谢

感谢 [Richard Gibson](https://twitter.com/rickityg) 和 [Instil 培训团队](https://instil.co/training/team/)对这一系列文章的评论、评论和鼓励。所有的错误当然是我自己的。