# 熟悉性偏见阻碍了你:是时候接受箭头功能了

> 原文：<https://medium.com/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75?source=collection_archive---------0----------------------->

![](img/4583a18b7733e6400c418c6ec99a2333.png)

“Anchor” — Actor212 — (CC BY-NC-ND 2.0)

我以教 JavaScript 为生。最近，我调整了我的课程，以便更快地教授课程化的 arrow 函数——在最初的几课中。我把它放在了课程的前面，因为它是一个非常有价值的技能，而且学生们学会用箭头奉承的速度比我想象的要快得多。

如果他们能早一点理解并利用它，为什么不早一点教呢？

> 注意:我的课程不是为从未接触过代码的人设计的。大多数学生在花了至少几个月的时间编写代码后加入——自己编写，在训练营编写，或者专业编写。然而，我看到许多经验很少或没有经验的初级开发人员很快就学会了这些话题。

我看到一群学生在一个小时的课程中就对 curried arrow 函数非常熟悉。(如果你是[“跟随 Eric Elliott 学习 JavaScript”](https://ericelliottjs.com/product/lifetime-access-pass/)的成员，你现在就可以观看 55 分钟的[ES6 Curry&Composition](https://ericelliottjs.com/premium-content/es6-curry-composition/)课程)。

看到学生们如此迅速地掌握它，并开始施展他们新发现的咖喱能力，当我在 Twitter 上发布咖喱箭头功能时，我总是有点惊讶，Twitterverse 对将“不可读”的代码强加给需要维护它的人的想法表示愤慨。

首先，让我给你举一个我们正在谈论的例子。我第一次注意到这种反弹是 Twitter 对这个功能的回应:

```
const secret = msg => () => msg;
```

当 Twitter 上的人指责我试图混淆视听时，我感到震惊。我写这个函数是为了演示在 ES6 中表达定制函数是多么容易。这是我在 JavaScript 中能想到的最简单的闭包的实际应用和表达。(相关:[“什么是闭包？”](/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36))。

它相当于下面的函数表达式:

```
const secret = function (msg) {
  return function () {
    return msg;
  };
};
```

`secret()`是一个接受一个`msg`并返回一个新函数的函数，该新函数返回一个`msg`。它利用闭包将`msg`的值固定为传递给`secret()`的任何值。

你可以这样使用它:

```
const mySecret = secret('hi');
mySecret(); // 'hi'
```

事实证明,“双箭”正是让人困惑的地方。我确信这是一个事实:

> 众所周知，内嵌箭头函数是用 JavaScript 表达定制函数的最易读的方式。

许多人向我争辩说长格式比短格式更容易阅读。他们部分是对的，但大部分是错的。它更加冗长，更加明确，但是不容易阅读——至少对于熟悉箭头功能的人来说是这样。

我在 Twitter 上看到的反对意见与我的学生们正在享受的顺利学习体验并不一致。根据我的经验，学生们喜欢咖喱箭头功能就像鱼喜欢水一样。在学习它们的几天内，它们就和箭融为一体了。他们毫不费力地解决各种各样的编码挑战。

我看不出任何迹象表明 arrow 函数对他们来说是“难以”学习、阅读或理解的——一旦他们在几个 1 小时的课程和研究会议中进行了学习它们的初始投资。

他们轻松地阅读他们从未见过的 curried arrow 函数，并向我解释这是怎么回事。当我向他们提出挑战时，他们自然会写他们自己的。

换句话说，一旦他们**熟悉**看到 curried arrow 函数，他们**就不会有麻烦了**。他们读起来就像你读这句话一样容易——他们的理解反映在更简单、错误更少的代码中。

# 为什么有些人认为遗留函数表达式看起来“更容易”阅读

熟悉偏差是一种可测量的人类认知偏差，它导致我们做出自我毁灭的决定，尽管我们知道有更好的选择。尽管出于舒适和习惯，我们知道更好的模式，但我们还是继续使用同样的旧模式。

你可以从这本优秀的书[“撤销项目:改变我们想法的友谊”](https://www.amazon.com/Undoing-Project-Friendship-Changed-Minds-ebook/dp/B01GI6S7EK/ref=as_li_ss_tl?ie=UTF8&qid=1492606452&sr=8-1&keywords=the+undoing+project&linkCode=ll1&tag=eejs-20&linkId=4ebd1476f97023e8acb4bba37ea18b90)中学到更多关于熟悉偏见(以及我们愚弄自己的许多其他方式)的知识。这本书应该是每个软件开发人员的必读之作，因为它鼓励你更批判地思考并测试你的假设，以避免陷入各种各样的认知陷阱——这些认知陷阱是如何被发现的故事也非常好。

# 遗留函数表达式可能会导致代码中的错误

今天我把一个 curried arrow 函数从 ES6 重写到 ES5，这样我就可以把它作为一个开源模块发布，人们可以在旧的浏览器中使用它，而不需要编译。ES5 版本让我震惊。

ES6 版本简单、简短、优雅，只有 4 行。

我肯定地认为，**这个**函数将向 Twitter 证明 arrow 函数是优越的，并且人们应该像他们的坏习惯一样放弃他们的传统函数。

所以我发微博说:

以下是这些函数的文本，以防图片对您不起作用:

```
// curried with arrows
const composeMixins = (...mixins) => (
  instance = {},
  mix = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x)
) => mix(...mixins)(instance);// vs ES5-style
var composeMixins = function () {
  var mixins = [].slice.call(arguments); return function (instance, mix) {
    if (!instance) instance = {}; if (!mix) {
      mix = function () {
        var fns = [].slice.call(arguments); return function (x) {
          return fns.reduce(function (acc, fn) {
            return fn(acc);
          }, x);
        };
      };
    } return mix.apply(null, mixins)(instance);
  };
};
```

正在讨论的函数是围绕`pipe()`的一个简单包装器，这是一个标准的函数式编程实用程序，通常[用于组合函数](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)。一个`pipe()`函数在 lodash 中以`lodash/flow`的形式存在，在 Ramda 中以`R.pipe()`的形式存在，甚至在几种函数式编程语言中都有自己的运算符。

熟悉[函数式编程](/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0)的人应该都很熟悉。其主要依赖关系也应如此:[减少](/javascript-scene/reduce-composing-software-fe22f0c39a1d)。

在这种情况下，它被用来组成功能混合，但这是一个不相关的细节(和一个完整的其他博客帖子)。以下是重要的细节:

该函数接受任意数量的函数混合，并返回一个函数，该函数在管道中一个接一个地应用它们——就像装配线一样。每个函数 mixin 都将`instance`作为输入，并在传递给管道中的下一个函数之前添加一些东西。

如果您省略了`instance`，将为您创建一个新对象。

有时，我们可能希望以不同的方式编写混音。例如，您可能想通过`compose()`而不是`pipe()`来颠倒优先顺序。

如果您不需要定制行为，您只需保留默认行为，并获得标准的`pipe()`行为。

# 只是事实

撇开可读性的观点不谈，下面是与这个例子相关的客观事实:

*   我在 ES5 和 ES6 函数表达式、箭头或其他方面有多年的经验。熟悉度偏差不是这个数据中的一个变量。
*   我几秒钟就写出了 ES6 版本。它没有包含任何错误(据我所知，它通过了所有的单元测试)。
*   写 ES5 版本花了我几分钟时间。至少多一个数量级的时间。分钟对秒钟。我两次失去了在函数缩进中的位置。我写了 3 个 bug，都是我必须调试和修复的。其中两次我不得不求助于`console.log()`来弄清楚发生了什么。
*   ES6 版本有 4 行代码。
*   ES5 版本有 21 行长(实际上有 17 行包含代码)。
*   尽管冗长乏味，ES5 版本实际上失去了 ES6 版本中的一些信息保真度。长了很多，但是**交流少了**，继续看详情。
*   ES6 版本包含 2 个功能参数范围。ES5 版本省略了 spreads，转而使用了*隐式* `arguments`对象，这伤害了函数签名的可读性(保真度降级 1)。
*   ES6 版本在函数签名中定义了`mix`的默认值，因此您可以清楚地看到它是一个参数值。ES5 版本模糊了这个细节，而是将它隐藏在函数体的深处。(保真度降级 2)。
*   ES6 版本只有两级缩进，这有助于阐明应该如何阅读的结构。ES5 版本有 6 个，嵌套层次模糊了而不是增加了函数结构的可读性(保真度降级 3)。

在 ES5 版本中，`pipe()`占据了函数体的大部分——以至于内联定义它有点疯狂。实际上**需要将**分解成一个独立的函数，以使 ES5 版本可读:

```
var pipe = function () {
  var fns = [].slice.call(arguments); return function (x) {
    return fns.reduce(function (acc, fn) {
      return fn(acc);
    }, x);
  };
};var composeMixins = function () {
  var mixins = [].slice.call(arguments); return function (instance, mix) {
    if (!instance) instance = {};
    if (!mix) mix = pipe; return mix.apply(null, mixins)(instance);
  };
};
```

在我看来，这显然更具可读性和可理解性。

让我们看看当我们对 ES6 版本应用同样的可读性“优化”时会发生什么:

```
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);const composeMixins = (...mixins) => (
  instance = {},
  mix = pipe
) => mix(...mixins)(instance);
```

像 ES5 优化一样，这个版本更加冗长(它添加了一个以前没有的新变量)。与 ES5 版本不同，在抽象出管道的定义后，这个版本的可读性并没有明显提高。毕竟，它已经在函数签名: `mix`中有一个明确分配给它的变量名*。*

`mix`的定义已经包含在它自己的行中，这使得读者不太可能弄不清它在哪里结束，而函数的其余部分继续。

现在我们有两个变量代表同样的东西，而不是一个。我们收获很大吗？不是很明显，不是。

那么为什么 ES5 版本**在抽象出相同功能的情况下**明显更好呢？

因为 ES5 版本显然更复杂。那种复杂性的来源是这件事的症结所在。我断言复杂性的来源归结于**语法噪声**，并且语法噪声**模糊了函数**的含义，没有帮助。

让我们换个方式，消除更多的变量。两个例子都用 ES6，只比较*箭头函数*和*遗留函数表达式:*

```
var composeMixins = function (...mixins) {
  return function (
    instance = {}, mix = function (...fns) {
      return function (x) {
        return fns.reduce(function (acc, fn) {
          return fn(acc);
        }, x);
      };
    }
  ) {
    return mix(...mixins)(instance);
  };
};
```

对我来说，这看起来可读性更强。我们所做的改变是，我们利用了 **rest** 和**默认参数**语法。当然，你必须熟悉 rest 和默认语法，这样这个版本才更易读，但是即使你不熟悉，我认为很明显这个版本还是不那么混乱。

这很有帮助，但对我来说，这个版本仍然很混乱，将`pipe()`抽象成它自己的函数**显然会有帮助:**

```
const pipe = function (...fns) {
  return function (x) {
    return fns.reduce(function (acc, fn) {
      return fn(acc);
    }, x);
  };
};// Legacy function expressions
const composeMixins = function (...mixins) {
  return function (
    instance = {},
    mix = pipe
  ) {
    return mix(...mixins)(instance);
  };
};
```

好多了，对吧？既然`mix`赋值只占用了一行，函数的结构就清晰多了——但是对我来说还是有太多的语法干扰。在`composeMixins()`中，一个功能的结束和另一个功能的开始对我来说不是一目了然的。

这个`function`关键字似乎在视觉上与及其周围的标识符融合在一起，而不是调用函数体。我的函数里有函数**隐藏**！参数签名在哪里结束，函数体在哪里开始？我仔细看就能搞清楚，但视觉上对我来说不明显。

如果我们可以去掉函数关键字，用一个大的**`**=>**`**箭头直观地指向返回值，而不是编写一个与周围标识符融为一体的`return`关键字，会怎么样？****

****事实证明，我们可以，这看起来是这样的:****

```
**const composeMixins = (...mixins) => (
  instance = {},
  mix = pipe
) => mix(...mixins)(instance);**
```

****现在应该清楚是怎么回事了。`composeMixins()`是一个接受任意数量`mixins`的函数，并返回一个接受两个可选参数`instance`和`mix`的函数。它通过组合的`mixins`返回管道`instance`的结果。****

****还有一件事…如果我们对`pipe()`应用同样的优化，它会神奇地变成一行程序:****

```
**const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);**
```

****有了一行的定义，将它抽象成自己的函数的优势就不那么明显了。记住，这个函数作为一个实用程序存在于 Lodash、Ramda 和许多其他库中，但是它真的值得导入另一个库吗？****

****甚至值得把它拉出来自成一行吗？大概吧。它们实际上是两种不同的功能，将它们分开会使这一点更加清楚。****

****另一方面，当您查看参数签名时，将它内联可以澄清类型和使用预期。下面是我们内联创建它时发生的情况:****

```
**const composeMixins = (...mixins) => (
  instance = {},
  mix = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x)
) => mix(...mixins)(instance);**
```

****现在我们回到最初的函数。一路上，**我们没有丢弃任何意义。**事实上，通过内联声明我们的参数和默认值，我们**添加了关于如何使用函数的信息**，以及参数值可能是什么样子。****

****ES5 版本中所有额外的代码都只是噪音。语法噪音。它没有为**提供任何有用的目的**，除了让不熟悉 curried arrow 功能的人适应一下。****

****一旦你对 curried arrow 函数有了足够的了解，很明显原始版本的可读性更好，因为语法更少了。****

****它也不太容易出错，因为有更少的表面区域让虫子藏起来。****

****我怀疑遗留函数中隐藏着大量的错误，如果你升级到 arrow 函数，这些错误会被发现并消除。****

****我还认为，如果您学会接受和喜欢 ES6 中更多的简洁语法，您的团队将会变得更加高效。****

****虽然有时候事情变得清晰会更容易理解，但一般来说，代码越少越好也是事实。****

****如果更少的代码可以完成同样的事情，并且可以在不牺牲任何意义的情况下进行更多的交流，那就**客观上**更好。****

****知道区别的关键是意义。如果更多的代码不能增加更多的意义，那么这些代码就不应该存在。这是一个非常基本的概念，它是自然语言中众所周知的风格指南。****

****同样的风格指南也适用于源代码。拥抱它，你的代码会更好。****

****在一天结束的时候，黑暗中的一盏灯。作为对另一条称 ES6 版本可读性较差的推文的回应:****

****是时候熟悉 ES6、currying 和函数组合了。****

# ****后续步骤****

****[“跟随 Eric Elliott 学习 JavaScript”](https://ericelliottjs.com/product/lifetime-access-pass/)会员现在就可以观看 55 分钟的 [ES6 库里&作文](https://ericelliottjs.com/premium-content/es6-curry-composition/)课。****

****如果你不是会员，你就错过了！****

****[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)****

*******埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验**********

******他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。******