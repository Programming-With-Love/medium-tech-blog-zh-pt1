# 面向企业 Bee 的高级 FP:状态

> 原文：<https://medium.com/google-developer-experts/advanced-fp-for-the-enterprise-bee-state-4f8fd2d8098b?source=collection_archive---------1----------------------->

![](img/0e61264a5b8e3e3507810f3c0387948f.png)

Bees Collaborating to Build Honeycomb

# 介绍

欢迎阅读我们的八部分系列的倒数第二篇文章，为有实践意识的企业开发人员研究高级函数式编程。本文将对上一篇文章中的用例[进行扩展，在上一篇文章中，我们开发了一组不可变类型来配置](/google-developer-experts/advanced-fp-for-the-enterprise-bee-optics-2ccc444d409b) [JetBrains 空间](https://www.jetbrains.com/space/)的一个实例。这一次，我们正在探索单子的世界，特别是*态*类型。一如既往，样本代码在本报告中可用[。](https://bitbucket.org/instilco/advanced-fp-gde/src/master/)

# “M”的房子

就像漫画里的[一样，M 的房子是软件工程里一个吓人的地方。这是一个令人不安的交替现实——有着奇怪名字和神秘能力的外星人似乎已经控制了局面。幸运的是，现实要平庸得多。](https://en.wikipedia.org/wiki/House_of_M)

为了编写软件，我们需要用代码来表示事物。客户、订单、交付、汽车、牲畜等等。但是，因为编程是一个复杂的职业，我们不能总是直接使用这些类型。相反，我们需要根据预期的复杂情况，将它们包装在各种容器中。

这些容器中的一些符合单子的条件。最简单的是 *Identity* (或 *Id* )类型，它包装一个值而不添加任何功能。例如，这是格罗古的[身份单子](https://en.wikipedia.org/wiki/Monad_(functional_programming)#Identity_monad):

![](img/6855fa141b2df1eac2ce8808ab869aa2.png)

我们可以用一些真实世界的例子来使单子的概念更加具体。假设我们有一个客户 ID，需要从数据库中获取匹配的订单:

*   如果客户可能没有订单，我们使用*选项<订单>*
*   如果可能有很多订单我们使用一个集合，比如*列表<订单>列表*
*   为了管理错误，我们可以使用*尝试<命令>命令*或*命令<错误，命令>命令*
*   对于某些订单，如果只有总值可用，我们可以使用*Double<，Order >*
*   如果订单经过一系列检查，其中任何一项都可能失败，我们可以使用一个经过*验证的<列表<字符串>，订单>*
*   为了推迟查询数据库，我们可以将查询代码包装在一个 *Eval* 或 *IO 中。*我们这样做可能是为了批量查询性能。

这些容器单体的特点是它们是通用的，可以根据一组规则进行组合，并实现某些功能(如 *flatMap* )。最有用的是，对于工作的开发人员来说，返回单子的函数可以通过统一的过程方便地组合起来。

为了更好地理解这是如何工作的，我们将把*状态单子*应用到我们现有的代码库中，以配置 JetBrains 空间的实例。状态是更抽象和深奥的单子之一，所以如果我们能理解它，那么剩下的应该不会有什么困难。

# 介绍状态单子

*状态*类型用于以下功能:

*   需要一些信息(状态)。
*   返回状态的修改版本和结果。

如果 S 表示状态的类型，R 表示结果，那么函数的签名是 *S = > (S，R)。通常我们想要组合多个函数，其中每个调用都需要前一个调用返回的状态。*

在上一篇文章的案例研究中，我们使用一些粗略且现成的`toString`方法生成了输出。让我们用使用*状态*类型的代码来替换它。我们的函数使用的状态将是一个`Instance`对象，它在 JetBrains 空间的一个实例中对项目、配置文件、存储库和博客进行建模。返回值将是一个`List<String>`，它保存我们想要打印的文本行。

下面的代码显示了一个使用*状态*类型的`displayInstance`方法。我们正在组合对`displayTitle`、`displayProfiles`、`displayProjects`和`displayBlogs`的调用。每个人负责处理一个`Instance`的一个部分。如前所述，它们接受一个`Instance`并返回一个字符串列表:

`fx`块中的函数组合负责将状态传递给每个函数。所以我们需要做的就是将结果捕捉到局部变量中。在块的末尾，我们将所有的返回值合并并平铺到一个列表中。然后通过`foreach`和`println`输出。

注意，我们需要调用`runA`来触发该块，并将初始状态作为参数传递。此时，我们需要将结果封装在一个`Id`单子中，并调用`fix`来执行向下转换。当*状态*类型成为 Arrow 的正式部分时，这两者都不需要。

我们可以以通常的方式调用`displayInstance`，输出是我们空间配置的完整描述:

```
Kotlin Programming for MegaCorp
 Profiles are:
  Pete Smith at [Pete.Smith@megacorp.com](mailto:Pete.Smith@megacorp.com)
  Jane Jones at [Jane.Jones@megacorp.com](mailto:Jane.Jones@megacorp.com)
 Projects are:
  Project 'Course Exercises' (PROJ202) with repos:
    [http://elsewhere.com](http://elsewhere.com)
  Project 'Course Examples' (PROJ101) with repos:
    [http://somewhere.com](http://somewhere.com)
 Blogs are:
  Setting Up
   setup.md
   More client-specific content
  Welcome to the Course
   welcome.md
   Some additional client-specific content
```

# 检查显示功能

下面是最简单的显示功能:

正如你所看到的，我们使用了“函数作为值”的风格，其中一个不可变的变量指的是一个 Lambda。我们使用`State`类型来指定输入和输出。这里的输入是`Instance`，而输出是一个单项列表，通过`toT`与`Instance`结合。

`displayProfiles`功能稍微复杂一些。对于实例中的每个配置文件，我们需要生成一个包含用户名和电子邮件的字符串。这可以通过调用`map`来实现。我们使用`cons`光学元件预先计划一个标题字符串，然后通过`toT`将其与`Instance`组合。

最后两个函数具有相同的结构，但是我们需要处理嵌套的内容。在`displayProjects`的例子中，它是存储库。在`displayBlogs`的情况下，它是附加内容:

# 使用状态构建实例

前面的例子展示了使用*状态*类型，加上一元合成，来输出我们的`Instance`的描述。然而，我们在任何时候都没有修改状态。

为了说明这一点，让我们重写我们的`createInstance`方法来使用 state 类型:

状态将再次成为一个`Instance`对象。但是随着函数调用的进行，我们会逐渐增加它。请记住，这种类型是不可变的，所以每次变异都会返回一个新的实例。

对于我们的结果类型，我们将使用一个简单的字符串，它描述了已经实现的内容。一旦所有的方法都被调用，我们在调用`displayInstance`之前组合结果并打印出来:

```
**Created Projects Created Profiles Created Blogs**
Kotlin Programming for MegaCorp
 Profiles are:
  Pete Smith at [Pete.Smith@megacorp.com](mailto:Pete.Smith@megacorp.com)
  Jane Jones at [Jane.Jones@megacorp.com](mailto:Jane.Jones@megacorp.com)
 Projects are:
  Project 'Course Exercises' (PROJ202) with repos:
    [http://elsewhere.com](http://elsewhere.com)
  Project 'Course Examples' (PROJ101) with repos:
    [http://somewhere.com](http://somewhere.com)
 Blogs are:
  Setting Up
   setup.md
   More client-specific content
  Welcome to the Course
   welcome.md
   Some additional client-specific content
```

下面是`copyProfiles`的声明。请注意，这是一个高阶函数。我们传入`DslInstance`并返回一个生成的函数，该函数接受一个`Instance`并返回一个`State<Instance, String>`。使用`toT`助手方法来组合修改后的`Instance`和`String`报告。新的`Instance`将被传递给下一个函数，而结果存储在`log1`局部变量中。

`copyProjects`和`copyBlogs`方法类似，但更复杂，因为它们需要创建嵌套内容:

# 结论

在这一系列文章中，我们一直在使用单子，但没有特别提到它。Monad 是包装一个或多个值以提供额外功能的容器。像`Either`、`Validated`和`State`这样的一元类型是由 Arrow (Kotlin)和 Cats (Scala)这样的函数库提供的。虽然每个单子都是容器，但不是每个容器都是单子。例如，未来不是一元的，因为它是不确定的。

单子最巧妙的一点是能够链接函数调用，并为您处理组合的机制。在`State`类型的情况下，每个调用返回一对结果和状态的新版本。我们希望将状态传递给下一个调用，并存储结果以备后用。在链的末端，我们可以传递结果和/或最终状态。

# 谢谢

继续感谢 [Richard Gibson](https://twitter.com/rickityg) 和 [Instil 培训团队](https://instil.co/training/team/)对这一系列文章的评论、评论和鼓励。