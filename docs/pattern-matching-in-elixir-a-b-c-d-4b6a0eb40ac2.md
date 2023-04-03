# ["模式"，"匹配"，"在"，"药剂"] = [a，b，c，d]

> 原文：<https://medium.com/quick-code/pattern-matching-in-elixir-a-b-c-d-4b6a0eb40ac2?source=collection_archive---------0----------------------->

![](img/5d0428e6e53534d68f2751ad50e765ff.png)

Pattern Matching In Elixir

> 向网上我最喜欢的人问好。有什么事吗？？？？？？利用这次隔离，我希望你是安全的

已经一个星期了，虽然我一直在学习长生不老药和凤凰。

Elixir 是一种函数式语言，用于构建可扩展和可维护的应用程序。

**Phoenix** 是一个用 Elixir 编写的 web 开发框架，它使用服务器端 MVC 模型，但是我们不打算在本文中深入研究 Phoenix，以后再说吧。

今天我们要讨论的是仙丹中惊人的 ***图案搭配*** 。到目前为止，这是我所见过的编程语言中非常独特的特性。

# 什么是模式匹配？

**模式匹配**指的是灵丹妙药，我们匹配一个模式来缓解问题。
模式匹配是仙丹的强大部分。它允许我们匹配简单的值、数据结构，甚至函数。

快速示例:

上面的例子是针对一个`list`，模式匹配可以用于复杂的数据结构。

让我们深入了解一下理解模式匹配的两个主要操作符。

# 匹配运算符

在大多数编程语言中通常被称为赋值操作符的`=`在 Elixir 中被称为`match operator`。
唯一不同的是，在 Elixir 中没有赋值或给变量赋值这种事情。

![](img/db5d162ca58803a908a98b037ba43ed1.png)

是的，你说对了，除了断言没有赋值。
值与变量绑定，可以是无界的，可以重新绑定。

它相当于代数中的等号。编写它会将整个表达式转换成一个等式，并使 Elixir 将左边的值与右边的值相匹配。如果匹配成功，则返回等式的值。否则，它会抛出一个错误。

```
# this is example for simple matching
iex> x = 1 
1iex> 1 = x 
1 iex> 2 = x 
** (MatchError) no match of right hand side value: 1
```

如果您不想在匹配过程中获取值，可以使用`_`(下划线)忽略它。

```
iex> [1, _, _] = [1, 2, 3]
[1, 2, 3]iex> [1, _, _] = [1, “elixir”, “js”]
[1, “elixir”, “js”]
```

# Pin 运算符

如果您想使用模式中某个变量的现有值，Elixir 为我们提供了一个`pin operator ^`。你只需要在变量前加上 pin 操作符，然后 elixir 强制使用变量的现有值。

```
iex> a = 1
1iex> a = 2
2iex> ^a = 1
** (MatchError) no match of right hand side value: 1iex> a = 1
1iex> [^a, 2, 3 ] = [ 1, 2, 3 ]
# uses existing value of a that is 1
[1, 2, 3]iex> a = 2
2iex> [ ^a, 2 ] = [ 1, 2 ]
** (MatchError) no match of right hand side value: [1, 2]
```

## 复杂数据结构上的模式匹配

模式匹配不仅用于简单的值，还可以扩展用于匹配复杂的数据类型，如元组、映射和列表等。

>的一些例子，让我们看看模式匹配列表

```
iex> list = [1, 2, 3]
[1, 2, 3]iex> [head | tail] = list
```

列表还支持对其自身的`head`和`tail`进行匹配，

```
iex> [head | tail] = list
[1, 2, 3]iex> head
1iex> tail
[2, 3]
```

关于 Elixir 中的模式匹配，有很多需要注意和学习的地方。这是一篇关于**模式匹配**的介绍性文章，只是为了让你熟悉它。

我将会写更多关于模式匹配的内容，直到下一次。在隔离期间，保持坚强，照顾好自己。

在 twitter 上关注我保持联系，让我知道你对 Elixir 最感兴趣的是什么。

[https://twitter.com/bhavishya2107](https://twitter.com/bhavishya2107)

> *谢谢！！！*
> 
> *保持学习和编码😎*

# 参考:

*   目前，我参考的唯一资源是这本关于长生不老药的书。
    [https://pragprog.com/book/elixir16/programming-elixir-1-6](https://pragprog.com/book/elixir16/programming-elixir-1-6)