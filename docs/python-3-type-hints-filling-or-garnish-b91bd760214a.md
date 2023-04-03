# Python 3 类型提示:填充还是装饰？

> 原文：<https://medium.com/capital-one-tech/python-3-type-hints-filling-or-garnish-b91bd760214a?source=collection_archive---------5----------------------->

![](img/9e6fb39e6ca3070e81e6ad6f0a85013e.png)

## **表白#1:**

我喜欢莳萝泡菜三明治。是的。莳萝泡菜。黑麦面包。我女儿喜欢吃，但我女婿呢？他不喜欢泡菜。*“那太不对了。泡菜是一道配菜。或者装饰。你放在汉堡上的东西。但不是三明治馅料。”*

## **告白#2:**

我写了 [Functional Python 编程——第二版](https://www.packtpub.com/application-development/functional-python-programming-second-edition)，可从 Packt Publishing 获得。我编写了大部分包含类型提示的代码示例，所以我对适当使用提示以及如何使用 [mypy 工具](http://mypy-lang.org/)有一两点想法。

这些自白是相关的——在 Python 3 中，类型提示有点像莳萝泡菜。它们是装饰品吗？它们是三明治的馅料吗？将它们包含在遗留代码中就像吃一周前做的不新鲜的遗留三明治一样吗？ *(我们不要切得太深，好吗？)*

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

# 类型提示和类型检查

在探索我们的 pickle 隐喻问题和看一个具体的例子之前，让我们先讨论一下关于类型暗示的讨论。有了类型提示，观点的改变会导致不适，而那种宽泛模糊的陈述揭示了人们的不适。

如果你在 Twitter 上关注各种各样的 Pythonistas，你可以在关于类型提示的优点的辩论中看到一些。一些关键点似乎包括:

*   很难。
*   太难了，只有绝对需要的时候才做。
*   这很难，但会有帮助。
*   太啰嗦了。
*   真的很有帮助，需要更广泛的使用。
*   它代表了语言中的一个“空白”；缺少类型检查意味着 Python 不能用于“真正的”计算。

让我们更认真地看看其中的一些抱怨。

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

# Python 缺乏类型检查

这一点在我看来反映了一个怪异的观点。Python *是否有非常非常严格的运行时类型检查。大量糟糕的代码会引发运行时类型错误。Python 强调类型和操作符之间极好的正交性，允许 duck 类型在许多情况下工作。这个我怎么强调都不为过， ***Python 有非常严格的类型检查。****

Python 没有将类型信息附加到标识符上:变量没有静态声明的类型。然而，变量引用的对象是有类型的，试图滥用这些对象会导致运行时问题。我怎么强调都不为过——不，真的，我不能——***Python 有非常严格的类型检查。***

对于一些人来说，这一点不是关于缺少类型检查，而是缺少*“编译时”检查*。一旦人们了解了内部类型检查，他们喜欢将论点向前推进，*“一种真正的语言会用类型检查来防止这种情况。”*我一般用反问来回应:*“那你为什么单元测试？”* Python 和静态类型检查语言有相同的工作流程，所以错误“预防”的想法似乎没有多大帮助。

在少数情况下，关于 Python 和类型提示的讨论会偏离主题，转而讨论一些 IDE 如何基于声明的变量类型提供语法建议。我确信这可能是有帮助的，但是我有保留。大多数情况下，这让我想知道当作者依赖 IDE 提供建议时，代码的可靠性有多高。

继续泡菜比喻的问题，当我女儿小的时候，我们在厨房玩，故意做奇怪的三明治。我记得饼干中间的薯片让我们俩和家里的另一个大人都陷入了麻烦。但是不管我女儿问多少次，我们从来没有在三明治里用过猫粮。

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

# 冗长

类型提示的冗长会成为一个问题。当从内置类型创建复杂对象时，我们经常忘记给中间对象类命名。让我们看一些完全由内置 Python 集合类型创建的复杂类型对象的具体例子。

假设我们正在做一些地理定位处理。我们得到的数据如下所示:

```
{((12, 13), (14, 15)): 2.8284271247461903, ((14, 15), (2, 3)): 16.97056274847714, ((2, 3), (5, 7)): 5.0}
```

这可以用下面的类型来描述

```
Dict[Tuple[Tuple[int, int], Tuple[int, int]], float]
```

让我们假设这个结构是由下面的 **d_map()** 函数创建的。下面是没有提示的版本。添加提示似乎很难。

```
def d_map(points):
    return {
        (p1, p2): hypot(p1[0]-p2[0], p1[1]-p2[1])
        for p1, p2 in points 
    }
```

宣言变成了 L. O… N… G…也就是说，如果我们犯了一个常见的错误。

```
 def d_map(points: List[Tuple[Tuple[int, int], Tuple[int, int]]]) -> Dict[Tuple[Tuple[int, int], Tuple[int, int]], float]:
       return {
           (p1, p2): hypot(p1[0]-p2[0], p1[1]-p2[1])
           for p1, p2 in points
       }
```

这些提示并没有完全描述正在发生的事情。我们写的提示省略了重要的细节，没有反映数据结构的底层语义。

Python 的优势之一是内置语法的一流数据结构的丰富集合。我们可以将一些复杂的概念缩写成简洁明了的代码。然而，我们不应该忽视简洁的代码代表了什么。在这种情况下，它代表了一些相当复杂的概念。

```
**<rant>***Let me play the old programmer card. Wait a second while I set up my lawn chair so I can shake my fist at kids these days. When I was your age we walked to school. It was uphill. Both ways. And, we spent forever trying to get linked lists and simple hash maps to work. I remember struggling with a simple binary tree to summarize a large file into counts grouped by code values. Nowadays, you just slap a* collections.Counter *into your code like it’s a nothing. It’s not a nothing. It’s serious, sophisticated software engineering. It’s more than* Dict[Any, int]*.**In the long run, I think it’s the simple summary that makes it easy for us to leverage a sophisticated structure without having to write down all of the details.***</rant>**
```

当遇到一个超长的类型提示时，我们能做什么？有疑问的时候，*公开中间类型*。下面是我对上面例子的改写:

```
Point = Tuple[int, int]
Leg = Tuple[Point, Point]
Distances = Dict[Leg, float]
def d_map(points: Iterable[Leg]) -> Distances:
    return {
        (p1, p2): hypot(p1[0]-p2[0], p1[1]-p2[1])
        for p1, p2 in points
    }
```

这以一种有用的形式提供了细节，而最初的定义避免描述输入数据结构的任何细节。查看代码，我们可以推断出二元组的数字可能是输入，但没有太多的细节。这个修订版给了我们可重用的类型名，它可以是其他函数的一部分。

我想为类型提示调用一种质量保证检查。长类型提示通常包含冗余。我认为冗余是错误:它们是中间数据结构，应该给它们起个合适的名字。

当我们进一步研究这个问题时，我们可能会重新考虑使用一个简单的二元组来表示一个点。 **p1[0]** 语法是冰箱后面的奶酪，上面可能有东西生长。我们不希望三明治上有这个。也许这是应该的。

```
class Point(NamedTuple):
    x: int
    y: int
```

这导致了微小的(几乎-但不是很微不足道的)简化。代替为每个点构建简单的元组，我们现在可以构建命名的**点**元组，并使用 **p1.x** 和 **p1.y** 使代码更加有趣。这种变化就像你的鲁本三明治从平淡无味的白面包换成了香脆可口的黑麦面包。黑麦面包有更多的结构来做一个装满泡菜的丰盛三明治。

当我们仔细观察上面概述的抱怨时，“很难”的抱怨感觉就像“我不喜欢在我的鲁本三明治上放泡菜。”没有泡菜(或凉拌卷心菜)，这不是真正的鲁本。类似地，没有类型提示的 Python 代码似乎缺少一个基本特性。

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

# 代码高尔夫没有赢家

这种方法的一个次要后果是倾向于避免使用 **()** 、 **[]** 和 **{}** 来构建元组、列表、映射或集合。是的。这是异端邪说。我发现使用 **tuple()** ， **list()** ， **dict()** 和 **set()** 稍微容易一些，因为随着设计的成熟，我可以用等价的名称替换类型名称。

“但是，”你可能会反对，“代码客观上更长！你什么都没给我留！你是个骗子！”

我的第一反应是，“正确。”客观上更长。我的第二个回答是，“[代码高尔夫](https://en.wikipedia.org/wiki/Code_golf)不是我想赢的游戏。”

虽然混淆的 Python 代码竞赛可能很有趣，但我不想故意混淆工作代码。Python 的[禅建议我们“明的比隐的好。”依赖于以下行为的代码是错误的，从更大的意义上来说，是有意和恶意的模糊。](https://www.python.org/dev/peps/pep-0020/)

```
>>> {1: ‘one’, True: ‘true’}
{1: ‘true’}
```

我喜欢在更复杂的数据结构中展示细节。类型定义可能是可重用的，我认为这可以在整个大型应用程序中增加清晰度。它还可以作为编写描述行为的完整文档字符串的种子。

当这种声明是可重用模块的一部分时，这种美好就像微笑和拥抱一样传遍整个应用程序。不久，其他功能也有所调整，每个人都互相发送带有彩虹纸杯蛋糕的泰迪熊拥抱小礼物。(只是请不要换聚脂薄膜气球。他们是邪恶的。)

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

# 通用函数

提供类型提示的一个更困难的问题是详细描述泛型行为。让我们来看一个函数，它获取一系列单独的点，并创建旅行的成对点(腿)。

```
def pair_iter_1(points):
    return zip(points[:-1], points[1:])
```

这需要两个(可能更大的)内存数据结构。我的偏好如下。

```
def pair_iter_2(points):
    point_iter = iter(points)
    p1 = next(point_iter)
    for p2 in point_iter:
        yield p1, p2
        p1 = p2
```

这两个函数， **pair_iter_1()** 和 **pair_iter_2()** ，都依赖于在数据类型方面非常通用的 Python。这些函数适用于各种各样的集合，基本类型几乎可以是任何类型。找到合适的抽象可能很有挑战性。这里有一种方法——虽然在技术上是允许的——却有误导性。

```
def pair_iter(points: Iterable[Any]) -> Iterator[Tuple[Any, Any]]: …
```

这有什么不好？任何一种类型都不会对输出和输入之间的关系做出强有力的声明。类型暗示的目的是根据需要做出强有力的声明。现在，我们可以走得更远，这样写:

```
def pair_iter(points: Iterable[float]) -> Iterator[Tuple[float, float]]: …
```

这遭受了一个不必要的约束，在一个完全通用的算法中需要数字。使用类型变量允许我们提供足够强的约束。这澄清了转换的本质:源数据没有被正在执行的重构所影响。

```
_T = TypeVar(“_T”)
def pair_iter(points: Iterable[_T]) -> Iterator[Tuple[_T , _T]]:
    point_iter = iter(points)
    p1 = next(point_iter)
    for p2 in point_iter:
        yield p1, p2
        p1 = p2
```

类型变量 **_T** 被绑定到参数 iterable 以及结果迭代器的类型。这表明该函数的意图是提供一个围绕源对象构建的结果结构。它阐明了底层对象没有任何转换。

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

# TL；速度三角形定位法(dead reckoning)

当您的类型提示看起来笨拙而庞大时，请考虑公开中间类型。它通常有助于将一个大的结构类型提示分解成组成部分。我喜欢这样想:

> 如果您必须为 list、dict、set 和 tuple 的每个变体创建一个类定义，您的新类会被命名为什么？

这条规则有助于阐明类型提示的作用。实际上，我们正在为 Python 数据结构创建名称，同时避免了完整类定义的开销。如果你必须描述一个类的潜在含义——与其实现细节分开——你会给它取什么名字？挑选名字是在软件中创造意义的核心。在处理了数十个函数式编程示例之后，类型命名问题频繁出现。给类型起一个合理的名字可以更容易得到正确的类型提示。它还教会了我选择名字是计算中最难的两个问题之一。(另一个最难的问题？缓存失效和一个接一个错误。)

一个复杂的类型定义就像泡菜、gari 或 kimchee:一种腌制的混合物，不仅仅是配菜或装饰，而不是一顿饭。就像酵母发酵的面包和烤肉本身不是鲁本一样。一个合适的三明治包含多种成分，没有一种是事后才想到的。

## **第三次表白**:

我错过了写这篇文章的午餐。

![](img/dbf083e03b5e90c7f17f3fd0af44dfc3.png)

***披露声明:以上观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 Capital One 2018。***