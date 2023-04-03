# 嘲讽不是火箭科学:预期行为和行为验证

> 原文：<https://blog.kotlin-academy.com/mocking-is-not-rocket-science-expected-behavior-and-behavior-verification-3862dd0e0f03?source=collection_archive---------1----------------------->

在前一篇文章中，我描述了嘲讽的基础。现在让我来概述一下[默克](http://mockk.io)的基本特征。

![](img/79b133a42db9b6f96be13837868c93b8.png)

对依赖组件的调用可能相当复杂。这就是为什么有时您需要提供复杂的预期行为。有两件事可以帮助你做到这一点:参数匹配器和预期答案。

> 参数匹配器是一个参数占位符，用于指定在`every`和`verify`结构中参数可以接受的各种值。
> 
> 预期答案是在被测系统中使用时调用返回的答案。

## 参数匹配

比如你用`call(arg: Int): Int`功能嘲讽了 DOC。如果参数大于`5`，则返回`1`，如果参数小于或等于`5`，则返回`-1`。这可以通过以下结构实现:

```
*every* **{** mock.call(more(5)) **}** returns 1
*every* **{** mock.call(or(less(5), eq(5))) **}** returns -1
```

`more`、`less`、`eq`和`or`是参数匹配器。你可以在文档中查看它们的[完整列表](http://mockk.io/#matchers)。对于大多数情况来说，这应该足够了，但是当然，你可以通过子类化或者 lambda 函数来创建你自己的。

相反，对于 Mockito 中的参数匹配，不需要为所有参数指定所有参数匹配器。您可以将一些参数保留为固定值。如果您给匹配器留一个固定值，它会自动被包含在`eq`匹配器中。

```
*every* **{** mock.call(more(5), 6) **}** returns 1// is same as*every* **{** mock.call(more(5), eq(6)) **}** returns 1
```

![](img/2f4722d181fb06dd8c2a4285f9489c65.png)

## 预期答案

答案定义了被模仿方法的行为。最简单的回答是`returns`。我们已经在前面的例子中观察到了。提供的值是固定的，并在每次匹配调用时返回。

`returnsMany`指定一个接一个使用的数值，即第一个匹配的调用返回第一个元素，第二个-第二个元素，等等

```
*every* **{** mock1.call(5) **}** returnsMany *listOf*(1, 2, 3)
```

您可以使用`andThen`构造实现同样的目的:

```
*every* **{** mock1.call(5) **}** returns 1 andThen 2 andThen 3
```

`throws`如果调用匹配，抛出异常。

```
*every* **{** mock1.call(5) **}** throws RuntimeException(**"error happened"**)
```

当返回值为`Unit`时，应使用`just Runs`。

```
*every* **{** mock1.callReturningUnit(5) **}** just Runs
```

`answers`允许指定返回答案的自定义 lambda 函数。

```
*every* **{** mock1.call(5) **}** answers **{** arg<Int>(0) + 5 **}**
```

`arg<Int>(0)`代表拦截调用中第一个参数的值。在 answer lambda 的范围内有很多这样的属性和函数。这有助于构建复杂的自定义答案。文档中提供了完整的列表。

## 行为验证

这是对上一篇文章中的示例的扩展。这模仿文档的行为，检查 SUT 并验证文档是否被调用。

> 从这一点开始，当我们添加验证时，它可以被称为嘲讽，作为一个行业创造的术语。点击阅读更多信息。

```
@Test
**fun** calculateAddsValues() {
  **val** doc1 = *mockk*<Dependency1>()
  **val** doc2= *mockk*<Dependency2>()

  *every* **{** doc1.**value1 }** returns 5
  *every* **{** doc2.**value2 }** returns **"6"

  val** sut = SystemUnderTest(doc1, doc2)

  a*ssertEquals*(11, sut.calculate()) *verify* **{** doc1.**value1**
    doc2.**value2**
  **}** }
```

> 模拟依赖组件的行为验证检查被调用。该检查应在测试结束时进行，在检查被测系统之后。

基本上支持四种类型的验证:`unordered`、`ordered`、`sequential`和`all`。

![](img/00fb9c49c3e0b7ea1631b851c92b0d16.png)

无序验证保证调用的发生不考虑顺序。例如，您想检查`call(5)`是否至少发生过一次。

```
*verify* **{** mock1.call(5) **}**
```

不像在`every`中，在`verify`块中，你可以一个接一个地进行几个调用。

```
*verify* **{** mock1.call(5)
mock1.call(6)
**}**
```

这将检查`call(5)`和`call(6)`是否至少出现过一次。

`verify`可以有参数。`atLeast`、`atMost`指定发生了多少已验证的呼叫。默认情况下，`atLeast`是`1`而`atMost`是`Int.MAX_VALUE`，这实际上意味着我们期望调用至少发生一次。

```
*verify(atLeast = 5, atMost = 7)* **{** mock1.call(5)
}
```

这将检查`call(5)`至少发生`5`次，最多发生`7`次。

`exactly`参数将`atLeast`和`atMost`设置为相同的值。

```
*verify(exactly = 5)* **{** mock1.call(5)
}
```

这个检查`call(5)`正好发生了 5 次。

指定`exactly = 0`您可以验证通话根本没有发生:

```
*verify(exactly = 0)* **{** mock1.call(5)
}
```

有时你想检查是否根本没有调用模拟文件。这可以通过特殊的`wasNot Called`构造来实现。相当于 Mockito 中的`verifyZeroInteractions`。

```
*verify* **{** mock1 *wasNot* Called 
**}**
```

这就是无序验证的基本内容。

`verifyAll`和`verify`做的一样，除了它额外检查所有匹配的呼叫是唯一发生在提到的模仿上的呼叫。这类似于《摩奇托》中的`verifyNoMoreInteractions`。

```
*verifyAll* **{** mock1.call(5)
}
```

接下来的两种验证类型是`verifyOrder`和`verifySequence`。他们检查来电的顺序。

不同之处在于`verifySequence`检查顺序，所有匹配的调用都是发生在提到的模仿上的唯一调用。这样它就能验证调用的确切顺序。

例如，让我们听听发生在 SUT 的电话:

```
mock1.call(1)
mock1.call(2)
mock1.call(3)
```

要验证发生的确切序列，您需要编写以下代码:

```
*verifySequence* **{** mock1.call(1)
  mock1.call(2)
  mock1.call(3)
**}**
```

`verifyOrder`另一方面，只验证调用发生的顺序，允许调用序列中有间隔。

```
*verifyOrder* **{** mock1.call(1)
  mock1.call(3)
**}**
```

这将检查`call(1)`是否发生在`call(3)`之前。

![](img/247a3a5a0063feed6647aae55c89339b.png)

这些是你应该知道的最基本的 MockK 特性，在下一篇文章中，我们将继续讨论更高级的东西。

下一篇文章:

[](/mocking-is-not-rocket-science-mockk-features-e5d55d735a98) [## 嘲笑不是火箭科学:嘲笑特征

### 如果你使用 Kotlin，那么与其他嘲讽框架相比，MockK 无疑是更好的选择。前一篇文章展示了一些…

blog.kotlin-academy.com](/mocking-is-not-rocket-science-mockk-features-e5d55d735a98) 

**拍手**说“谢谢”并帮助他人找到这篇文章。

[![](img/5cf9179effdb3e8ab1a9bf39598fdfd2.png)](https://kt.academy/article)

关于用 MockK 进行单元测试的下一篇文章将在下周发表。不要错过它。订阅出版物和作者频道。这是以前的文章:

[](/mocking-is-not-rocket-science-basics-ae55d0aadf2b) [## 嘲笑不是火箭科学:基础

### 模仿是一种使测试代码可读和可维护的技术。在随后的三篇文章中，我想…

blog.kotlin-academy.com](/mocking-is-not-rocket-science-basics-ae55d0aadf2b) 

感谢 [Dariusz Baciński](https://twitter.com/DBacinski) 的技术验证。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)