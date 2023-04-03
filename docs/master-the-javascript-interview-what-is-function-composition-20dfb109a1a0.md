# 掌握 JavaScript 面试:什么是函数合成？

> 原文：<https://medium.com/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0?source=collection_archive---------0----------------------->

![](img/5d7da2f65a2c5b62b559f457abc0cd4e.png)

Google Datacenter Pipes — Jorge Jorquera — (CC-BY-NC-ND-2.0)

> “掌握 JavaScript 面试”是一系列的帖子，旨在帮助候选人准备在申请中高级 JavaScript 职位时可能遇到的常见问题。这些是我在真实面试中经常用到的问题。

函数式编程正在接管 JavaScript 世界。就在几年前，很少有 JavaScript 程序员知道什么是函数式编程，但是我在过去 3 年中看到的每个大型应用程序代码库都大量使用了函数式编程思想。

**功能组合**是将两个或两个以上的功能组合起来产生一个新功能的过程。将函数组合在一起就像将一系列管道连接在一起，让我们的数据流过。

简单地说，函数` *f`* 和` *g`* 的组合可以定义为 *`f(g(x))`，*从内到外(从右到左)求值。换句话说，评估顺序是:

1.  *`x`*
2.  *`g`*
3.  *`f`*

让我们在代码中更仔细地看看这一点。假设您想要将用户的全名转换为 URL slugs，以便为每个用户提供一个个人资料页面。为此，您需要完成一系列步骤:

1.  将名称拆分成空格数组
2.  将名称映射为小写
3.  用破折号连接
4.  编码 URI 分量

下面是一个简单的实现:

不错…但是如果我告诉你它可以更具可读性呢？

想象一下，这些操作中的每一个都有相应的可组合函数。这可以写成:

这看起来比我们的第一次尝试更难理解，但是坚持住，这是有意义的。

为了实现这一点，我们使用了常见实用程序的可组合形式，如 *`split()`* 、 *`join()`* 和 *`map()`* 。以下是实现方式:

除了 *`toLowerCase()`，*所有这些函数的生产测试版本都可以从 Lodash/fp 获得。您可以像这样导入它们:

```
import { curry, map, join, split } from 'lodash/fp';
```

或者像这样:

```
const curry = require('lodash/fp/curry');
const map = require('lodash/fp/map');
//...
```

我有点懒了。注意，这个咖喱从技术上讲并不是真正的咖喱，它总是产生一元函数。相反，它是一个简单的局部应用程序。见[“库里和局部应用有什么区别？”](/javascript-scene/curry-or-partial-application-8150044c78b8#.13tj19278)，但是为了*这个演示的目的*，它将与一个真正的 curry 函数互换工作。

回到我们的 *`toSlug()`* 实现，有一些事情真的让我很困扰:

对我来说，这看起来像很多嵌套，读起来有点混乱。我们可以用一个自动组合这些函数的函数来简化嵌套，这意味着它将从一个函数获取输出，并自动将其修补到下一个函数的输入，直到它吐出最终值。

仔细想想，我们有一个 array extras 实用程序，听起来像是做类似的事情。它接受一个值列表，并对每个值应用一个函数，累积一个结果。这些值本身可以是函数。这个函数叫做 *`reduce()`，*但是为了匹配上面的合成行为，我们需要它从右向左减少，而不是从左向右。

好在有一个 *`reduceRight()`* 正是我们要找的:

和 *`.reduce()`* 一样，数组 *`.reduceRight()`* 方法采用一个 reducer 函数和一个初始值(` *x`* ) *。*我们迭代数组函数(从右到左)，依次将每个函数应用于累积值( *`v`* )。

有了 compose，我们可以在没有嵌套的情况下重写上面的合成:

当然， *`compose()`* 也附带了 lodash/fp:

```
import { compose } from 'lodash/fp';
```

或者:

```
const compose = require('lodash/fp/compose');
```

当你从里到外从数学形式的角度思考写作时，写作是很棒的…但是如果你想从左到右的顺序来思考呢？

还有一种形式俗称 *`pipe()`* 。Lodash 称之为 *`flow()`:*

注意实现与 *`compose()`* 完全相同，除了我们使用 *`.reduce()`* 而不是 *`.reduceRight()`，*从左到右而不是从右到左减少。

让我们看看我们用 *`pipe()`:* 实现的 *`toSlug()`* 函数

对我来说，这更容易阅读。

核心函数式程序员根据函数组合来定义他们的整个应用程序。我经常使用它来消除对临时变量的需求。仔细看看 *`pipe()`* 版本的 *`toSlug()`* ，你可能会注意到一些特别的地方。

在命令式编程中，当您对某个变量执行转换时，您会在转换的每一步中找到对该变量的引用。上面的 *`pipe()`* 实现是以**无点**风格编写的，这意味着它根本不识别它所操作的参数。

我经常在单元测试和 Redux 状态减少器中使用管道，以消除对中间变量的需要，这些变量只用于保存一个操作和下一个操作之间的瞬态值。

起初这听起来可能很奇怪，但是随着您对它的实践，您会发现在函数式编程中，您正在处理非常抽象、一般化的函数，其中事物的名称并不那么重要。名字只会碍事。你可能开始认为变量是不必要的样板文件。

也就是说，我认为免分的风格可能走得太远了。它可能会变得过于密集，难以理解，但如果你感到困惑，这里有一个小提示…你可以进入流来跟踪发生了什么:

你可以这样使用它:

*`trace()`* 只是更一般的 *`tap()`* 的一种特殊形式，它让您对流经管道的每个值执行一些操作。明白了吗？烟斗？踢踏舞？你可以这样写 *`tap()`* :

现在你可以看到 *`trace()`* 只是一个特例 *`tap()`* :

你应该开始了解函数式编程是什么样子，以及**局部应用** & **如何与**函数组合**协作**来帮助你用更少的样板文件编写更具可读性的程序。

## 探索该系列

*   [什么是闭包？](/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36#.ecfskj935)
*   [类和原型继承有什么区别？](/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9#.h96dymht1)
*   [什么是纯函数？](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976#.4256pjcfq)
*   [什么是函数构成？](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0#.i84zm53fb)
*   [什么是函数式编程？](/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0#.jddz30xy3)
*   什么是承诺？
*   [软技能](/javascript-scene/master-the-javascript-interview-soft-skills-a8a5fb02c466)

# 提升你的技能

[跟着 Eric Elliott](http://ericelliottjs.com/product/lifetime-access-pass/) 学习 JavaScript。如果你不是会员，你就错过了！

[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有《编程 JavaScript 应用程序》* *(奥赖利)，以及《高级 JavaScript 与开发领导力课程》。他为 Adobe Systems******Zumba Fitness*******【华尔街日报】*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他和世界上最美丽的女人一起在任何他想去的地方工作。**