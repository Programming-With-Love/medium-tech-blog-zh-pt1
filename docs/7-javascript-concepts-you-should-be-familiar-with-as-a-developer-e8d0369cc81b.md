# 作为开发人员，您应该熟悉的 7 个 JavaScript 概念

> 原文：<https://medium.com/quick-code/7-javascript-concepts-you-should-be-familiar-with-as-a-developer-e8d0369cc81b?source=collection_archive---------0----------------------->

![](img/9e0f58d73b035af5dd2a87da5f384bda.png)

Photo by [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

截至 2022 年，JavaScript 是世界上最常用的语言。95%的网站都在使用它，无论是小公司还是大公司。他们中的一些人正在开发特定的网站或应用程序，需要对这种语言有很强的理解。

javascript 用户可以使用大量的框架和库。如果你能理解 Javascript 的基本原理，你就能很容易地学会这些框架和库。有几个概念让一些开发人员感到困惑和不知所措，但是从长远来看，学习这些 Javascript 概念将使您受益。不仅如此，学习这些 JavaScript 概念将帮助您构建任何类型的应用程序，并学习任何框架和库。

学习 JavaScript 将在 2022 年派上用场。这也会在面试中帮助你。所以事不宜迟，让我们讨论一些每个 javascript 开发人员都应该知道的基本 JavaScript 概念。

# 范围

Scope 代表可变访问。所以，问题是，当代码运行时，我可以访问什么变量？然而，在 Javascript 中，默认情况下，您总是在根范围内(窗口范围)。这些边界限制了变量，并决定了您是否可以访问它们。它将变量的可见性或可用性限制在代码的其他部分。理解这个概念将有助于您在代码中分离逻辑并提高可读性。我们可以用两种方式定义范围:

*   **局部范围:**它可以让你访问盒子内边界内的一切。
*   **全局范围:**它让你在盒子之外访问边界之外的一切。它不能访问在局部范围内定义的变量，除非你返回它，因为它是与外界隔离的。

# 异步和等待

Async & await 本质上是承诺之上的语法糖，它们提供了保持异步操作更加同步运行的方法，就像承诺一样。因此，您可以用几种方式在 javascript 中处理异步操作:

*   ES5 ->回调
*   ES6 ->承诺
*   ES7 ->异步和等待

如果希望等待数据完全加载后再显示，可以使用 Async/Await 执行 Rest API 请求。对于 NodeJS 和浏览器程序员来说，它们是很好的语法改进。它帮助开发人员用 Javascript 实现函数式编程，也使代码更具可读性。

# 复试

当一个函数被调用时，它必须等待另一个函数执行或返回值，从而创建功能链。因此，回调通常用于 javascript 的异步操作中，以提供同步功能。

# 关闭

简单地说，闭包是另一个函数中的一个函数，可以访问外部函数变量。这个定义本身看起来很简单，但是范围使得这个定义很独特。内部函数(闭包)可以访问在它们的作用域(花括号中定义的变量)、它们的父函数和内部全局变量中定义的变量。现在，你需要记住外部函数不能访问内部函数变量。

# 提升

一些开发人员得到了意想不到的结果，因为他们不熟悉 javascript 中的提升概念。如果在 javascript 中定义函数之前调用它，就不会出现“未捕获引用错误”的错误。因此，javascript 解释器在执行代码之前将变量和函数声明提升到当前作用域(函数作用域或全局作用域)之上。

# IIFE(立即调用的函数表达式)

顾名思义，IIFE 是一个 Javascript 函数，当它被定义时，立即被调用和执行。在 IIFE 中声明的变量不能被外界访问，从而防止全局范围被污染。因此，IIFE 主要用于代码的即时执行和数据隐私。

# 承诺

当您需要启动两个或更多的连续操作(或链调用)时，承诺在异步 javascript 操作中非常有用，其中每个后续函数在前一个函数完成后被调用。承诺是一个很快就会产生一个值的对象，可以是一个已解决的值，也可以是无法解决(拒绝)的原因。

承诺可以以三种状态之一存在:

*   **完成:**操作完全成功
*   **被拒绝:**操作失败
*   **待定:**早期状态，既不履行也不拒绝。

感谢您花时间阅读这篇文章。以下是一些更有趣的话题:

[](/quick-code/7-must-know-python-skills-to-advance-in-python-ff752ff760f6) [## 学习 Python 的 7 个必备技巧

### 学习这些 Python 技巧来智胜其他开发者，成为更好版本的自己。

medium.com](/quick-code/7-must-know-python-skills-to-advance-in-python-ff752ff760f6) [](/quick-code/most-useful-free-online-courses-from-top-universities-aa47bf0f13a5) [## 顶尖大学最有用的免费在线课程

### 哈佛、斯坦福、麻省理工等顶尖大学的最佳免费课程列表。

medium.com](/quick-code/most-useful-free-online-courses-from-top-universities-aa47bf0f13a5) [](/quick-code/how-to-become-a-data-science-manager-662032d861b0) [## 如何成为一名更好的数据科学管理者？

### 这里有一些对你成为数据科学经理有价值的提示。

medium.com](/quick-code/how-to-become-a-data-science-manager-662032d861b0)