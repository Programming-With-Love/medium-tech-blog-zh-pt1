# 作曲软件:导论

> 原文：<https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea?source=collection_archive---------0----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分**s**[(现在一本书！)](https://leanpub.com/composingsoftware) 从基础开始学习 JavaScript ES6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc) *|* [*下期>*](/javascript-scene/the-dao-of-immutability-9f91a70c88cd)

> 组成:“将部分或元素结合起来形成整体的行为。”~ Dictionary.com

在我的第一堂高中编程课上，我被告知软件开发是“将一个复杂的问题分解成更小的问题，并组合简单的解决方案以形成复杂问题的完整解决方案的行为。”

我人生中最大的遗憾之一是，我没能尽早理解那一课的意义。我学到软件设计的本质时已经太晚了。

我采访了数百名开发人员。我从这些会议中学到的是，我并不孤单。很少有在职的软件开发人员能够很好地理解软件开发的本质。他们不知道我们拥有的最重要的工具，也不知道如何好好利用它们。100%的人都在努力回答软件开发领域中最重要的一个或两个问题:

*   什么是函数构成？
*   什么是物体构成？

问题是，你不能因为没有意识到，就不去构图。你仍然这样做——但你做得很差。你写的代码有更多的错误，让其他开发人员更难理解。这是个大问题。影响是非常昂贵的。我们花在维护软件上的时间比从头开始创建软件的时间还多，我们的错误影响着全世界数十亿人。

今天，整个世界都依赖于软件。每辆新车都是一台装在轮子上的迷你超级计算机，软件设计的问题会引发真正的事故，并造成真正的生命损失。2013 年，在一次事故调查揭示了包含 1 万个全局变量的意大利面条代码后，陪审团认定丰田软件开发团队[犯有“鲁莽忽视”](http://www.safetyresearch.net/blog/articles/toyota-unintended-acceleration-and-big-bowl-%E2%80%9Cspaghetti%E2%80%9D-code)罪。

[黑客和政府储存漏洞](https://www.technologyreview.com/s/607875/should-the-government-keep-stockpiling-software-bugs/)为了监视人们，窃取信用卡，利用计算资源发动分布式拒绝服务(DDoS)攻击，破解密码，甚至[操纵选举](https://www.technologyreview.com/s/604138/the-fbi-shut-down-a-huge-botnet-but-there-are-plenty-more-left/)。

我们必须做得更好。

# 你每天都在编写软件

如果你是一名软件开发人员，不管你是否知道，你每天都在编写函数和数据结构。你可以有意识地这样做(而且做得更好)，或者你可以用胶带和强力胶水偶然地这样做。

软件开发的过程是将大问题分解成小问题，构建解决这些小问题的组件，然后将这些组件组合在一起形成一个完整的应用程序。

# 构成函数

函数组合是将一个函数应用于另一个函数的输出的过程。在代数中，给定两个函数，`f`和`g`，`(f ∘ g)(x) = f(g(x))`。圆圈是复合运算符。通常发音为“由...组成”或“在...之后”。你可以大声地说用 `g`组成的`f` *等于`x`的`g`的`f`，或者* `g`后的`f` *等于`x`的`g``f`。我们说`f` *在* `g`之后，因为`g`首先被求值，然后它的输出作为参数传递给`f`。*

每次你像这样写代码，你都在构造函数:

```
const g = n => n + 1;
const f = n => n * 2;const doStuff = x => {
  const afterG = g(x);
  const afterF = f(afterG);
  return afterF;
};doStuff(20); // 42
```

每次你写一个承诺链，你都在构造函数:

```
const g = n => n + 1;
const f = n => n * 2;Promise.resolve(20)
  .then(g)
  .then(f)
  .then(value => console.log(value)) // 42
;
```

同样，每次你链接数组方法调用、lodash 方法、observables (RxJS 等)时，你都在组合函数。如果你在链接，你就是在作曲。如果你把返回值传递给其他函数，你就是在编写。如果你在一个序列中调用两个方法，你使用`this`作为输入数据进行组合。

> 如果你在链接，你就是在作曲。

当你有意组合函数时，你会做得更好。

有意地组合函数，我们可以将我们的`doStuff()`函数改进成一个简单的一行程序:

```
const g = n => n + 1;
const f = n => n * 2;const doStuffBetter = x => f(g(x));doStuffBetter(20); // 42
```

对这种形式的一个常见反对意见是它更难调试。例如，我们如何用函数组合来写这个？

```
const doStuff = x => {
  const afterG = g(x);
  console.log(`after g: ${ afterG }`);
  const afterF = f(afterG);
  console.log(`after f: ${ afterF }`);
  return afterF;
};doStuff(20); // =>
/*
"after g: 21"
"after f: 42"
*/
```

首先，让我们将“f 之后”、“g 之后”的登录抽象成一个名为`trace()`的小实用程序:

```
const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};
```

现在我们可以这样使用它:

```
const doStuff = x => {
  const afterG = g(x);
  trace('after g')(afterG);
  const afterF = f(afterG);
  trace('after f')(afterF);
  return afterF;
};doStuff(20); // =>
/*
"after g: 21"
"after f: 42"
*/
```

流行的函数式编程库，如 Lodash 和 Ramda，包含了使函数组合更容易的工具。您可以像这样重写上面的函数:

```
import pipe from 'lodash/fp/flow';const doStuffBetter = pipe(
  g,
  trace('after g'),
  f,
  trace('after f')
);doStuffBetter(20); // =>
/*
"after g: 21"
"after f: 42"
*/
```

如果您想在不导入任何东西的情况下尝试这段代码，您可以像这样定义管道:

```
// pipe(...fns: [...Function]) => x => y
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
```

如果你还不明白它是如何工作的，也不要担心。稍后，我们将更详细地探讨函数组合。事实上，它是如此的重要，你会在这篇文章中多次看到它的定义和演示。重点是帮助你熟悉它，以至于它的定义和使用变得自动化。与构图融为一体。

创建一个函数管道，将一个函数的输出传递给另一个函数的输入。当你使用`pipe()`(和它的孪生兄弟`compose()`)时，你不需要中间变量。不提及参数而编写函数被称为**无点风格。为此，您将调用一个返回新函数的函数，而不是显式声明该函数。这意味着你不需要`function`关键字或者箭头语法(`=>`)。**

无点风格可能走得太远，但是这里或那里有一点点是很好的，因为那些中间变量给你的函数增加了不必要的复杂性。

降低复杂性有几个好处:

## 内存储器

普通人的大脑在[工作记忆](http://www.nature.com/neuro/journal/v17/n3/fig_tab/nn.3655_F2.html)中只有少量用于离散量子的共享资源，每个变量都可能消耗其中一个量子。随着你增加更多的变量，我们准确回忆每个变量含义的能力会减弱。工作记忆模型通常包含 4-7 个离散量子。在这些数字之上，错误率急剧增加。

使用管道形式，我们消除了 3 个变量——为其他事情释放了几乎一半的可用工作内存。这大大降低了我们的认知负荷。软件开发人员往往比普通人更擅长将数据分块存储到工作记忆中，但也不会多到削弱保存的重要性。

## 信噪比

简洁的代码还可以提高代码的信噪比。这就像听收音机——当收音机没有调到合适的电台时，你会听到很多干扰噪音，更难听到音乐。当你调到正确的电台时，噪音就消失了，你会得到更强的音乐信号。

代码也是一样。更简洁的代码表达有助于理解。有些代码给了我们有用的信息，有些代码只是占用了空间。如果你能减少你使用的代码量而不减少要传递的意思，你将使代码更容易被其他需要阅读它的人解析和理解。

## 虫子的表面区域

看看之前和之后的函数。看起来这个功能好像节食减肥了。这很重要，因为额外的代码意味着有额外的表面区域让虫子藏在里面，这意味着会有更多的虫子藏在里面。

> 更少的代码=更少的 bug 表面积=更少的 bug。

# 合成对象

> “偏爱对象组合胜过类继承”四人帮，[“设计模式:可重用面向对象软件的要素”](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4)
> 
> 在计算机科学中，复合数据类型或复合数据类型是可以在程序中使用编程语言的原始数据类型和其他复合类型构建的任何数据类型。[……]构造复合类型的行为称为组合。”~维基百科

这些是原语:

```
const firstName = 'Claude';
const lastName = 'Debussy';
```

这是一个合成图:

```
const fullName = {
  firstName,
  lastName
};
```

同样，所有的数组、集合、映射、WeakMaps、TypedArrays 等都是复合数据类型。任何时候构建任何非原始数据结构，都是在执行某种对象组合。

注意，四人帮定义了一种称为**复合模式**的模式，这是一种特定类型的递归对象复合，它允许您同等地对待单个组件和聚合复合。一些开发人员感到困惑，认为复合模式是对象组合的唯一形式。别糊涂了。有许多不同种类的对象组成。

四人帮继续说，“你会看到对象组合在设计模式中一次又一次地被应用”，然后他们列出了三种对象组合关系，包括**委托**(用在状态、策略和访问者模式中)**相识**(当一个对象通过引用了解另一个对象时，通常作为参数传递:a 使用-a 关系，例如， 可以向网络请求处理器传递对记录器的引用，以记录请求—请求处理器*使用*记录器)，以及**聚合**(当子对象形成父对象的一部分时:具有-a 关系，例如，DOM 子对象是 DOM 节点中的组件元素—DOM 节点*具有*子对象)。

类继承可以用来构造复合对象，但这是一种限制性的、脆弱的方法。当“四人帮”说“比起类继承，更喜欢对象组合”时，他们是在建议你使用灵活的方法来构建组合对象，而不是僵硬的、紧耦合的类继承方法。

我们将使用来自[“计算机科学中的分类方法:来自拓扑学的方面”](https://www.amazon.com/Categorical-Methods-Computer-Science-Topology/dp/0387517227/ref=as_li_ss_tl?ie=UTF8&qid=1495077930&sr=8-3&keywords=Categorical+Methods+in+Computer+Science:+With+Aspects+from+Topology&linkCode=ll1&tag=eejs-20&linkId=095afed5272832b74357f63b41410cb7) (1989)的对象组合的更一般的定义:

> “复合对象是通过将对象放在一起而形成的，因此后者中的每一个都是前者的‘一部分’。”

另一个很好的参考是“通过复合设计的可靠软件”，Glenford J Myers，1975。这两本书早已绝版，但如果你想从更深的技术层面探索对象合成的主题，你仍然可以在亚马逊或易贝上找到卖家。

*类继承只是复合对象构造的一种。*所有的类都会产生复合对象，但并不是所有的复合对象都是由类或类继承产生的。“优先选择对象组合而不是类继承”意味着您应该从小的组件部分形成组合对象，而不是从类层次结构中的祖先继承所有属性。后者在面向对象设计中引起了各种各样众所周知的问题:

*   **紧密耦合问题:**因为子类依赖于父类的实现，所以类继承是面向对象设计中最紧密的耦合。
*   **脆弱的基类问题:**由于紧密耦合，对基类的更改可能会破坏大量的子类——可能是在由第三方管理的代码中。作者可以破解他们不知道的代码。
*   **不灵活的层次问题:**对于单祖先分类法，如果有足够的时间和进化，所有的类分类法最终对于新的用例来说都是错误的。
*   **必然的复制问题:**由于不灵活的层次结构，新的用例经常通过复制而不是扩展来实现，这导致了相似的类意外地不同。一旦复制开始，新类应该从哪个类继承，或者为什么继承就不明显了。
*   **大猩猩/香蕉问题:**“…面向对象语言的问题是它们拥有所有这些隐含的环境。你想要一个香蕉，但你得到的是一只大猩猩拿着香蕉和整个丛林。”~乔·阿姆斯特朗，[《工作中的编码员》](http://www.amazon.com/gp/product/1430219483?ie=UTF8&camp=213733&creative=393185&creativeASIN=1430219483&linkCode=shr&tag=eejs-20&linkId=3MNWRRZU3C4Q4BDN)

JavaScript 中最常见的对象组合形式被称为**对象串联**(又名 mixin 组合)。它像冰淇淋一样工作。你从一个物体开始(像香草冰淇淋)，然后混合你想要的特征。添加一些坚果，焦糖，巧克力漩涡，你最后得到的是坚果焦糖巧克力漩涡冰淇淋。

使用类继承构建复合:

```
class Foo {
  constructor () {
    this.a = 'a'
  }
}class Bar extends Foo {
  constructor (options) {
    super(options);
    this.b = 'b'
  }
}const myBar = new Bar(); // {a: 'a', b: 'b'}
```

用混合成分制造复合材料；

```
const a = {
  a: 'a'
};const b = {
  b: 'b'
};const c = {...a, ...b}; // {a: 'a', b: 'b'}
```

稍后，我们将更深入地探讨对象组合的其他风格。目前，你的理解应该是:

1.  做这件事的方法不止一种。
2.  有些方式比其他方式更好。
3.  您希望为手头的任务选择最简单、最灵活的解决方案。

# 结论

这不是函数式编程(FP)对面向对象编程(OOP)，或者一种语言对另一种语言。组件可以采取函数、数据结构、类等形式。不同的编程语言倾向于为组件提供不同的原子元素。Java 提供类，Haskell 提供函数，等等……但是无论你喜欢什么语言，什么范式，你都离不开组合函数和数据结构。最终，这就是所有的一切。

我们将大量讨论函数式编程，因为函数是用 JavaScript 编写的最简单的东西，函数式编程社区已经投入了大量的时间和精力来形式化函数组合技术。

我们不会说函数式编程比面向对象编程更好，或者你必须选择一个而不是另一个。OOP vs FP 是一个错误的二分法。我近年来看到的每一个真正的 Javascript 应用程序都广泛地混合了 FP 和 OOP。

我们将使用对象组合为函数式编程产生数据类型，函数式编程为 OOP 产生对象。

不管你怎么写软件，都要把它写好。

> 软件开发的本质是组合。

一个不懂组成的软件开发人员就像一个不懂螺栓或钉子的房屋建筑商。构建没有组合意识的软件就像一个房屋建筑商用胶带和胶水把墙粘在一起。

是时候简化了，而简化的最好方法就是去本质。问题是，这个行业中几乎没有人能很好地把握要点。作为一个行业，我们辜负了软件开发人员。作为一个行业，我们有责任更好地培训开发人员。我们必须改进。我们需要承担责任。如今，从经济到医疗设备，一切都在软件上运行。在这个星球上，人类生活的每一个角落都受到软件质量的影响。我们需要知道我们在做什么。

是时候学习如何编写软件了。

> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc) *|* [*下一个>*](/javascript-scene/the-dao-of-immutability-9f91a70c88cd)

# 在 EricElliottJS.com 了解更多信息

EricElliottJS.com 会员可以参加互动代码挑战视频课程。如果你还不是会员，今天就注册吧。

***Eric Elliott****是一位分布式系统专家，著有书籍* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](https://ericelliottjs.com/product/programming-javascript-applications-ebook/) *。作为*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发人员远程工作和拥抱工作/生活平衡所需的技能。他为加密项目组建开发团队并为其提供建议，并为 Adobe Systems、****【Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *和顶级录音师(包括****

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**