# 掌握 JavaScript 面试:什么是闭包？

> 原文：<https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36?source=collection_archive---------0----------------------->

![](img/2b2c86dce97adebd87c0c60ffdb6bba5.png)

> “掌握 JavaScript 面试”是一系列的帖子，旨在帮助候选人准备在申请中高级 JavaScript 职位时可能遇到的常见问题。这些是我在真实面试中经常用到的问题。

我要用 4 万美元的问题来启动这个系列。如果你答错了这个问题，很有可能你不会被录用。如果你被录用了，不管你作为软件开发人员工作了多久，你都有很大的机会被录用为初级开发人员。平均而言，初级开发人员的年薪比更有经验的软件工程师少 4 万美元。

闭包之所以重要，是因为它们控制着特定函数中什么在范围内，什么不在范围内，以及哪些变量在同一个包含范围内的兄弟函数之间共享。理解变量和函数之间的关系对于理解代码中发生的事情至关重要，无论是函数式还是面向对象的编程风格。

在面试中错过这个问题非常不利的原因是，对闭包如何工作的误解是一个非常明显的危险信号，可以显示出缺乏深刻的经验，不仅仅是在 JavaScript 中，在任何大量依赖闭包的语言(Haskell、C#、Python 等)中都是如此。

在不理解闭包的情况下用 JavaScript 编码就像在不理解语法规则的情况下试图说英语一样——你也许能把自己的想法表达出来，但可能有点笨拙。

当你试图理解别人写的东西时，你也很容易被误解。

你不仅应该知道闭包是什么，还应该知道它为什么重要，并且能够很容易地回答闭包的几种可能的用例。

闭包**经常在 JavaScript** 中用于对象数据隐私，在事件处理程序和回调函数中，以及在[部分应用程序、currying](/javascript-scene/curry-or-partial-application-8150044c78b8#.l4b6l1i3x) 和其他函数式编程模式中使用。

> 如果你不能回答这个问题，你可能会失去这份工作，或者每年约 4 万美元。

准备好快速跟进:“你能说出闭包的两种常见用法吗？”

## 什么是终结？

一个**闭包**是一个函数与对其周围状态的引用捆绑在一起(封闭)的组合**词法环境**。换句话说，闭包允许您从内部函数访问外部函数的范围。在 JavaScript 中，闭包是在每次创建函数时创建的。

要使用闭包，请在另一个函数中定义一个函数并公开它。若要公开函数，请返回它或将它传递给另一个函数。

即使在外部函数返回之后，内部函数也可以访问外部函数范围内的变量。

## 使用闭包(示例)

其中，闭包通常用于给对象数据提供隐私。数据隐私是帮助我们编写接口程序的一个基本属性，而不是一个实现。这是一个重要的概念，有助于我们构建更健壮的软件，因为实现细节比接口契约更有可能改变。

> "编程到一个接口，而不是一个实现."
> [设计模式:可重用面向对象软件的元素](http://www.amazon.com/gp/product/B000SEIBB8?ie=UTF8&camp=213733&creative=393177&creativeASIN=B000SEIBB8&linkCode=shr&tag=eejs-20&linkId=CSQYBHTUP625XI4T)

在 JavaScript 中，闭包是实现数据隐私的主要机制。当您使用闭包来保护数据隐私时，所包含的变量只在包含(外部)函数的范围内。除了通过对象的**特权方法**之外，你不能从外部获取数据。在 JavaScript 中，任何在闭包范围内定义的公开方法都是特权的。例如:

[在 JSBin](https://jsbin.com/gareno/edit?html,js,output) 中玩这个。*(看不到任何输出吗？复制并粘贴* [*这个 HTML*](https://gist.github.com/ericelliott/bec3f3824c0ef180f0a8) *到 HTML 窗格中。)*

在上面的例子中，在*“getsecret()”和*的范围内定义了*. get()`*方法，这使得它可以访问来自*“getsecret()”、*的任何变量，并且使它成为一个特权方法。在这种情况下，参数，*【秘密】*。

对象不是产生数据隐私的唯一方法。闭包也可以用来创建**状态函数**，其返回值可能会受到其内部状态的影响，例如:

```
const secret = msg => () => msg;
```

[在 JSBin](https://jsbin.com/bazayo/1/edit?html,js,output) 上可用。*(看不到任何输出吗？复制并粘贴* [*这个 HTML*](https://gist.github.com/ericelliott/bec3f3824c0ef180f0a8) *到 HTML 窗格中。)*

在函数式编程中，闭包经常被用于部分应用& currying。这需要一些定义:

**应用:**将*函数应用于其*参数*以产生返回值的过程。*

**局部应用:**将一个函数应用到*它的一些参数*的过程。部分应用的功能被返回供以后使用**。**部分应用*修复*(将函数部分应用于)返回函数内部的一个或多个参数，返回函数将剩余参数作为参数，以完成函数应用。

部分应用利用了闭包作用域，以便**固定**参数。您可以编写一个通用函数，将部分参数应用于目标函数。它将具有以下签名:

```
partialApply(targetFunction: Function, ...fixedArgs: Any[]) =>
  functionWithFewerParams(...remainingArgs: Any[])
```

如果你需要帮助阅读上面的签名，请查看 [Rtype:阅读函数签名](https://github.com/ericelliott/rtype#reading-function-signatures)。

它将接受一个函数，该函数接受任意数量的参数，后面跟着我们希望部分应用于该函数的参数，并返回一个接受剩余参数的函数。

举个例子会有帮助。假设您有一个将两个数相加的函数:

```
const add = (a, b) => a + b;
```

现在，您需要一个向任意数字加 10 的函数。我们称之为 *`add10()`。**` add 10(5)`*的结果应该是 *`15`* 。我们的 *`partialApply()`* 函数可以做到这一点:

```
const add10 = partialApply(add, 10);
add10(5);
```

在这个例子中，参数 *`10`* 变成了在 *`add10()`* 闭包作用域内记忆的**固定参数**。

让我们来看一个可能的 *`partialApply()`* 实现:

[可在 JSBin](https://jsbin.com/biyupu/edit?html,js,output) 上获得。*(看不到任何输出？将* [*这个 HTML*](https://gist.github.com/ericelliott/bec3f3824c0ef180f0a8) *复制粘贴到 HTML 窗格中。)*

如您所见，它只是返回一个函数，该函数保留了对传递给 *`partialApply()`* 函数的 *`fixedArgs`* 参数的访问。

## 轮到你了

这个帖子有一个视频帖子和 EricElliottJS.com 成员的练习作业。如果你已经是会员，[现在就登录并练习](https://ericelliottjs.com/premium-content/what-is-a-closure/)。

如果你还不是会员，今天就注册吧。

## 探索该系列

*   [什么是闭包？](/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36#.ecfskj935)
*   [类和原型继承有什么区别？](/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9#.h96dymht1)
*   [什么是纯函数？](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976#.4256pjcfq)
*   [什么是函数构成？](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0#.i84zm53fb)
*   [什么是函数式编程？](/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0#.jddz30xy3)
*   [什么是承诺？](/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261#.aa7ubggsy)
*   [软技能](/javascript-scene/master-the-javascript-interview-soft-skills-a8a5fb02c466)

**更新:**

[![](img/649c1c875d8140aa42e9e3d9ffedf8e5.png)](https://ericelliottjs.com/premium-content/lesson-pure-functions)

[Start your free lesson on EricElliottJS.com](https://ericelliottjs.com/premium-content/lesson-pure-functions)

***埃里克·埃利奥特*** *是一位科技产品和平台顾问，著有* [*【排版软件】*](https://leanpub.com/composingsoftware) *，*[*EricElliottJS.com*](https://ericelliottjs.com)*和*[*devanywhere . io*](https://devanywhere.io)*，也是 dev 团队导师。他曾为* ***Adobe Systems、*** ***华尔街日报、*******【ESPN、*******BBC、*** *和顶级录音艺术家包括* ***Usher、弗兰克·奥申、【Metallica*****

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**