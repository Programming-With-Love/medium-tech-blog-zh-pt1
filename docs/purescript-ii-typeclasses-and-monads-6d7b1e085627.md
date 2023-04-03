# Purescript II:类型类和单子

> 原文：<https://medium.com/quick-code/purescript-ii-typeclasses-and-monads-6d7b1e085627?source=collection_archive---------0----------------------->

![](img/8215aaf3b71fb436b16f39ae69f0f9cf.png)

上周，我们开始探索 Purescript。Purescript 试图将 Haskell 的一些神奇之处带到 web 开发的世界中。它的语法看起来很像 Haskell，但是它编译成 Javascript。这使得它非常容易用于 web 应用程序。它不仅仅看起来像哈斯克尔。它使用了该语言的许多重要特性，比如强大的系统和功能的纯洁性。

如果您需要重温 Purescript 的基础知识，请务必再次阅读第一篇文章。本周，我们将探索 Purescript 有所不同的几个其他领域。我们将看到 Purescript 是如何处理类型类的，我们还将看到一元代码。我们还将快速看一下操作符的其他一些小细节。下周，我们将看看如何使用 Purescript 编写一些前端代码。

关于功能 web 开发的另一个观点，请查看我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)。您也可以下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)以获得更多创意！你也可以看看我们的 [Elm 系列](https://www.mmhaskell.com/elm)寻找另一个前端语言选项！

# 类型类别

从 Haskell 到 Purescript，类型类的概念仍然非常一致。但是仍然有一些问题。让我们记住上周的`Triple`类型。

```
data Triple = Triple
  { a :: Int
  , b :: Int
  , c :: Int
  }
```

让我们为它编写一个简单的`Eq`实例。首先，Purescript **中的实例必须有名字**。所以我们将把名字`tripleEq`分配给我们的实例:

```
instance tripleEq :: Eq Triple where
  eq (Triple t1) (Triple t2) = t1 == t2
```

同样，我们只为我们的类型打开一个字段。这对应于记录，而不是单个字段。事实上，我们可以互相比较记录。我们提供的名称有助于 Purescript 生成可读性更好的 Javascript。注意:命名我们的实例不允许我们拥有相同类型和类的多个实例。如果我们试图创建另一个实例，我们会得到一个编译错误，如下所示:

```
instance otherTripleEq :: Eq Triple where
  ...
```

当对类使用显式导入时，还有另一个小变化。我们必须在导入列表中使用`class`关键字:

```
import Data.Eq (class Eq)
```

你可能希望我们可以为我们的`Triple`类型派生出`Eq`类型类，我们可以做到。因为我们的实例需要一个名字，所以普通的 Haskell 语法不起作用。以下操作将失败:

```
-- DOES NOT WORK
data Triple = Triple
  { a :: Int
  , b :: Int
  , c :: Int
  } deriving (Eq)
```

但是对于简单的类型类，我们可以使用独立派生。这允许我们为实例提供一个名称:

```
derive instance eqTriple :: Eq Triple
```

最后一点，Purescript 不允许孤儿实例。孤立实例是指在不同于类型定义和类定义的文件中定义 typeclass 实例。在 Haskell 中你可以逃脱这些，尽管 GHC 会警告你。但是 Purescript 不那么宽容。解决这个问题的方法是围绕你的类型定义一个新的类型包装器。然后，您可以在该包装器上定义实例。

# 效果

在第 1 部分中，我们看了一小段一元代码。看起来像是:

```
main :: Effect Unit
main = do
  log ("The answer is " <> show answer)
```

如果我们试图与 Haskell 进行比较，似乎`Effect`是与`IO`相当的单子。事实也的确如此。但是比这要复杂一点。在 Purescript 中，我们可以使用`Effect`来表示“原生”效果。在我们深入了解这意味着什么以及如何做到这一点之前，让我们先来考虑一下“非本土”效应。

非原生效果是指那些像`Maybe`或`List`一样可以独立存在的单子。事实上，在本系列的第 1 部分中我们有一个`List`单子的例子。下面是`Maybe`可能的样子。

```
maybeFunc :: Int -> Maybe IntmightFail :: Int -> Maybe Int
mightFail x = do
  y <- maybeFunc x
  z <- maybeFunc y
  maybeFunc z
```

原生效果使用`Effect`单子。这些包括很多我们传统上在 Haskell 中与`IO`联系在一起的东西。例如，随机数生成和控制台输出使用`Effect`单子:

```
randomInt :: Int -> Int -> Effect Intlog :: String -> Effect Unit
```

但是还有其他与 web 开发相关的“原生效应”。其中最重要的是在我们的 Javascript 应用程序中写入 DOM 的任何东西。下周，我们将使用`purescript-react`库创建一个基本的网页。它的大部分主要功能都在`Effect`单子里。同样，我们可以想象这种效果会在 Haskell 中使用`IO`。所以如果你想把 Purescript 的`Effect`看作是`IO`的一个类似物，这是一个不错的起点。

有趣的是，Purescript 过去更多的是基于自由单子的系统。每一种不同类型的自然效果都建立在先前效果的基础上。最酷的是 Purescript 使用自己的记录语法来跟踪播放中的效果。你可以在 Purescript 书的第 8 章中读到更多关于这如何工作的内容。然而，在我们的例子中，我们不需要它。我们可以坚持使用`Effect`。

除了免费的单子，Purescript 还有`purescript-transformers`库。如果您更熟悉 Haskell，这可能是一个更好的起点。它允许您使用 MTL 风格的方法，这种方法在 Haskell 中比自由单子更常见。

# 特殊操作员

值得注意的是其他一些小差异。Haskell 和 Purescript 之间关于操作符的一些规则略有不同。由于 Purescript 使用句点操作符`.`进行记录访问，它不再引用函数组合。相反，我们将使用`<<<`data-preserve-html-node = " true "操作符:

```
odds :: List Int -> List Int
odds myList = filter (not <<< isEven) myList
  where
    isEven :: Int -> Boolean
    isEven x = mod x 2 == 0
```

此外，我们不能以中缀的方式定义运算符。我们必须首先为它们定义一个正常的名称。以下将不起作用:

```
(=%=) :: Int -> Int -> Int
(=%=) a b = 2 * a - b
```

相反，我们需要定义一个像`addTwiceAndSubtract`这样的名字。然后我们可以告诉 Purescript 将它作为中缀运算符应用:

```
addTwiceAndSubtract :: Int -> Int -> Int
addTwiceAndSubtract a b = 2 * a - binfixrl 6 addTwiceAndSubtract as =%=
```

最后，使用运算符作为部分函数看起来有点不同。这在 Haskell 中有效，但在 Purescript 中无效:

```
doubleAll :: List Int -> List Int
doubleAll myList = map (* 2) myList
```

相反，我们需要这样的语法:

```
doubleAll :: List Int -> List Int
doubleAll myList = map (_ * 2) myList
```

# 结论

这总结了我们对 Haskell 和 Purescript 之间的关键区别的看法。现在我们已经了解了类型类和单子，是时候深入了解 Purescript 最擅长什么了。下周回来，我们将看看如何用 Purescript 编写真正的前端代码！

想了解更多关于使用 Haskell 实现一些很酷的功能的想法，请下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)！对于函数前端开发的另一个视角，请查看我们最近的 [Elm 系列](https://www.mmhaskell.com/elm)！