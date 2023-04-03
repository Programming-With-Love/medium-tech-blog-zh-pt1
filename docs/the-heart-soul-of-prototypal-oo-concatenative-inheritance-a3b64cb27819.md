# 原型 OO 的核心和灵魂:串联继承

> 原文：<https://medium.com/javascript-scene/the-heart-soul-of-prototypal-oo-concatenative-inheritance-a3b64cb27819?source=collection_archive---------3----------------------->

![](img/7cd2bb306fea32d42567a5887b031797.png)

jQuery、下划线、Lodash 和 ES6 *`Object`* 实用程序有什么共同点？

1.  它们是 JavaScript 世界中最常用的实用程序库。
2.  它们都包含一个为**串联继承**服务的实用程序。

我最近写了一篇名为[的文章，“掌握 JavaScript 采访:类和原型继承的区别是什么？”](/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9)

后来，我收到了几个问题，询问关于串联继承的更多信息。因为串联继承是 JavaScript 中原型 OO 的核心和灵魂，并且因为我们如此频繁地使用它，我认为我们作为一个社区对它有更深的理解是至关重要的。

串联继承是您从未听说过的最常用的继承技术。让我先回答一些问题:

*   [对，是个东西。](https://en.wikipedia.org/wiki/Prototype-based_programming#Concatenation)
*   不，这不是什么晦涩难懂的技术。请继续阅读非常常见的习语和例子。你可能已经使用了 1000 次，只是不知道它被称为“串联继承”。

在 JS 中，串联继承的本质往往被通用名“mixins”所掩盖。令人困惑的是，“mixins”在其他语言中有其他含义，甚至在一些 JavaScript 库中也有。它还有一些用途，称之为“mixins”会让人感到困惑。由于这些原因，我更喜欢“串联继承”这个更精确的术语。

> "串联继承是原型 OO 的核心和灵魂."

## 什么是串联继承？

**串联继承**是将一个或多个源对象的属性组合成一个新的目标对象的过程。信不信由你，它是 JavaScript 中最常用的继承形式。

## 默认值/覆盖值(> = ES5)

它经常用在普通的 JavaScript 习惯用法中，包括 ES5 版本的 defaults/overrides 模式(注意“mixins”对于这个用例来说是一个容易混淆的名称):

您可以在成千上万个 jQuery 插件中看到 ES5 默认/覆盖模式的例子，其中 *`$。extend()`* 最初在 2006-2010 年间流行起来。

当然，默认/覆盖在 jQuery 世界之外也非常普遍。ES6 引入了参数缺省值，今天它正在迅速取代这种模式。

> 附注:jQuery 仍然是使用最广泛的 JavaScript 库，远远超过其他库，尽管各种 DOM 实现的兼容性有了很大的提高，并且出现了 React 等其他 DOM 抽象，但 jQuery 的受欢迎程度仍在上升。

## 对象组成

串联继承是实现 JavaScript 简化形式的对象组合的最简单方法(这就是大多数人在 JavaScript 中所说的“混合”:

这种复合模式在 2010 年随着下划线开始获得主流使用和流行，广泛采用了来自主干文档 *:* 的 *`Backbone.Events`* mixin

## 原型遗传的两种基本类型

在[“类和原型继承有什么区别？”](/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9)我这样定义原型继承:

> **原型继承:** ***原型是工作对象实例。*** 对象直接继承其他对象。

这个定义可以用 JavaScript 中的两种不同机制来实现，同样来自同一篇文章:

> **原型委托:**在 JavaScript 中，一个对象可以链接到**委托**的原型。如果在对象上找不到属性，查找将被**委托**给**委托原型，**可能有一个到它自己的委托原型的链接，依此类推，直到到达根委托 *`Object.prototype`* 。这是当你附加到一个*` constructor . prototype`*并使用 *`new '实例化时得到的原型。*你也可以使用 *`Object.create()`* 来实现这个目的，甚至可以将这种技术与串联混合使用，以便将多个原型展平为一个委托，或者在创建后扩展对象实例。
> 
> **串联继承:**通过复制源对象属性，从一个对象直接向另一个对象继承特征的过程。在 JavaScript 中，源原型通常被称为**mixin。**从 ES6 开始，这个特性在 JavaScript 中有一个便利的实用程序，叫做 *`Object.assign()`* 。在 ES6 之前，这通常通过下划线/Lodash 的 *`.extend()`* jQuery 的*`＄来完成。extend()`、*等等……上面的合成例子使用了串联继承。

对于那些从未听说过这个的人来说，有些背景:

## 串联遗传的历史

在经常被引用的[“Orlando 条约”](http://web.media.mit.edu/~lieber/Publications/Treaty-of-Orlando-Chapter.pdf)中，复制和委托机制都被认可，它描述了面向对象代码重用的不同方法:

> **同理心**:一个对象分享另一个对象行为的能力，无需明确重新定义。
> 
> **模板:**基于模板创建新对象的能力，模板是一个“千篇一律”的东西，它至少部分保证了新创建对象的特征。
> 
> 模板允许两个对象共享一个公共表单。它们可能嵌入在类中，也可能本身就是对象。

这里需要做一点翻译。本质上，他们说遗传的主要机制是:

1.  “共享行为”的能力，或者将属性查找从目标对象委托给一些已经存在的对象属性，**也称为原型委托**。
2.  基于预定义的行为**创建新实例**的能力，例如通过创建行为的非引用副本来共享行为**，该副本随后成为**实例安全。****

**在基于类的环境**中，您可以使用 [*“设计模式:可重用面向对象软件的元素”*](http://www.amazon.com/gp/product/0201633612?ie=UTF8&camp=213733&creative=393185&creativeASIN=0201633612&linkCode=shr&tag=eejs-20&linkId=WMUILDJNIUXY4NSH) (用于委托)和**类**(用于模板)中的委托模式来实现这些目标。

**在 JavaScript 中，**同理心被内置为**原型委托**。我们不需要“类”来获得新的对象和实例安全，因为我们可以简单地复制一个现有的对象。换句话说，**你可以使用一个现有的对象作为模板**。

因此，在 JavaScript 中:

*   **移情**是通过**委托原型完成的。**
*   **模板**通过从现有对象复制来完成，有时称为**样本原型**。

您还可以动态地扩展一个现有的对象，这将我们引向作为继承机制的连接…

Orlando 条约在“T36 最小模板一节中描述了我们现在所称的**动态对象扩展**:

> “……从同样的意义上说，最小模板是一个 cookie cutter，但是一旦创建，cookie-cut 对象也可以定义其他属性。一个扩展实例——由最小模板生成，然后添加到——没有一个先验的模板。它的后代不能是严格实例，因为它们的类型没有模板。

这描述了可以通过向现有实例添加属性来构建**对象的思想，ad-hoc** :动态对象扩展，它是串联继承的基础。

> "动态对象扩展[…]串联继承的基础."

在 Iain Craig 所著的《面向对象语言的解释》一书中(如果你找到了这本书，我推荐第一版——后来的版本引入了许多令人困惑的印刷错误),描述了在基于原型的 Kevo 语言中与委托结合使用的**串联继承**,这已经成为短语“串联继承”起源的规范语言参考:

> 每个 Kevo 对象可以被认为是与其父对象相关联的所有属性的完整集合。这意味着 Kevo 对象是完全独立的，不需要任何父关系来表达对象间的派生关系。
> 
> “Kevo 对象[…]可以通过指定插槽和方法重新创建。或者，它们可以通过克隆和改造来创造。”

很难找到可用的 Kevo 例子。Kevo 对串联继承的使用是众所周知的，并在 OO 设计学术界被广泛引用，因为其发明者 Antero Taivalsaari 博士发表了原型 OO 研究工作和学术论文。他还[写过 JavaScript](http://lively.cs.tut.fi/publications/TR6-JavaScriptConcatenation-Taivalsaari.pdf%20Dr.%20Antero%20Taivalsaari) 中的串联继承。顺便提一下，Taivalsaari 博士最著名的工作是设计 Java ME，它被用于数十亿台设备。

## 自我联系

原型继承通过自我的影响进入了 JavaScript。像 JavaScript 一样，Self 支持:

*   委托
*   多个祖先的组成
*   动态对象扩展

> Self 是一种基于原型的面向对象语言，其继承和封装的设计源于对继承允许父母成为其子女的共享部分的理解

一些影响 JavaScript 设计的[自己的论文](https://www.cs.ucsb.edu/~urs/oocsb/self/papers/papers.html)可以在网上找到。它们是非常有趣的读物。*(注意 Self 的“槽”类似于 JavaScript 的“属性”。)*

## 串联继承:你从未听说过的最常用的继承

因为动态对象扩展在 JavaScript 中非常常见，而且对于许多常见的 JavaScript 习惯用法来说非常重要，所以它是 JavaScript 中最常用的继承形式。这一点并没有得到广泛认可，因为当人们想到“继承”这个词时，他们并没有想到这个词。

人们已经在“继承”和“类继承”之间建立了非常紧密的联系——以至于他们认不出任何其他形式的继承:即使它更常用。

串联继承是如此普遍和简单，人们每天都在做，甚至没有意识到这一点。

查看所有最常用的实用程序库中便利实用程序的激增: *`jQuery.extend()`、`下划线. extend()`、` lodash.assign()`、*以及现在的内置 *`Object.assign()`* 以了解广泛使用的证据。

## 对继承保持开放的心态

如果你武断地将你对语言术语的理解过多地限制在一个狭窄的定义上，你常常看不到一个更广泛可用的理解，这种理解可以有效地应用于更广泛的问题，并且常常可以应用于对你现有问题的更简单的解决方案。

***Eric Elliott*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(O'Reilly)，以及* [*【学习 JavaScript 通用 App 开发用节点，ES6，&*](https://leanpub.com/learn-javascript-react-nodejs-es6/)*。他为****Adobe Systems*******Zumba Fitness*******【华尔街日报】*******【ESPN*******BBC****等顶级录音师贡献了软件经验*****

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**