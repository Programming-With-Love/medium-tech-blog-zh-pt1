# 面向企业蜂的高级 FP:应用程序

> 原文：<https://medium.com/google-developer-experts/advanced-fp-for-the-enterprise-bee-applicatives-be76e4b6803c?source=collection_archive---------4----------------------->

![](img/c81bb36e997155a21088b70293296fa6.png)

Containers of Honey

# 介绍

在本系列的第一篇文章中，我们展示了*遍历*操作符是多么有用。作为其中的一部分，我们使用了适用的而没有进一步的解释。在本文中，我们将从头开始解释*应用程序*，然后回到最初的例子。这个系列的所有代码都可以在这个库中找到。

# 样本问题

就像上次一样，让我们说我们团队的常驻 FP 超级粉丝一直在努力工作。这一次他们转换了一个 UI 助手函数来返回一个*或者*。

如您所见，我们向用户提问，然后检查答案是否与模式匹配:

如果一切顺利，我们返回一个包含他们响应的 *Right* 。在出现错误的情况下，我们返回一个带有适当信息的*左*。

假设你试试这个。但是因为 FP 不是你的东西，你很难处理不同的排列。特别是，如果一切顺利，你需要想出如何将两个右*值的内容组合起来。这里有一个潜在的解决方案:*

```
What's your name?
Lucy
Where do you live?
Melbourne
Hello Lucy from Melbourne
```

这确实给出了所需的输出，但是这太程序化了，而且感觉不对。肯定有更好的方法吧？

# 介绍应用程序

这里有一个更好的方法来解决这个问题。它要求我们编写一个特殊的函数，我称之为*动作*。

```
val action = { name: String -> { location: String -> "Hello $name from $location" } }
```

如您所见，我们有:

*   一个 lambda，它接受一个名为 *name* 的参数，并返回一个 lambda
*   第二个 lambda 接受一个名为 *location* 的参数，并返回一个字符串
*   该字符串包括*名称*和*位置*的值

然后我们在表达式`name.map(action)`中使用它——这本身就很奇怪。如你所知 *map* 应用了一个转换，但是通常我们在标准数据类型之间转换，比如 *String* 和 *Int* 。

在这种情况下，将返回一个包含 lambda 的*右*，并提供了*名称*参数，但仍缺少*位置*参数。这可以被称为“部分应用的功能”。

然后我们将这个表达式嵌套在对 ap 的调用中，如下所示:

```
val result = location.ap(name.map(action))
```

*ap* 操作符针对*或*被调用，并期望包含部分应用功能的*或*作为其输入。它通过提供当前*或*中的值(如果可用)来完成函数的应用。

在快乐路径上，来自 *ap* 的返回值是一个*或者*包含来自内部 lambda 的返回值。在这种情况下，这是我们给用户的问候消息。成功！

```
What's your name?
Jane
Where do you live?
Bristol
Hello Jane from Bristol
```

# 解释适用的

本质上，*应用程序*提供了一种方法来操作一个容器类型的两个或更多实例的内容，而不必求助于程序上的诡计。 *ap* 函数是最常用的，但是还有许多其他操作符用于创建和组合实例。

要做到这一点，你的动作必须以上述方式书写。您必须提供一个接受单个输入的函数，然后返回一个期望额外输入的函数。幸运的是，Arrow 提供了一个 *curry* 操作符，将一个带多个参数的 lambda 转换成这样一个链:

如果我们看一下 *ap* 方法的实现，会感到困惑:

```
fun <A, B, C> EitherOf<A, B>.ap(ff: EitherOf<A, (B) -> C>): Either<A, C> =
  *flatMap* **{** a **->** ff.*fix*().map **{** f **->** f(a) **} }**
```

正如你所看到的，上一篇文章中神秘的*修复*方法再次被使用。另外，在我们期望看到*的地方，*我们看到了*或者*。现在我们不要担心这个，这将在下一篇关于更高级类型的文章中讨论。

# 重访导线

如果你记得上次，这就是我们如何调用*遍历*操作:

```
val result = input.traverse(Either.applicative(), ::propertyViaJVM)
```

现在你可以看到*应用*是如何帮助我们建立最终结果的。但这是一种略有不同的用法。对*应用型*的调用返回一个单例实例。这又包含了许多用于组合(在本例中)*或*对象的辅助方法。

这是我们最初的例子，重写后使用了 Singleton。当我们调用 *tupledN* 方法时，我们(在快乐路径上)得到一个包含用户输入的 *Tuple2* 的 *Right* 。

我们可以像往常一样用*映射*，并根据需要从 *Tuple2* 中获取值。在元组的箭头实现中，属性被简单地称为“a”、“b”、“c”等等。

如果你深究一下，你会发现箭头中的[应用类型是从](https://arrow-kt.io/docs/apidocs/arrow-core-data/arrow.typeclasses/-applicative/index.html#applicative)[扩展而来的一种叫做 Apply](https://arrow-kt.io/docs/apidocs/arrow-core-data/arrow.typeclasses/-apply/index.html) 的类型，这也是 *tupledN* 方法被声明的地方。这是 Arrow 实现的一个细节，但仍然值得一提。

*Apply* 类型还声明了一个 *mapN* 操作，其契约允许所提供的函数并行执行。下面是它的使用方法:

# 适用并经过验证

正如在上一篇文章中，我们可以通过从*或者*切换到*验证的*来改进原始代码。*验证的*类型增加了合并错误消息的额外功能。

我们需要告诉经过*验证的*实例我们想要如何组合多个实例。我们通过提供一个*半群*来做到这一点。这只是一个带有*组合*方法的对象。Arrow 为所有标准 Kotlin 类型提供了*半群*:

字符串的默认半群*将两个值连接起来。在多个错误的情况下，消息将一起运行:*

```
What's your name?
123
Where do you live?
456
Sorry: 123 does not match [A-Z a-z]+456 does not match [A-Z a-z]+
```

我们可以通过指定我们自己的半群来避免这种情况，它通过用空格和逗号连接来组合字符串:

现在，我们的错误消息将以逗号分隔:

```
What's your name?
123
Where do you live?
456
Sorry: 123 does not match [A-Z a-z]+ , 456 does not match [A-Z a-z]+
```

太棒了。直接调用 *ap* 操作符似乎比使用 Singleton 实例更简单，所以让我们用那个版本结束:

# 结论

在本文中，我们探讨了适用的类型和 *ap* 操作符。在这个过程中，我们也看到了什么是半群*和*。下一轮我们将解决另一个悬而未决的问题，即*修复*方法和*高级类型*。敬请关注…

# 谢谢

感谢 [Richard Gibson](https://twitter.com/rickityg) 和 [Instil 培训团队](https://instil.co/training/team/)对这一系列文章的评论、评论和鼓励。所有的错误当然是我自己的。