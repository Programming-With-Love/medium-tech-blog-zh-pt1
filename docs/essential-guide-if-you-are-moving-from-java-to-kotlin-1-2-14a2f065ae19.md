# 基本指南——如果您正从 Java 迁移到 Kotlin #1/2

> 原文：<https://medium.com/quick-code/essential-guide-if-you-are-moving-from-java-to-kotlin-1-2-14a2f065ae19?source=collection_archive---------0----------------------->

## Kotlin 的一些基本语法，如果您是从 Java 切换过来的，这些语法会很有用。

![](img/a9d63ee45309253ed1183bccbb45d5a0.png)

Image by [andrekheren](https://pixabay.com/users/andrekheren-73289/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=199225) from [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=199225)

K otlin 比 JAVA 或者其他语言简单。虽然 JAVA 语言被 Android 开发者使用，但是 Kotlin 在 Android 社区越来越受欢迎。所以，这篇文章被用作从 Java 语言向 Kotlin 迁移*的基本指南。*

在本文中，我们将讨论 Kotlin 的以下主要主题:

1.  变量
2.  如果语句
3.  when 表达式
4.  for 循环
5.  while 和 do-while 循环
6.  可空值和空检查
7.  功能
8.  班级
9.  构造器
10.  遗产

# 1.变量

创建变量非常容易。创建变量有两个关键字`val`和`var`。

**val** —仅赋值一次

**var** —多次赋值

Variable in Kotlin

# 2.如果语句

在科特林，`if`的说法类似于 JAVA。看看吧…

If Statement in Kotlin

> 注意:没有使用**三元** **运算符**(像，条件？价值 1:价值 2)。

Kotlin 不使用三元运算符，而是使用以下语法:

Ternary like operation in Kotlin

在 Kotlin 中，`if`也可以返回值(我们在上面一点中已经看到)。因此，它也可以用在函数的返回语句中。

If statement with returning value in Kotlin

# 3.当...的时候

`when`表达式被用作代替`switch`的情况(在 Java 中)。`when`表达式可以用来返回值，类似于`if`表达式。

When expression in Kotlin

在科特林，`when`表情有**超能力**。让我们看看这个:

When with Super Power in Kotlin

让我们为输入取一些值，并检查输出:

1.`“Hi”`——“输入是一个字符串”

2.`‘M’` —“输入在 A 到 Z 之间”

3.`6` —“输入在 1 到 10 之间”

# 4.For 循环

`for`循环有多种表示或用例。我在这里讨论了主要的用例。让我们看看一些最受欢迎的使用案例:

1. `in` —最简单的 for 循环，遍历列表中的每个元素

2.`..` —迭代一个范围

3.`withIndex()` —使用当前项目的索引遍历列表。

> 仅供参考:如果你想知道 kotlin 中 for 循环所有可能的语法，那么你应该看看这篇文章:

[](/quick-code/all-you-need-to-know-about-kotlin-for-loop-144dc950271d) [## 科特林——2 分钟“for”循环

### 关于 Kotlin 中的“for”循环，您只需要知道

medium.com](/quick-code/all-you-need-to-know-about-kotlin-for-loop-144dc950271d) 

# 5.While 和 do-while 循环

正如我们所知，`while`和`do-while`循环有类似的用例，如`for`循环，以循环通过某事或某些条件。

`while`循环首先检查条件，然后执行语句/块。

While loop in Kotlin

`do-while`循环首先执行程序块，然后检查条件。

Do-While loop in Kotlin

# 6.可空值和空检查

如果你正从 Java 转换到 Kotlin，这对 Android 开发者来说是一个非常重要的概念。

在 Kotlin 中，当`null`值可能时，引用必须显式地标记为可空。`**?**` 用于表示可能为空的引用或变量。

# 7.功能

在 Kotlin 中，函数/方法的语法与 Java 语言略有不同。

1.  下面的示例使用带有两个 Int 参数和 Int 返回类型的函数:

Function in Kotlin (1)

2.带有表达式体和推断返回类型的函数:

Function in Kotlin (2)

3.函数没有返回有意义的值:

Function in Kotlin (3)

4.在上面的例子中，`Unit`返回类型也可以省略:

Function in Kotlin (4)

# 8.班级

在 Kotlin 中，使用`class`关键字声明类。

类声明由用花括号括起来的类名、类头和类体组成。标题和正文都是可选的；如果类没有主体，可以省略花括号。

Class in Kotlin

# 9.构造器

在 Kotlin 中，使用`constructor`关键字声明构造函数。

主构造函数是类头的一部分:它跟在类名(和可选的类型参数)后面。

Constructor in Kotlin

# 10.遗产

Kotlin 中的所有类都有一个公共超类`Any`，这是没有声明超类型的类的默认超类。

默认情况下，Kotlin 类是`final`，它们不能被继承。要使一个类可继承，用关键字`open`标记它。若要声明显式超类型，请将该类型放在类头中的冒号后面。

Inheritance in Kotlin

我希望，这篇文章能对你有所帮助。感谢阅读！

## 关于作者

你好，我是[认识一下 K . Parmar](https://medium.com/u/a75f2fc00ebf?source=post_page-----14a2f065ae19--------------------------------)。我是一名最后一年的学生，攻读计算机工程。我写关于技术和编程的故事。我喜欢为开源项目做贡献。

帮我接通:[LinkedIn](https://www.linkedin.com/in/meetkparmar/)—[GitHub](https://github.com/meetkparmar)—[Medium](/@meetkparmar)