# Purescript 入门！

> 原文：<https://medium.com/quick-code/getting-started-with-purescript-cd3ed3b275b5?source=collection_archive---------0----------------------->

![](img/9e156ab12697f04fda655138b42b8130.png)

我们的 [Elm 系列](https://www.mmhaskell.com/elm-1)是我们在周一早上第一次潜入前端 web 开发的世界。我们没有使用众多库中的一个来实现纯 Haskell 方法，而是尝试了一种更适合 Web 的不同函数式语言。

延续这一趋势，我们将在接下来的几周时间里关注 Purescript。像 Elm 一样，它的语法也像 Haskell 的一样，并且它包含了许多功能纯洁性的元素。但是它可以编译成 Javascript，因此有一些特性似乎更适合用这种语言。

本周，我们将从探索 Purescript 的基础开始。我们将看到它和 Haskell 之间的一些主要的相同点和不同点。我们会发现 Purescript 拥有很多 Elm 所没有的 Haskell 语言特性。我们将用 Purescript 制作一个 web 前端，并看看如何将它连接到 Haskell 后端，这将是本系列的高潮。

Purescript 只是在产品中使用函数式语言的冰山一角！查看我们的[生产清单](https://www.mmhaskell.com/production-checklist)中一些令人敬畏的 Haskell 库！

# 入门指南

因为 Purescript 是它自己的语言，我们需要一些新的工具。你可以按照 [Purescript 网站](https://github.com/purescript/documentation/blob/master/guides/Getting-Started.md)上的说明去做，不过这里是要点。

1.  安装 Node.js 和 NPM，Node.js 包管理器
2.  运行`npm install -g purescript`
3.  运行`npm install -g pulp bower`
4.  创建您的项目目录并运行`pulp init`。
5.  然后你可以用`pulp build`和`pulp test`构建和测试代码。
6.  您还可以使用 PSCI 作为控制台，类似于 GHCI。

首先，我们需要 NPM。Purescript 是它自己的语言，但是我们想把它编译成可以在浏览器中使用的 Javascript，所以我们需要 Node.js。我们还会安装`pulp`和`bower`。纸浆将成为我们的建筑工具，就像阴谋集团一样。

Bower 是一个类似 Hackage 的包存储库。要在我们的程序中加入额外的库，可以使用`bower`命令。例如，在本文后面的解决方案中，我们需要`purescript-integers`。为此，运行以下命令:

```
bower install --save purescript-integers
```

# 简单的例子

一旦设置好了，就该开始涉猎这门语言了。虽然 Purescript 编译成 Javascript，但语言本身看起来更像 Haskell！我们将通过比较来检验这一点。假设我们想找出所有毕达哥拉斯三元组的总和小于 100。下面是我们如何用 Haskell 编写这个解决方案:

```
sourceList :: [Int]
sourceList = [1..100]allTriples :: [(Int, Int, Int)]
allTriples =
  [(a, b, c) | a <- sourceList, b <- sourceList, c <- sourceList]isPythagorean :: (Int, Int, Int) -> Bool
isPythagorean (a, b, c) = a ^ 2 + b ^ 2 == c ^ 2isSmallEnough :: (Int, Int, Int) -> Bool
isSmallEnough (a, b, c) = a + b + c < 100finalAnswer :: [(Int, Int, Int)]
finalAnswer = filter 
  (\t -> isPythagorean t && isSmallEnough t)
    allTriples
```

让我们用 Purescript 制作一个模块来解决同样的问题。我们将从编写一个模块`Pythagoras.purs`开始。下面是我们要编写的与上面的 Haskell 匹配的代码。我们将在下面逐一检查具体细节。

```
module Pythagoras whereimport Data.List (List, range, filter)
import Data.Int (pow)
import PreludesourceList :: List Int
sourceList = range 1 100data Triple = Triple
  { a :: Int
  , b :: Int
  , c :: Int
  }allTriples :: List Triple
allTriples = do
  a <- sourceList
  b <- sourceList
  c <- sourceList
  pure $ Triple {a: a, b: b, c: c}isPythagorean :: Triple -> Boolean
isPythagorean (Triple triple) =
  (pow triple.a 2) + (pow triple.b 2) == (pow triple.c 2)isSmallEnough :: Triple -> Boolean
isSmallEnough (Triple triple) =
  (triple.a) + (triple.b) + (triple.c) < 100finalAnswer :: List Triple
finalAnswer = filter
  (\triple -> isPythagorean triple && isSmallEnough triple) 
  allTriples
```

绝大多数情况下，事情都很相似！我们还有表情。这些表达式有类型签名。我们使用了很多类似的元素，比如列表和过滤器。总的来说，Purescript 看起来更像 Haskell 而不是 Javascript。但是有一些关键的区别。让我们从更高层次的概念开始探索这些。

# 差异

您在代码语法中看不到的一个区别是，Purescript 不会被延迟评估。Javascript 天生就是一种渴望的语言。因此，首先从一门渴望的语言开始，编译成 JS 要容易得多。

但是现在让我们考虑一下我们可以从代码中看到的一些不同之处。首先，我们必须进口更多的东西。默认情况下，Purescript 不会导入一个`Prelude`。你必须总是明确地把它带进来。我们还需要基本列表功能的导入。

说到列表，Purescript 缺少 Haskell 所拥有的语法优势。例如，我们需要使用`List Int`而不是`[Int]`。我们不能使用`..`来创建范围，而是求助于`range`功能。

我们也不能使用列表理解。相反，为了生成我们最初的三元组列表，我们使用列表单子。与列表一样，我们必须使用术语`Unit`而不是`()`:

```
-- Comparable to main :: IO ()
main :: Effect Unit
main = do
  log "Hello World!"
```

下周，我们将讨论 Purescript 中的`Effect`和 Haskell 中的`IO`等一元结构之间的区别。

一个烦恼是多态类型签名更复杂。而在 Haskell 中，我们创建类型签名`[a] -> Int`没有问题，这在 Purescript 中将会失败。相反，我们必须始终使用`forall`关键字:

```
myListFunction :: forall a. List a -> Int
```

这个例子中没有出现的另一个东西是`Number`类型。我们可以像在 Haskell 中一样在 Purescript 中使用`Int`。但是除此之外，唯一重要的数字类型是`Number`。这种类型也可以表示浮点值。这两者都被翻译成 Javascript 中的`number`类型。

# Purescript 数据类型

但是现在让我们来看看我们的例子之间更明显的区别。在 Purescript 中，我们需要创建一个单独的`Triple`类型，而不是使用一个简单的三元组。让我们通过考虑一般的数据类型来看看出现这种情况的原因。

如果我们愿意，我们可以像在 Haskell 中一样创建 Purescript 数据类型。因此，我们可以创建一个数据类型来表示毕达哥拉斯三元组:

```
data Triple = Triple a b c
```

这在 Purescript 中运行良好。但是，每当我们想从这个元素中提取一个单独的值时，它迫使我们使用模式匹配。我们可以在 Haskell 中解决这个问题，方法是使用记录语法为我们自己提供访问器函数:

```
data Triple = Triple
  { a :: Int
  , b :: Int
  , c :: Int
  }
```

这种语法在 Purescript 中仍然有效，但是它的含义有所不同。在 Elm 中，Purescript 记录是它自己的类型，就像普通的 Javascript 对象一样。例如，我们可以将它作为类型同义词，而不是完整的数据类型:

```
type Triple = { a :: Int, b :: Int, c :: Int}oneTriple :: Triple
oneTriple = { a: 5, b: 12, c: 13}
```

然后，我们不像在 Javascript 中那样使用字段名，而是使用“点语法”。下面是我们的类型同义词定义的样子:

```
type Triple = { a :: Int, b :: Int, c :: Int}oneTriple :: Triple
oneTriple = { a: 5, b: 12, c: 13}sumAB :: Triple -> Int
sumAB triple = triple.a + triple.b
```

这就是令人困惑的地方。如果我们使用带有记录语法的完整数据类型，Purescript 不再将它视为一个包含 3 个字段的项目。相反，我们会有一个只有一个字段的数据类型，而这个字段就是一条记录。因此，在使用访问函数之前，我们需要使用模式匹配来打开记录。

```
data Triple = Triple
  { a :: Int
  , b :: Int
  , c :: Int
  }oneTriple :: Triple
oneTriple = Triple { a: 5, b: 12, c: 13}sumAB :: Triple -> Int
sumAB (Triple triple) = triple.a + triple.b-- This is wrong!
sumAB :: Triple -> Int
sumAB triple = triple.a + triple.b
```

这是一个相当大的陷阱。犯这个错误得到的编译器错误有点混乱，所以要小心！

# 纯手稿中的毕达哥拉斯

有了这样的理解，上面的 Purescript 代码应该更有意义。但是我们会再看一遍，指出一些小细节。

首先，让我们列出我们的资源清单。我们没有范围语法糖，但是我们仍然可以使用`range`函数:

```
import Data.List (List, range, filter)data Triple = Triple
  { a :: Int
  , b :: Int
  , c :: Int
  }sourceList :: List Int
sourceList = range 1 100
```

我们没有列表理解。但是我们可以用 do 语法代替列表来获得同样的效果。注意，要在 Purescript 中使用 do 语法，我们必须导入`Prelude`。特别是，我们需要`bind`函数来实现它。现在让我们生成所有可能的三元组。

```
import Prelude…allTriples :: List Triple
allTriples = do
  a <- sourceList
  b <- sourceList
  c <- sourceList
  pure $ Triple {a: a, b: b, c: c}
```

还要注意我们用`pure`代替`return`。现在让我们写过滤函数。这些将使用上面提到的记录模式匹配和访问。

```
isPythagorean :: Triple -> Boolean
isPythagorean (Triple triple) = 
  (pow triple.a 2) + (pow triple.b 2) == (pow triple.c 2)isSmallEnough :: Triple -> Boolean
isSmallEnough (Triple triple) =
  (triple.a) + (triple.b) + (triple.c) < 100
```

最后，我们可以像在 Haskell 中一样用`filter`将它们结合起来:

```
finalAnswer :: List Triple
finalAnswer = filter 
  (\triple -> isPythagorean triple && isSmallEnough triple)
  allTriples
```

现在我们的解决方案将会奏效！

# 结论

本周我们开始探索 Purescript。从语法上来说，Purescript 是 Haskell 的近亲。但是我们在这里强调了一些关于语言本质的关键区别。

下周，我们将看看类型系统中其他一些重要的区别。我们将看到 Purescript 如何处理类型类和单子。之后，我们将会看到如何使用 Purescript 来构建一个 web 前端，并具有一些固体类型系统的安全性。

下载我们的[制作清单](https://www.mmhaskell.com/production-checklist)以获得更多你可以使用的酷库创意！