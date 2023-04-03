# Scala 3:这 10 个主要变化可能会影响你的日常编码

> 原文：<https://medium.com/globant/scala-3-these-10-major-changes-could-influence-your-everyday-coding-3a5075b5ae6f?source=collection_archive---------0----------------------->

![](img/55e727c6450cf7030056af142ab4dbe7.png)

# 新版本，许多变化…

Scala 3 已经存在一段时间了(在我写这篇文章的时候，它的版本是 3.1.1)。如果您来自 Scala 2，想要进入版本 3，无论是出于好奇还是因为即将到来的项目，本文将向您展示语言语法和结构的 10 个主要变化，它们将影响您的日常编码任务。

## 1:作为程序入口点

通常 Scala 2 程序的入口点是这样的:

```
object MyScalaMainProgram{
 def main(args: Array[String]): Unit = {
  println(s"Hello ${args(0}}!")
 }
}
```

或者这个:

```
object MyScalaMainProgram extends App {
  println(s"Hello ${args(0}}!")
} 
```

此外，Scala 3 通过使用 ***@main*** 注释将一个函数定义为我们程序的入口点，如下所示:

```
@main def mainProgramFunction(name: String): Unit = {
  println(s"Hello $name!")
}
```

请注意，该函数具有类型化参数，因此，如果在命令行中提供了正确值的字符串表示，它们将自动转换为相应参数的数据类型(当然，这是一个只有一个字符串参数的非常简单的示例)。这个特性有助于避免所有与简单命令行参数解析相关的样板代码。

如果您想从命令行获得旧式的字符串值，您可以使用 *varargs 语法*编写函数:

```
@main def mainProgramFunction(args: String*): Unit = {
  println(s"Hello ${args(0)}!")
}
```

## 2:可选的基于缩进的语法

Scala 3 为 if-else、for-comprehensions、模式匹配和函数等表达式提供了一种新的、替代的(but ***可选的*** )语法，该语法基于缩进而不是括号。据称，这种新语法是为了吸引非 Scala 受众，即 Python 开发人员。这里我们有一些例子:

```
// for-comprehension, cycle style
for
  i <- 1 to n if n % 2 == 0
do
  *println*(i)
  *println*("-------")
end for// for-comprehension with yield
val result =
  for
    i <- 1 to 5
    j <- 1 to 5
  yield
    val z = i * j
    z
// You can also use 'end for' here if you like// if-else, control structure style
if n % 2 == 0 then
  *println*("Yes")
  *println*(s"**$**n is an odd number")
else
  *println*("Nope")
  *println*(s"**$**n is not an odd number")
end if// if-else, expression style
if n % 2 == 0 then
  "Yes"
else
  "Nope"
// You can use 'end if' here if you like// Class with brace-less syntax
class Point2D(val x: Double, val y: Double):

  def distance: Double = *sqrt*(*pow*(x, 2.0) + *pow*(y, 2.0))

  def angle: Double = angleForQuadrant(x, y) + 180 / *Pi* * *atan*(y/x) // A method with a sample pattern matching
  private def angleForQuadrant(x: Double, y: Double)  =
    (x.sign, y.sign) match
      case (1.0, 1.0) => 0
      case (-1.0, _) => 180
      case (1.0, -1.0) => 360

end Point2D
```

你可能已经注意到了 ***结尾的*** 字后跟 ***if*** ， ***为*** 甚至是一个类或函数/方法名。这叫做*结束标记*，在代码块很长的情况下使用，以帮助读者区分代码块的结束位置，从而避免混淆；它是完全可选的，如上面的例子所示。

## 3:扩展方法而不是隐式转换

通常，我们有一个类型/类，它的代码不受我们的控制，例如第三方库中的一个类，但是我们希望它有一个目前没有的方法。在那些情况下，我们求助于*隐式转换*。

例如，假设我们想要一个方法 **Int.times()** 来执行一个动作，次数与 Int 的值一样多。我们首先在包对象中创建一个隐式类:

```
package object conversion {
  implicit class EnrichedInt(val n: Int) {
    def times(action: => Unit): Unit = for {_ <- 1 to n } action
  }
}
```

然后我们在代码中使用:

```
10.times { *println*(s"Hello, **$**{args(0)}") }
```

相反，Scala 3 的特点是*扩展方法*。让我们看看如何在 Scala 3 中添加上述额外的方法:

```
extension (n: Int) def times(action: => Unit): Unit =
 for _ <- 1 to n do action
```

更少的样板代码。此外，扩展方法不需要包对象来包含它们(下一节将详细介绍)。

## 4:顶级定义而不是包对象

Scala 中的包对象让我们持有包范围的定义，例如:

*   键入别名
*   常量值/对象
*   向现有类型添加方法的隐式类*(见上一节)*
*   不作为类中的方法应用的函数

然而，在 Scala 3 中，所有这些都被认为是*顶级定义*，所以我们要做的只是在 Scala 文件中定义它们，如下所示:

```
package io.picetti.scala3.article//We already know this: extension function
extension (n: Int) def times(action: => Unit): Unit =
 for _ <- 1 to n do action

// Immutable variables
val X_COORD: Double = 35.5
val Y_COORD: Double = 45.2

def fizzBuzz(num: Int) =
  num match
    case n if n % 15 == 0 =>
      *print*("Fizzbuzz")
      *println*("!")
    case n if n % 5 == 0 =>
      *print*("Buzz")
      *println*("!")
    case n if n % 3 == 0 =>
      *print*("Fizz")
      *println*("!")
    case _ =>
      *println*(num)
  end match
```

## 5:联合和相交类型

让我们考虑用 Scala 2 编写的以下函数:

```
def divideXByY(x: Int, y: Int): Either[String, Int] = {
  if (y == 0) *Left*("Dude, can't divide by 0")
  else *Right*(x / y)
}
```

它使用***【E，V】***类型返回结果或错误消息。在 Scala 3 中使用*联合类型*可以获得相同的结果。这种类型允许您定义返回的类型或提供的参数是 A ***或*** B ***或*** …。让我们在上面的例子中使用这个，而不是任何一个:

```
def divideXByY(x: Int, y: Int): String | Int =
  if y == 0 then "Dude, can't divide by 0" else x / y
```

当然，这个例子是琐碎的，并没有反映出联合类型的力量。但是，如果我们想扩展这个函数的逻辑，使它返回:

*   两个整数的整除，如果它们可以被整除的话
*   在两个整数都不能被整除的情况下，带小数的精确除法*和*
*   除数为零时的常见错误消息。

在这种情况下，Scala 3 中的函数可以这样表达:

```
ef divideXByY(x: Int, y: Int): String | Double | Int =
  if y == 0 then
    "Dude, can't divide by 0"
  else if x % y != 0 then
    (x * 1.0) / (y * 1.0)
  else x / y
```

这种情况不能用 ***或者【E，V】***处理，否则代码会过于复杂，因为其中任何一个都只处理 2 种类型(我将把上面代码的实现留给读者作为练习)

类似地，*交集类型*让我们将返回类型或提供的参数定义为类型 A ***和*** B ***和*** …。让我们来看看下面的代码:

```
trait Point2D:
  val x: Double
  val y: Double

  def draw: Unit

trait Polarizable:
  def distance: Double
  def angle: Double

class PolarPoint(
  override val x: Double,
  override val y: Double
  ) extends Point2D with Polarizable:

  override def draw: Unit =
    *println*("Let's assume the point is drawn here")
  end draw

  override def distance: Double = *sqrt*(*pow*(x, 2.0) + *pow*(y, 2.0))

  override def angle: Double =
    angleForQuadrant(x, y) + 180 / *Pi* * *atan*(y/x)

  private def angleForQuadrant(x: Double, y: Double) =
    (x.sign, y.sign) match
      case (1.0, 1.0) => 0
      case (-1.0, _) => 180
      case (1.0, -1.0) => 360

end PolarPoint
```

那么像下面这样的函数是可能的:

```
def drawPolar(point: Point2D & Polarizable): Unit =
  *println*(
    s"""
       |Drawing Point(**$**{point.x},**$**{point.y})
       |with polar distance: **$**{point.distance}
       |and angle: **$**{point.angle}
       |""".stripMargin)
```

无论我们定义哪一类点，该函数都只接受那些扩展点 2d****和*** ***可极化*** 的点。*

## *6:给定/使用而不是隐式值和参数*

*Scala 的*隐式*是 Scala 2 中一个多方面的特性；使用它们，您可以定义以下内容:*

*   *上下文参数*
*   *用新方法扩展现有类型*
*   *隐式转换*

*然而，这意味着关键字 ***隐式*** 有许多用途，并使代码有些混乱，甚至对编译器来说，导致意想不到的效果。这就是为什么 Scala 3 将这个单一特性分成了一组特性:*

*   *扩展方法*(参见第 3 节)**
*   *隐式转换需要显式定义(即需要更多的工作)*
*   *上下文参数用关键字 ***给定*** / ***使用****

*我们将集中讨论后者。回到上面的 ***Point2D*** 示例 trait，假设我们想要让 ***draw*** 方法在不同的设备上绘制一个点，每个设备都被建模为一个***graphic context***，其实现在程序的不同部分可能会有所不同。所以我们这样重新定义我们的绘制方法:*

```
*trait Point2D(val x: Double, val y: Double):
  **def draw(using ctx: GraphicContext): Unit***
```

*注意使用关键字 的 ***表示必须提供一个上下文值。这个值可以在一个类中定义，也可以在一个包/子包中定义为顶级定义，就像我们过去对隐式所做的那样:****

```
*given ctx: GraphicContext = GraphicContext()*
```

*注意 ***给出了*** 关键字来表示一个上下文值。上面的定义也可以写成一个匿名的上下文值，就像这样:*

```
*given GraphicContext = GraphicContext()*
```

## *7:特质参数*

*在 Scala 3 中，特征*可以有参数*，和类一样。例如，上面第 5 节中的例子定义了特征 ***点 2D*** :*

```
*trait Point2D:
  val x: Double
  val y: Double

  def draw: Unit*
```

*相同的特征可以写成:*

```
*trait Point2D(val x: Double, val y: Double):
  def draw: Unit*
```

*和 class PolarPoint 应适当更改为:*

```
*class PolarPoint(
  override val x: Double,
  override val y: Double
  ) extends **Point2D(x,y)** with Polarizable:
// Rest of class definition*
```

*此时，你可以说:*“嗯？为什么不是抽象类 Point2D？”*。我承认这个例子是琐碎的，因为它是用来说教的，但是记住*抽象类只对单一继承*有效，而特征让你*以类似“多重继承”的方式组合*。*

## *8:通用应用方法:不需要“new”关键字*

*正如我们已经知道的，在 Scala 2 中，case 类的实例可以在不使用关键字 ***new*** 的情况下创建，因为编译器向该类添加了一个 ***apply()*** 方法，以及其他自动添加的方法。Scala 3 将一个 ***apply()*** 方法的添加扩展到了*所有的具体类*。这意味着上面代码片段中的类 ***PolarPoint*** 的实例可以简单地创建为:*

```
*val polarPoint = PolarPoint(X_COORD, Y_COORD)*
```

**(btw，X_COORD 和 Y_COORD 来自第 4 节的片段)**

## *9:枚举*

*到目前为止，为了创建枚举，我们不得不求助于 Java ***枚举*** 或第三方库。现在 Scala 3 有了自己的*枚举类型*。我们可以这样定义一个简单的枚举:*

```
*enum Currency:
  case Dollar, Yen, Euro*
```

*或者使用*参数化枚举*获得更多信息:*

```
*enum Currency(val code: String):
  case Dollar extends Currency("USD")
  case Yen extends Currency("JPY")
  case Euro extends Currency("EUR")*
```

*我们可以用刚刚定义的枚举做一些事情:*

```
*val currency = Currency.Dollar*println*(currency.code)
*println*(Currency.valueOf("Yen").ordinal)*
```

*TBD:合计类型*

## *10:参数解耦*

*在我们有元组列表并且需要使用元组的值将它映射到另一个列表的情况下，我们通常有两种方法。第一种方法是像这样访问元组的元素:*

```
*val points = *List*(
  (PolarPoint(100,100), PolarPoint(20,200)),
  (PolarPoint(100,100), PolarPoint(100,200)),
)
val sumPoints = points.map { pointPair =>
 (pointPair._1.x + pointPair._2.x, pointPar._1.y + pointPair._2.y)
}*
```

*然而，在重要的代码中，这被认为是一种不好的做法，因为读者无法知道 _1 或 _2 中有什么。如果生成元组列表的代码不明显，并且有人更改了元组中数据的顺序，情况会变得更糟。因此，我们有第二种选择:用一个*事例去结构化元组:**

```
**val sumPoints = points.map { case (p1, p2) =>
 (p1.x + p2.x, p1.y + p2.y)
}**
```

**好多了，但是现在的情况表明模式匹配如果不彻底可能会失败。Scala 3 提供了更清晰的选择:*参数解耦*。让我们看看前面的代码片段是什么样子的:**

```
**val sumPoints = points.map {
  (p1, p2) => (p1.x + p2.x, p1.y + p2.y)
}**
```

**元组将被非结构化成它的组件，而不使用模式匹配的 ***case*** 。**

# **结论**

**我们已经介绍了 Scala 3 中的 10 个主要变化，如果你在 Scala 3 中开始一个新项目，这些变化将影响你的日常编码任务。当然，还涉及到更多的东西，比如对工具链、库和框架的支持，但是我希望这篇文章能让你对用 Scala 3 编写代码有一个初步的了解。**