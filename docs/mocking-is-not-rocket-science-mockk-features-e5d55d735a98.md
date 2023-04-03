# 嘲笑不是火箭科学:嘲笑特征

> 原文：<https://blog.kotlin-academy.com/mocking-is-not-rocket-science-mockk-features-e5d55d735a98?source=collection_archive---------0----------------------->

如果你使用 Kotlin，那么与其他嘲讽框架相比，MockK 无疑是更好的选择。[上一篇文章](/mocking-is-not-rocket-science-expected-behavior-and-behavior-verification-3862dd0e0f03)展示了一些基础知识。现在是时候讨论诸如捕获的参数、轻松的模拟、间谍和注释等特性了。

![](img/2639ebe88a58339f71f9b1932f794908.png)

## 占领

如果您需要在`every`或`verify`块中获取一个参数的值，参数捕获可以让您的生活更加轻松。假设我们有以下类:

```
**class** Divider {
    **fun** divide(a: Int, b: Int) = a / b
}
```

有两种方法可以捕获参数:使用`CapturingSlot<Int>`和使用`MutableList<Int>`。

`CapturingSlot`只允许捕捉一个值，所以使用起来更简单。

```
**val** slot = *slot*<Int>()
**val** mock = *mockk*<Divider>()
*every* **{** mock.divide(capture(slot), any()) **}** returns 22
```

这创建了一个 slot 和一个 mock，并按照下面的方式设置预期的行为:如果调用了`mock.divide`，那么第一个参数被捕获到`slot`并返回`22`。

现在正在测试的代码:

```
mock.divide(5, 2) // 22 is a result
```

执行后，`slot.captured`值等于第一个参数，即`5`

现在你可以做一些检查，比如断言:

```
assertEquals(5, slot.captured)
```

除此之外，你可以在一个`answer` lambda 中使用一个插槽:

```
*every* **{** mock.divide(capture(slot), any()) 
**}** answers **{** slot.captured * 11
**}**
```

所以`mock.divide(5, 2)`返回`55`。

基本上就是这样。与`MutableList`的工作方式相同，只是应该使用`MutableList`功能，而不是`capture`中的`slot`。

```
**val** list = *mutableList*<Int>()
**val** mock = *mockk*<Divider>()
*every* **{** mock.divide(capture(list), any()) **}** returns 22
```

这允许在一行中从几个调用中捕获几个值。

![](img/1541386c39f29638d2fb4fe56b8c65b4.png)

## 轻松的嘲笑

默认情况下，模拟是严格的。在将 mock 传递给被测试的代码之前，你应该用`every`块设置行为。如果您没有提供预期的行为，并且执行了调用，库将抛出一个异常。

这和 Mockito 默认的做法是不一样的。Mockito 允许您跳过指定预期行为，并使用类似于`null`或`0`的一些基本值进行回复。通过声明 relaxed mock，您可以在 mock 中实现相同甚至更多的功能。

```
**val** mock = *mockk*<Divider>(relaxed = **true**)
```

然后你可以马上使用它:

```
mock.divide(5, 2) // returns 0
```

除此之外，如果返回值是引用类型，库将尝试创建子 mock 并构建调用链。

```
mock.call1(1, 2, 3).call2(4, 5, 6)
```

在`verify`块中，您可以检查调用是否被执行:

```
*verify* **{** mock.divide(5, 2) **}**
```

和

```
*verify* **{** mock.call1(1, 2, 3).call2(4, 5, 6) **}**
```

但是请注意，由于自然语言的限制，不可能让调用链为泛型返回类型工作，因为这些信息已经被删除了。如果您需要这样的用例，请在`every`块中显式设置预期行为。

## 间谍

Spies 提供了设置预期行为和进行行为验证的可能性，同时仍然执行对象的原始方法。

假设我们有下面的类:

```
**class** Adder {
    **fun** magnify(a: Int) = a

    **fun** add(a: Int, b: Int) = a + magnify(b)
}
```

我们想要测试`add`函数的行为，同时模仿`magnify`函数的行为。

让我们首先创建一个间谍:

```
**val** spy = *spyk*(Adder())
```

这里我们创建了对象`Adder()`，并在其上构建了一个间谍。构建一个 spy 实际上意味着创建一个特殊的同类型的空对象，并复制所有的字段。

现在我们可以使用它，就好像它是普通的`Adder()`物体一样。

```
*assertEquals*(9, spy.add(4, 5))
```

这将检查原始方法是否被调用。

除此之外，我们可以通过将它放到`every`块来定义间谍行为:

```
*every* **{** spy.magnify(any()) **}** answers **{** firstArg<Int>() * 2 **}**
```

这为`magnify`函数提供了一个预期的答案。

此后，`add`的行为发生了变化，因为它依赖于`magnify`:

```
*assertEquals*(14, spy.add(4, 5))
```

所以我们用间谍达到了目的。

此外，我们可以验证调用，就像它是一个模拟:

```
*verify* **{** spy.add(4, 5) **}** *verify* **{** spy.magnify(5) **}**
```

这是一种非常强大的测试技术。

![](img/abcdf63749fcb8b4160ab0479d160c4f.png)

## 释文

该库支持注释`@MockK`、`@SpyK`和`@RelxedMockK`，可以作为一种更简单的方式来创建相应的模拟、间谍和轻松模拟。

```
**class** Test {
    @MockK
    **lateinit var doc1**: Dependency1

    @RelaxedMockK
    **lateinit var doc2**: Dependency2

    @SpyK
    **val doc3** = Dependency3()

    @Before
    **fun** setUp() = MockKAnnotations.init(this)

    @Test
    **fun** calculateAddsValues1() {
        *every* **{ doc1**.call().add(any()) **}** returns 5
        *every* **{ doc2**.**value2 }** returns **"6"** *every* **{ doc3**.sub(any()) **}** returns 7

        **val** sut = SystemUnderTest(**doc1**)

        *assertEquals*(11, sut.calculate())
    }
}
```

这里重要的部分是在`@Before`阶段执行的`MockKAnnotations.init(this)`调用。当它被执行时，所有带注释的属性都被相应的对象所替代:模仿、间谍和放松的模仿。

![](img/d27a417733ced087a022994fad0dc3f1.png)

下一篇文章将描述链式调用、对象模拟、扩展函数和 DSL。

感谢您的关注！

**拍手**说“谢谢”并帮助他人找到这篇文章。

[![](img/5cf9179effdb3e8ab1a9bf39598fdfd2.png)](https://kt.academy/article)

关于用 MockK 进行单元测试的下一篇文章将在下周发表。不要错过它—订阅出版物和作者频道。以下是上一篇文章:

[](/mocking-is-not-rocket-science-expected-behavior-and-behavior-verification-3862dd0e0f03) [## 嘲讽不是火箭科学:预期行为和行为验证

### 在上一篇文章中，我描述了嘲讽的基础。现在让我来概述一下 MockK 的基本特性。

blog.kotlin-academy.com](/mocking-is-not-rocket-science-expected-behavior-and-behavior-verification-3862dd0e0f03) 

感谢 [Dariusz Baciński](https://twitter.com/DBacinski) 的技术验证。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)