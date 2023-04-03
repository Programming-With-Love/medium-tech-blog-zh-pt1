# 作为一种策略，你能避免函数式编程吗？

> 原文：<https://medium.com/javascript-scene/can-you-avoid-functional-programming-as-a-policy-7bd0570bcfb2?source=collection_archive---------0----------------------->

![](img/6d6e21b514d05d5c22146daf734a0c56.png)

Policy by [Nick Youngson](http://www.nyphotographic.com/) [CC BY-SA 3.0](http://creativecommons.org/licenses/by-sa/3.0/) [Alpha Stock Images](http://alphastockimages.com/)

函数式编程已经渗透到大多数主流编程领域——JS 生态系统的大部分，C#的 Linq，甚至 Java 中的高阶函数。这是 2018 年的 Java:

```
*getUserName(*users, user -> user.getUserName*())*;
```

FP 是如此有用和方便，它已经在我所能想到的主流现代编程语言中找到了自己的路。

但不全是彩虹和小狗。许多开发人员都在努力应对我们对软件的看法的这种结构性转变。坦率地说，现在很难找到一份不需要熟悉函数式编程概念的 JavaScript 工作。

FP 位于两个主流框架 React(避免共享可变 DOM 是其架构和单向数据流的动机)和 Angular (RxJS 是通过高阶函数作用于流的实用操作符库——在 Angular 中广泛使用)的核心。Redux 和 ngrx/store 也是如此:在这两方面都起作用。

对于刚接触函数式编程的开发人员来说，这可能是一个令人生畏的话题，为了快速入门，你团队中的一些人可能会建议你通过**避免使用函数式编程作为策略，让你的团队更容易适应代码库。**

对于不熟悉 FP 是什么，或者它在现代代码生态系统中的普遍性的管理者来说，这听起来可能是一个明智的建议。毕竟 30 年来 OOP 不是很好的服务了软件世界吗？为什么不继续这样做呢？

让我们暂时考虑一下这个想法。

“禁止”计划生育*作为一项政策意味着什么？*

# 什么是函数式编程？

我最喜欢的函数式编程的定义是:

**函数式编程**是一种编程范式，使用**纯函数**作为组合的原子单位，避免了*共享可变状态*和*副作用。*

纯函数是这样的函数:

*   给定相同的输入，总是返回相同的输出
*   没有副作用

FP 的本质可以归结为:

*   **带功能的程序**
*   **避免共享可变状态&副作用**

当你把这些放在一起时，你得到了使用纯函数作为你的构建块的软件开发(“组成的原子单位”)。

顺便提一下，根据所有现代面向对象程序设计的倡导者艾伦·凯的观点，面向对象程序设计的本质是:

*   **封装**
*   **消息传递**

所以 OOP 只是避免共享可变状态和副作用的另一种方法。

显然，FP 的反义词不是 OOP。FP 的对立面是无结构的、过程化的编程。

small talk(Alan Kay 奠定 OOP 基础的地方)既是**面向对象的**又是**功能性的**，选择其中一个的想法完全是外来的，不可想象的。

JavaScript 也是如此。当 Brendan Eich 受雇开发 JavaScript 时，他的想法是:

*   浏览器中的方案(FP)
*   看起来像 Java (OOP)

在 JavaScript 中，你可以选择其中之一，但无论好坏，它们都是密不可分的。JavaScript 中的封装依赖于闭包——一个来自函数式编程的概念。

不管你是否知道，很有可能你已经在做一些函数式编程了。

# 如何不做 FP:

为了避免函数式编程，你必须避免使用纯函数。问题是，现在你不能写这个，因为它可能是纯的:

```
const getName = obj => obj.name;
const name = getName({ uid: '123', name: 'Banksy' }); // Banksy
```

让我们重构它，这样它就不再是 FP 了。相反，我们可以使它成为一个具有公共属性的类。因为它没有使用封装，所以称之为 OOP 有些牵强。也许我们应该称之为“过程化对象编程”？：

```
class User {
  constructor ({name}) {
    this.name = name;
  }
  getName () {
    return this.name;
  }
}const myUser = new User({ uid: '123', name: 'Banksy' });
const name = myUser.getName(); // Banksy
```

恭喜你！我们刚刚把两行代码变成了 11 行，并引入了不受控制的外部突变的可能性。我们**收获了什么？**

嗯，没什么，真的。事实上，我们已经失去了很多灵活性和节省大量代码的潜力。

前面的`getName()`函数适用于任何输入对象。这个也可以(因为它是 JavaScript，我们可以将方法委托给随机的对象)，但是它要笨拙得多。我们可以让这两个类从一个公共基类继承——但这意味着对象类之间可能根本不需要存在关系。

忘记可重用性。我们刚刚把它冲进了下水道。下面是代码复制的样子:

```
class Friend {
  constructor ({name}) {
    this.name = name;
  }
  getName () {
    return this.name;
  }
}
```

一个乐于助人的学生从教室后面插嘴道:

> “当然是创建人类啦！”

但是后来:

```
class Country {
  constructor ({name}) {
    this.name = name;
  }
  getName () {
    return this.name;
  }
}
```

> “但显然这些是不同的类型。**当然**你不能在一个国家上用一个人的方法！”

对此我回答:*“为什么不呢？”*

函数式编程的一个惊人的好处是非常简单的泛型编程。你可以称之为“默认通用”:编写单个函数的能力，该函数可以与满足其**通用需求的任何**类型一起工作。

> 注意:对于 Java 人来说，这与静态类型无关。一些 FP 语言有很好的静态类型系统，并且仍然使用像结构和/或更高级类型的特性来分享这个好处。

这个例子很简单，但是我们从这个技巧中节省的代码量是巨大的。

它使得像 [autodux](https://github.com/ericelliott/autodux) 这样的库能够为任何由 getter/setter 对组成的对象自动生成域逻辑(除此之外还有更多)。单单这个技巧就可以将领域逻辑的代码减少一半或更多。

## 不再有高阶函数

因为大多数(但不是全部)高阶函数利用纯函数基于相同的输入返回相同的值，所以你也不能使用像`.map()`、`.filter()`、`reduce()`这样的函数而不产生副作用，只是说你不纯:

```
const arr = [1,2,3];const double = n => n * 2;
const doubledArr = arr.map(double);
```

变成了:

```
const arr = [1,2,3];const double = (n, i) => {
  console.log('Random side-effect for no reason.');
  console.log(`Oh, I know, we could directly save the output
to the database and tightly couple our domain logic to our I/O. That'll be fine. Nobody else will need to multiply by 2, right?`);
  saveToDB(i, n);
  return n * 2;
};const doubledArr = arr.map(double);
```

## **RIP，功能合成 1958–2018**

忘掉[](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)**的**高阶组件**来封装你跨页面的横切关注点。这种方便的声明性语法现在已经被禁止使用了:**

```
const wrapEveryPage = compose(
  withRedux,
  withEnv,
  withLoader,
  withTheme,
  withLayout,
  withFeatures({ initialFeatures })
);
```

**您将不得不手动地将这些导入到每个组件中，或者更糟——陷入一个混乱的、不灵活的类继承层次结构中([被大多数理智的人正确地认为是一个反模式](/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9),甚至[特别是？] by [OO 设计佳能](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612//ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=6bf38f374c00b60d798680a47193cd12))。**

## **告别、承诺和异步/等待**

**承诺是单子。从范畴理论上讲，但我听说它们也是 FP 的东西，因为 Haskell 有它们，并使用单子使一切变得纯粹和懒惰。**

**老实说，失去单子和函子教程可能是一件好事。这些事情比我们说的要简单得多！在我介绍函子和单子的一般概念之前，我教人们如何使用`Array.prototype.map`和承诺*是有原因的。***

**你知道怎么用吗？你对函子和单子的理解已经超过一半了。**

# **所以，为了避免 FP**

*   **不要使用任何 JavaScript 最流行的框架或库(它们都会把你出卖给 FP！).**
*   **不要写纯函数。**
*   **不要使用大量 JavaScript 的内置特性:大多数数学函数(因为它们是纯的)，不可变的字符串和数组方法。map()，。filter()，。forEach()、promises 或 async/await。**
*   **一定要写不必要的类。**
*   **通过手动为几乎所有东西编写 getters 和 setters，使您的域逻辑翻倍。**
*   **采取“可读、明确”的命令式方法，将您的域逻辑与呈现和网络 I/O 问题结合起来。**

**和...说再见:**

*   **时间旅行调试**
*   **轻松的撤消/重做功能开发**
*   **坚如磐石、一致的单元测试**
*   **模拟和 D/I 自由测试**
*   **不依赖网络 I/O 的快速单元测试**
*   **小型、可测试、可调试、可维护的代码库**

**避免将函数式编程作为一种策略？没问题。**

**哦，等等。**

# **在 EricElliottJS.com 了解更多信息**

**EricElliottJS.com 的会员可以上函数式编程的视频课。如果您还不是会员，[今天就注册吧](https://ericelliottjs.com/)。**

**[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)**

*****埃里克·艾略特*** *是* [*【编程 JavaScript 应用】*](http://pjabook.com) *(O'Reilly)的作者，软件导师平台*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人。他曾为****Adobe Systems*******尊巴健身*******华尔街日报*******ESPN*******BBC****等顶级录音师贡献过软件经验*******

****他在远离任何地方的地方和世界上最美丽的女人一起工作。****