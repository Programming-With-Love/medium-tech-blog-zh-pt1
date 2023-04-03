# 为什么要学习 JavaScript 中的函数式编程？

> 原文：<https://medium.com/javascript-scene/why-learn-functional-programming-in-javascript-composing-software-ea13afc7a257?source=collection_archive---------1----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> ***注:*** *这是《作曲软件》丛书****s***[***(现在一本书！)***](https://leanpub.com/composingsoftware) *从基础开始学习 JavaScript 6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！* [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c) *|* [*下一篇>*](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)

忘记你认为自己对 JavaScript 的了解，以初学者的心态来阅读这些材料。为了帮助您做到这一点，我们将从头开始复习 JavaScript 基础知识，就好像您以前从未见过 JavaScript 一样。如果你是初学者，你很幸运。终于有东西从头开始探索 ES6 和函数式编程了！希望所有的新概念都在过程中得到解释——但是不要指望太多的纵容。

如果您是一名经验丰富的开发人员，已经熟悉 JavaScript 或纯函数式语言，那么您可能会认为 JavaScript 是探索函数式编程的有趣选择。把这些想法放在一边，试着用开放的心态去接触材料。您可能会发现 JavaScript 编程还有另一个层次。一个你从来不知道存在的世界。

既然这篇文章被称为“编写软件”，而函数式编程是编写软件的显而易见的方法(使用函数组合、高阶函数等)，您可能会奇怪为什么我不讨论 Haskell、ClojureScript 或 Elm，而讨论 JavaScript。

JavaScript 具有函数式编程所需的最重要的特性:

1.  **第一类函数:**使用函数作为数据值的能力:将函数作为参数传递，返回函数，将函数赋给变量和对象属性。该属性允许更高阶的函数，这允许部分应用、currying 和合成。
2.  **匿名函数和简洁的 lambda 语法:** `x => x * 2`是 JavaScript 中有效的函数表达式。简洁的 lambdas 使得更容易处理高阶函数。
3.  闭包:闭包是一个函数与其词法环境的捆绑。闭包是在函数创建时创建的。当一个函数在另一个函数中定义时，它可以访问外部函数中的变量绑定，即使在外部函数退出之后。闭包是部分应用程序获得固定参数的方式。固定参数是限定在返回函数的闭包范围内的参数。在`add2(1)(2)`中，`1`是`add2(1)`返回的函数中的固定参数。

# JavaScript 缺少什么

JavaScript 是一种多范式语言，这意味着它支持许多不同风格的编程。JavaScript 支持的其他风格包括过程式(命令式)编程(如 C ),其中函数表示可以重复调用以进行重用和组织的指令子例程，面向对象编程，其中对象(而不是函数)是主要的构建块，当然还有函数式编程。多范例语言的缺点是命令式和面向对象的编程往往意味着几乎所有东西都是可变的。

突变是就地发生的数据结构的变化。例如:

```
const foo = {
  bar: 'baz'
};foo.bar = 'qux'; // mutation
```

对象通常需要是可变的，这样它们的属性就可以被方法更新。在命令式编程中，大多数数据结构都是可变的，以支持对象和数组的高效就地操作。

以下是一些函数式语言具有的，而 JavaScript 没有的特性:

1.  在一些 FP 语言中，纯度是由语言强制执行的。不允许使用有副作用的表达式。
2.  **不变性:**有些 FP 语言禁用突变。表达式计算出新的数据结构，而不是改变现有的数据结构，如数组或对象。这听起来可能效率很低，但是大多数函数式语言都使用 trie 数据结构，这种数据结构具有结构共享的特性:意味着旧对象和新对象共享对相同数据的引用。
3.  **递归:**递归是函数为了迭代而引用自身的能力。在许多 FP 语言中，递归是唯一的迭代方式。没有像`for`、`while`或`do`循环这样的循环语句。

**纯度:**在 JavaScript 中，纯度必须通过约定来实现。如果你不是通过组合纯函数来构建大部分应用程序，那么你就不是在使用函数式风格编程。不幸的是，JavaScript 很容易因为意外创建和使用不纯的函数而偏离轨道。

**不变性:**在纯函数式语言中，不变性经常被强制执行。JavaScript 缺乏大多数函数式语言使用的高效、不可变的基于 trie 的数据结构，但有一些库可以提供帮助，包括 [Immutable.js](https://facebook.github.io/immutable-js/) 和 [Mori](https://github.com/swannodette/mori) 。我希望 ECMAScript 规范的未来版本将包含不可变的数据结构。

有迹象显示这带来了希望，比如 ES6 中添加了`const`关键字。不能重新分配用`const`定义的名称绑定来引用不同的值。理解`const`并不代表不可变的*值是很重要的。*

一个`const`对象不能被重新分配来引用一个完全不同的对象，但是它所引用的对象*可以使其属性变异*。JavaScript 也有能力`freeze()`对象，但是这些对象只在根级别被冻结，这意味着一个嵌套的对象仍然可以有其属性变异的属性。换句话说，在我们看到 JavaScript 规范中真正的复合不变量之前，还有很长的路要走。

**递归:** JavaScript 在技术上支持递归，但是大多数函数式语言都有一个特性叫做尾调用优化。尾部调用优化是一个允许递归函数为递归调用重用堆栈帧的特性。

如果没有尾部调用优化，调用堆栈会无限制地增长，并导致堆栈溢出。在 ES6 规范中，JavaScript 在技术上得到了有限形式的尾部调用优化。不幸的是，只有一个主要的浏览器引擎实现了它，并且优化被部分实现，然后随后从 Babel(最流行的标准 JavaScript 编译器，用于编译 ES6 到 ES5 以便在旧浏览器中使用)中删除。

底线:对大型迭代使用递归仍然不安全——即使你小心地在尾部调用函数。

# JavaScript 有哪些纯函数式语言没有的东西

纯粹主义者会告诉你 JavaScript 的可变性是它的主要缺点，这是真的。然而，副作用和突变有时是有益的。事实上，创建最有用的现代应用程序而不产生副作用是不可能的。像 Haskell 这样的纯函数式语言使用副作用，但是使用称为单子的盒子将它们从纯函数中隐藏起来，即使单子表示的副作用不纯，也允许程序保持纯净。

单子的问题在于，尽管它们的用法很简单，但向不熟悉大量例子的人解释单子是什么，有点像向盲人解释“蓝色”是什么颜色。

> *“单子是内函子范畴的幺半群，有什么问题？”~詹姆斯·伊里虚构地引用了菲利普·瓦德勒的话，转述了桑德斯·麦克兰恩的一句真实的话。* [*“一部简短的、不完整的、而且大多是错误的编程语言史”*](http://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html)

通常，戏仿会夸大事情，使有趣的观点更有趣。在上面的引用中，单子的解释实际上是从最初的引用中*简化的*，它是这样的:

> *“在* `*X*` *中的一个单子只是* `*X*` *的内函子范畴中的一个幺半群，其乘积* `*×*` *被内函子的复合和单位内函子的集合所代替。”~桑德斯·麦克兰恩。* [*【工作数学家的范畴】*](https://www.amazon.com/Categories-Working-Mathematician-Graduate-Mathematics/dp/0387984038//ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=de6f23899da4b5892f562413173be4f0)

即便如此，在我看来，对单子的恐惧是弱推理。学习单子的最好方法不是阅读一堆关于这个主题的书和博客文章，而是投入进去并开始使用它们。与函数式编程中的大多数事情一样，难以理解的学术词汇比概念更难理解。相信我，你不必理解桑德斯·麦克兰恩就能理解函数式编程。

虽然它可能不是每种编程风格的绝对理想语言，但毫无疑问，JavaScript 是一种通用语言，旨在供具有各种编程风格和背景的不同人使用。

根据 Brendan Eich 的说法，这从一开始就是故意的。网景公司必须支持两种程序员:

> 组件作者，他们用 C++或(我们希望)Java 编写；以及业余或专业的“脚本编写人员”，他们将编写直接嵌入 HTML 的代码。”

最初的意图是 Netscape 将支持两种不同的语言，脚本语言可能类似于 Scheme(Lisp 的一种方言)。布伦丹·艾希:

> “我被网景公司录用了，并得到了在浏览器中‘做方案’的承诺。”

JavaScript 必须是一种新的语言:

> *“上层工程管理的命令是语言必须“看起来像 Java”。这就排除了 Perl、Python、Tcl 以及 Scheme。”*

所以，布兰登·艾希从一开始就有这样的想法:

1.  浏览器中的方案。
2.  看起来像 Java。

它最终变得更加混乱:

> *“我并不骄傲，但我很高兴我选择了 Scheme-ish 一级函数和 Self-ish(虽然是单数)原型作为主要成分。不幸的是，Java 的影响，尤其是 y2k 日期错误，以及原语与对象的区别(例如，字符串与字符串)*

我要在最终进入 JavaScript 的“不幸”类 Java 特性列表中添加一项:

*   构造函数和`new`关键字，调用和使用语义与工厂函数不同。
*   一个以单祖先`extends`为主要继承机制的`class`关键字。
*   用户倾向于认为`class`是静态类型(其实不是)。

我的建议是:尽可能避免这些。

我们很幸运，JavaScript 最终成为一种如此强大的语言，因为事实证明，脚本方法战胜了“组件”方法(今天，Java、Flash 和 ActiveX 扩展在大量已安装的浏览器中不受支持)。

我们最终得到了一种浏览器直接支持的语言:JavaScript。

这意味着浏览器不那么臃肿，也不那么容易出错，因为它们只需要支持一组语言绑定:JavaScript。您可能认为 WebAssembly 是一个例外，但 WebAssembly 的设计目标之一是使用兼容的抽象语法树(AST)共享 JavaScript 的语言绑定。事实上，第一次演示将 WebAssembly 编译成 JavaScript 的一个子集，称为 ASM.js。

作为 web 平台唯一的标准通用编程语言，JavaScript 在软件史上掀起了最大的语言流行浪潮:

应用程序吃掉了世界，网络吃掉了应用程序，JavaScript 吃掉了网络。

通过[多](http://redmonk.com/sogrady/2016/07/20/language-rankings-6-16/)多[措施](http://stackoverflow.com/research/developer-survey-2016)， [JavaScript](https://octoverse.github.com/) 现在是世界上最流行的编程语言。

JavaScript 不是函数式编程的理想工具，但对于在非常大的分布式团队中构建大型应用程序来说，它是一个很好的工具，在这样的团队中，不同的团队可能对如何构建应用程序有不同的想法。

一些团队可能会专注于脚本胶水，其中命令式编程特别有用。其他人可能专注于构建架构抽象，在这种情况下，一点点(克制的、小心的)面向对象思维可能不是一个坏主意。还有一些人可能采用函数式编程，通过使用纯函数来减少用户操作，从而对应用程序状态进行确定性的、可测试的管理。这些团队的成员都使用相同的语言，这意味着他们可以更容易地交换想法，相互学习，并建立在彼此的工作基础上。

在 JavaScript 中，所有这些想法都可以共存，这让更多的人拥抱 JavaScript，这导致了全球最大的[开源包注册中心](http://www.modulecounts.com/)(截至 2017 年 2 月) [npm](https://www.npmjs.com/) 。

JavaScript 的真正优势在于生态系统中思想和用户的多样性。对于函数式编程纯粹主义者来说，它可能不是绝对理想的语言，但它可能是使用一种可以在你能想象到的几乎所有平台上工作的语言进行协作的理想语言——对于来自其他流行语言(如 Java、Lisp 或 c)的人来说是熟悉的。对于具有任何这些背景的用户来说，JavaScript 可能不会感到非常舒适，但他们可能会觉得*足够舒适，可以快速学习该语言并变得高效。*

我同意 JavaScript 不是函数式程序员的最佳语言。然而，没有任何其他函数式语言可以声称它是一种每个人都可以使用和接受的语言，正如 ES6 所展示的那样:JavaScript 能够并且确实在满足对函数式编程感兴趣的用户的需求方面做得更好。与其放弃 JavaScript 及其几乎被世界上每家公司使用的令人难以置信的生态系统，为什么不拥抱它，并逐渐使它成为更好的软件合成语言呢？

事实上，JavaScript 已经是一种足够好的函数式编程语言，这意味着人们正在使用函数式编程技术用 JavaScript 构建各种有用和有趣的东西。网飞(以及每一个用 Angular 2+构建的应用)使用基于 RxJS 的功能实用程序。[脸书](https://github.com/facebook/react/wiki/sites-using-react)利用 React 中的纯函数、高阶函数、高阶分量等概念来构建脸书和 Instagram。 [PayPal、KhanAcademy、Flipkart](https://github.com/reactjs/redux/issues/310) 使用 Redux 进行状态管理。

他们并不孤单:Angular、React、Redux 和 Lodash 是 JavaScript 应用程序生态系统中的领先框架和库，所有这些都受到函数式编程的严重影响——或者在 Lodash 和 Redux 的情况下，它们都是为了在实际的 JavaScript 应用程序中实现函数式编程模式而构建的。

“为什么是 JavaScript？”因为 JavaScript 是大多数真正的公司用来构建真正的软件的语言。不管你喜不喜欢，JavaScript 已经从 Lisp 那里偷走了“最受欢迎的函数式编程语言”的头衔，而 Lisp 是几十年来的旗手。的确，Haskell 是当今函数式编程概念更合适的标准承担者，但是人们并没有用 Haskell 构建那么多真正的应用程序。

在任何时候，美国都有近 10 万个 JavaScript 职位空缺，而全球范围内则有数十万个。学习 Haskell 会教你很多关于函数式编程的知识，但是学习 JavaScript 会教你很多关于为实际工作构建生产应用的知识。

应用程序吃掉了世界，网络吃掉了应用程序，JavaScript 吃掉了网络。

> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一张*](/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c) *|* [*下一张>*](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)

# 在 EricElliottJS.com 了解更多信息

EricElliottJS.com 会员可以参加互动代码挑战视频课程。如果你还不是会员，今天就注册吧。

***埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com/)*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】********

*他和世界上最美丽的女人享受着与世隔绝的生活方式。*