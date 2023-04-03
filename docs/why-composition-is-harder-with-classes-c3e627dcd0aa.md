# 为什么写作越来越难

> 原文：<https://medium.com/javascript-scene/why-composition-is-harder-with-classes-c3e627dcd0aa?source=collection_archive---------1----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《排版软件》系列 **s** [**(现在一本书！)**](https://leanpub.com/composingsoftware) 从基础开始学习 JavaScript ES6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [<上一个](/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1) | < < [从第 1 部分开始](/javascript-scene/composing-software-an-introduction-27b72500d6ea) | [下一个>](/javascript-scene/composable-datatypes-with-functions-aec72db3b093)

之前，我们研究了工厂函数，并了解了使用函数混合将它们用于组合是多么容易。现在我们要更详细地看一下类，并检查一下`class`的机制是如何妨碍合成的。

我们还将看看类的良好用例，以及如何安全地使用它们。

ES6 包含了一个方便的`class`语法，所以你可能想知道我们为什么要关心工厂。最明显的区别是构造函数和`class`需要`new`关键字。但是`new`实际上做什么呢？

*   创建一个新对象，并在构造函数中将`this`绑定到它。
*   隐式返回`this`，除非你显式返回另一个对象。
*   将实例`[[Prototype]]`(内部参考)设置为`Constructor.prototype`，以便`Object.getPrototypeOf(instance) === Constructor.prototype`和`instance.__proto__ === Constructor.prototype`。
*   设置`instance.constructor === Constructor`。

所有这些都意味着，与工厂函数不同，类不是组成函数混合的好解决方案。您仍然可以使用`class`实现合成，但是这是一个更复杂的过程，正如您将看到的，额外的成本通常不值得额外的努力。

## 委托原型

您可能最终需要从一个类重构到一个工厂函数，如果您要求调用者使用`new`关键字，那么重构可能会以几种方式破坏您甚至没有意识到的客户端代码。首先，与类和构造函数不同，工厂函数不会自动连接委托原型链接。

`[[Prototype]]`链接用于原型委托，如果您有数百万个对象，这是一种节省内存的便捷方式，或者如果您需要在 16 毫秒的渲染循环周期内访问一个对象的数万个属性，这是一种从程序中挤出微性能提升的便捷方式。

如果不*需要*对内存或性能进行微优化的话，`[[Prototype]]`环节弊大于利。原型链支持 JavaScript 中的`instanceof`操作符，不幸的是`instanceof`因为两个原因而撒谎:

在 ES5 中，`Constructor.prototype`链接是动态的和可重新配置的，如果你需要创建一个抽象工厂，这可能是一个方便的特性——但是如果你使用这个特性，如果`Constructor.prototype`当前在内存中没有引用实例`[[Prototype]]`引用的同一个对象，那么`instanceof`将会给你一个错误的否定:

```
class User {
  constructor ({userName, avatar}) {
    this.userName = userName;
    this.avatar = avatar;
  }
}const currentUser = new User({
  userName: 'Foo',
  avatar: 'foo.png'
});User.prototype = {};console.log(
  currentUser instanceof User, // <-- false -- Oops!// But it clearly has the correct shape:
  // { avatar: "foo.png", userName: "Foo" }
  currentUser
);
```

Chrome 通过在属性描述符中制作`Constructor.prototype`属性`configurable: false`解决了这个问题。然而，Babel 目前并不反映这种行为，所以 Babel 编译的代码将像 ES5 构造函数一样工作。如果您试图重新配置`Constructor.prototype`属性，V8 会自动失败。无论哪种方式，都不会得到你期望的结果。更糟糕的是:行为不一致，现在在 Babel 中工作的代码将来很可能会崩溃。

> 我不建议重新分配`Constructor.prototype`。

一个更常见的问题是 JavaScript 有多个执行上下文——内存沙箱，其中相同的代码将访问不同的物理内存位置。例如，如果在父框架中有一个构造函数，并且在`iframe`中有相同的构造函数，则父框架的`Constructor.prototype`将不会引用与`iframe`中的`Constructor.prototype`相同的内存位置。JavaScript 中的对象值是引擎盖下的内存引用，不同的帧指向内存中不同的位置，所以`===`检查会失败。

`instanceof`的另一个问题是，它是一个名义类型检查而不是结构类型检查，这意味着如果你从一个`class`开始，然后切换一个工厂，所有使用`instanceof`的调用代码将不会理解新的实现，即使它们满足相同的接口契约。例如，假设你的任务是构建一个音乐播放器界面。稍后，产品团队会告诉您添加对视频的支持。之后，他们会要求你添加对 360 视频的支持。它们都提供相同的控制:播放、停止、倒带、快进。

但是如果你使用`instanceof`检查，你的视频接口类的成员不会满足代码库中已经存在的`foo instanceof AudioInterface`检查。

他们会在应该成功的时候失败。其他语言中的可共享接口通过允许一个类声明它实现了一个特定的接口来解决这个问题。这在 JavaScript 中是不可能的。

在 JavaScript 中处理`instanceof`的最好方法是在不需要的时候断开委托原型链接，让`instanceof`在每次调用时都失败。这样你就不会有一种错误的可靠感。不要听`instanceof`的，它永远不会骗你。

## 的。构造函数属性

属性`.constructor`在 JavaScript 中是一个很少使用的特性，但是它可能非常有用，将它包含在对象实例中是一个好主意。如果您不尝试使用它来进行类型检查，它基本上是无害的(因为`instanceof`是不安全的，所以它也是不安全的)。

**理论上，** `.constructor`可以用来创建泛型函数，这些函数能够返回您传入的任何对象的新实例。

**实际上，**在 JavaScript 中有许多不同的方法来创建事物的新实例——拥有对构造函数的引用并不等同于知道如何用它实例化一个新对象——即使是为了看似微不足道的目的，比如创建一个给定对象的空实例:

```
// Return an empty instance of any object type?
const empty = ({ constructor } = {}) => constructor ?
  new constructor() :
  undefined
;const foo = [10];console.log(
  empty(foo) // []
);
```

它似乎适用于数组。让我们用承诺来试试:

```
// Return an empty instance of any type?
const empty = ({ constructor } = {}) => constructor ?
  new constructor() :
  undefined
;const foo = Promise.resolve(10);console.log(
  empty(foo) // [TypeError: Promise resolver undefined is
             //  not a function]
);
```

注意代码中的关键字`new`。这是大部分的问题。假设您可以对任何工厂函数使用`new`关键字是不安全的。有时，这将导致错误。

要实现这一点，我们需要一种标准的方法，使用不需要`new`的标准工厂函数将一个值传递给一个新实例。对此有一个规范:任何工厂或构造函数上都有一个名为`.of()`的静态方法。`[.of()](https://github.com/fantasyland/fantasy-land#of-method)`[方法](https://github.com/fantasyland/fantasy-land#of-method)是一个工厂，它返回数据类型的一个新实例，该实例包含您传递给`.of()`的任何内容。

我们可以使用`.of()`来创建通用`empty()`函数的更好版本:

```
// Return an empty instance of any type?
const empty = ({ constructor } = {}) => constructor.of ?
  constructor.of() :
  undefined
;const foo = [23];console.log(
  empty(foo) // []
);
```

不幸的是，静态`.of()`方法刚刚开始在 JavaScript 中获得支持。`Promise`对象确实有一个类似于`.of()`的静态方法，但是它被称为`.resolve()`，所以我们的泛型`empty()`不能使用承诺:

```
// Return an empty instance of any type?
const empty = ({ constructor } = {}) => constructor.of ?
  constructor.of() :
  undefined
;const foo = Promise.resolve(10);console.log(
  empty(foo) // undefined
);
```

同样，在撰写本文时，JavaScript 中没有针对字符串、数字、对象、映射、弱映射或集合的`.of()`。

如果对`.of()`方法的支持在其他标准 JavaScript 数据类型中流行起来，那么`.constructor`属性最终会成为该语言一个更有用的特性。我们可以用它来构建一个丰富的实用函数库，能够作用于各种函子、单子和其他代数数据类型。

很容易向工厂添加对`.constructor`和`.of()`的支持:

```
const createUser = ({
  userName = 'Anonymous',
  avatar = 'anon.png'
} = {}) => ({
  userName,
  avatar,
  constructor: createUser
});createUser.of = createUser;// testing .of and .constructor:
const empty = ({ constructor } = {}) => constructor.of ?
  constructor.of() :
  undefined
;const foo = createUser({ userName: 'Empty', avatar: 'me.png' });console.log(
  empty(foo), // { avatar: "anon.png", userName: "Anonymous" }
  foo.constructor === createUser.of, // true
  createUser.of === createUser       // true
);
```

您甚至可以通过添加到委托原型来使`.constructor`不可枚举:

```
const createUser = ({
  userName = 'Anonymous',
  avatar = 'anon.png'
} = {}) => ({
  __proto__: {
    constructor: createUser
  },
  userName,
  avatar
});
```

## 类到工厂是一个突破性的变化

工厂允许以下列方式增加灵活性:

*   将实例化细节从调用代码中分离出来。
*   允许您返回任意对象——例如，使用对象池来驯服垃圾收集器。
*   不要假装提供任何类型保证，这样调用者就不会使用`instanceof`和其他不可靠的类型检查方法，这些方法可能会在执行上下文中破坏代码，或者如果您切换到抽象工厂。
*   因为工厂不会假装提供类型保证，所以它们可以动态地将实现换成抽象工厂。例如，为不同媒体类型交换出`.play()`方法的媒体播放器。
*   工厂更容易添加组合功能。

虽然使用类来完成这些目标中的大部分是可能的，但是使用工厂来完成更容易。潜在的 bug 陷阱更少，复杂程度更低，代码也更少。

出于这些原因，从`class`到工厂的重构通常是可取的，但这可能是一个复杂的、容易出错的过程。从类到工厂的重构是每一种面向对象语言的普遍需求。你可以在 Martin Fowler，Kent Beck，John Brant，William Opdyke 和 Don Roberts 所著的[“重构:改进现有代码的设计”](https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=e7d5f652bc860f02c27ec352e1b8342c)中了解更多信息。

由于`new`改变了被调用函数的行为，从类或构造函数改变为工厂函数是一个潜在的破坏性改变。换句话说，强迫调用者使用`new`可能会无意中将调用者锁定在构造函数实现中，因此`new`会将潜在的破坏实现的细节泄露到调用 API 中。

正如我们已经看到的，以下隐含行为会使切换成为一个突破性的变化:

*   工厂实例中缺少`[[Prototype]]`链接将破坏调用者`instanceof`检查。
*   工厂实例中缺少`.constructor`属性可能会破坏依赖它的代码。

这两个问题都可以通过在工厂中手工连接这些属性来解决。

在内部，您还需要注意的是`this`可能是从工厂调用站点动态绑定的，当调用者使用`new`时就不是这样了。如果您想将替代的抽象工厂原型作为静态属性存储在工厂中，这可能会使事情变得复杂。

还有另一个问题。所有`class`调用方必须使用`new`。在 ES6 中关闭它总是会抛出:

```
class Foo {};// TypeError: Class constructor Foo cannot be invoked without 'new'
const Bar = Foo();
```

在 ES6+中，箭头函数通常用于创建工厂，但是因为箭头函数在 JavaScript 中没有自己的`this`绑定，所以用`new`调用箭头函数会抛出一个错误:

```
const foo = () => ({});// TypeError: foo is not a constructor
const bar = new foo();
```

所以，如果你试图从一个类重构到一个箭头函数工厂，它将在原生 ES6 环境中失败，这是可以的。努力失败是好事。

但是，如果你把箭头函数编译成标准函数，就会*失败*失败。这很糟糕，因为这应该是一个错误。它会在你构建应用程序时“工作”,但在生产中可能会失败，这会影响用户体验，甚至阻止应用程序工作。

编译器默认设置的改变可能会破坏你的应用程序，即使你没有改变任何你自己的代码。这一点值得重复:

## **要求 new 的代码违反了开放/封闭原则**

**我们的 API 应该对扩展开放，但对突破性的改变关闭。由于对一个类的常见扩展是把它变成一个更灵活的工厂，但是重构是一个突破性的改变，需要`new`关键字的代码对扩展是封闭的，对突破性的改变是开放的。那和我们想要的正好相反。**

**这件事的影响比一开始看起来要大。如果你的`class` API 是公共的，或者如果你和一个非常大的团队一起开发一个非常大的应用程序，重构很可能会破坏你甚至没有意识到的代码。更好的办法是完全废弃这个类，用一个工厂函数来代替它。**

**这个过程将一个可以通过代码悄悄解决的小技术问题变成了一个需要意识、教育和认同的无界的人的问题——一个更昂贵的改变！**

**我已经多次看到`new`问题导致非常昂贵的头痛，而且很容易避免:**

> **导出工厂而不是类。**

# **`class`关键字和扩展**

**对于 JavaScript 中的对象创建模式来说，`class`关键字应该是一种更好的语法，但是它在几个方面存在不足:**

## **友好的语法**

**`class`的主要目的是提供一个友好的语法来模仿 JavaScript 中其他语言的`class`。我们应该问自己的问题是，JavaScript 真的需要模仿其他语言的`class`吗？**

**JavaScript 的工厂函数提供了一个更加友好的现成语法，并且复杂度更低。通常，一个对象文字就足够了。如果您需要创建许多实例，工厂是一个很好的下一步。**

**在 Java 和 C++中，工厂比类更复杂，但无论如何它们通常是值得构建的，因为它们提供了增强的灵活性。在 JavaScript 中，工厂没有类复杂，也更灵活。**

**比较班级:**

```
class User {
  constructor ({userName, avatar}) {
    this.userName = userName;
    this.avatar = avatar;
  }
}const currentUser = new User({
  userName: 'Foo',
  avatar: 'foo.png'
});
```

**与同等工厂相比…**

```
const createUser = ({ userName, avatar }) => ({
  userName,
  avatar
});const currentUser = createUser({
  userName: 'Foo',
  avatar: 'foo.png'
});
```

**熟悉了 JavaScript 和 arrow 函数之后，工厂显然语法更少，更容易阅读。也许你更喜欢看到`new`关键词，但是有很好的理由避免`new`。熟悉偏见可能会阻碍你的发展。**

**还有哪些论点？**

## **性能和内存**

> **代表原型的好用例很少。**

**`class`语法比 ES5 构造函数的等价语法好一点，但是主要目的是连接委托原型链，委托原型的好用例很少。这真的归结为性能。**

**`class`提供了两种性能优化:存储在委托原型上的属性的共享内存，以及属性查找优化。**

****委托原型**内存优化对工厂和类都可用。工厂可以通过在对象文本中设置`__proto__`属性或者使用`Object.create(proto)`来设置原型。**

**即便如此，大多数现代设备的内存都是以千兆字节计算的。在使用委托原型之前，您应该分析并确保它是真正需要的。**

**任何类型的闭包作用域或**属性访问**都是以每秒数十万次或数百万次操作来衡量的，因此在应用程序的上下文中*很难衡量*性能差异，更不用说影响了。**

**当然，也有例外。RxJS 使用了`class`实例，因为它们比闭包作用域更快，但是 RxJS 是一个通用的实用程序库，可以用于需要压缩到 16ms 渲染循环中的成千上万个操作的上下文中。**

**ThreeJS 使用类，但 ThreeJS 是一个 3d 渲染库，它可能用于游戏引擎每 16 毫秒处理数千个对象。**

**对于像 ThreeJS 和 RxJS 这样的库来说，尽可能地进行极端优化是有意义的。**

**在应用程序的上下文中，我们应该避免过早的优化，并且只将我们的努力集中在它们会产生巨大影响的地方。对于大多数应用程序来说，这意味着我们的网络调用&有效载荷、动画、资产缓存策略等…**

**除非您已经注意到了性能问题，分析了您的应用程序代码，并确定了真正的瓶颈，否则不要对性能进行微优化。**

**相反，您应该为了维护和灵活性而优化代码。**

## **类型检查**

**JavaScript 中的类是动态的，并且`instanceof`检查不能跨执行上下文工作，所以基于`class`的类型检查是不可行的。不靠谱。这很可能会导致错误，并使您的应用程序变得不必要的僵化。**

## **带有“extends”的类继承**

**类继承导致了几个值得重复的众所周知的问题:**

*   **紧密耦合:类继承是面向对象设计中最紧密的耦合形式。**
*   ****不灵活的层次结构:**如果有足够的时间和用户，所有的类层次结构对于新的用例来说最终都是错误的，但是紧密耦合使得重构变得困难。**
*   ****大猩猩/香蕉问题:**没有选择性遗传。“你想要一个香蕉，但你得到的是一只大猩猩拿着香蕉和整个丛林。”~乔·阿姆斯特朗在[《工作中的编码员》](https://www.amazon.com/Coders-Work-Reflections-Craft-Programming/dp/1430219483/ref=as_li_ss_tl?s=books&ie=UTF8&qid=1500436305&sr=1-1&keywords=coders+at+work&linkCode=ll1&tag=eejs-20&linkId=45e89bc5d776b1326c2ae90355e9ccac)**
*   ****必要的复制:**由于不灵活的层次结构和大猩猩/香蕉问题，代码重用通常是通过复制/粘贴来完成的，这违反了 DRY(不要重复自己)，并且首先破坏了继承的全部目的。**

**`extends`的唯一目的是创建单祖先类分类法。一些聪明的黑客读到这里会说，“啊哈！不是这样的！可以做班级作文！”对此我的回答是，“啊，但是现在你在使用对象组合而不是类继承，在 JavaScript 中有更容易、更安全的方法来做到这一点，而不需要使用`extends`”**

# **如果你小心的话，上课是可以的**

**随着所有警告的消失，出现了一些清晰的指导方针，可以帮助您安全地使用类:**

*   **避免`instanceof`——这是因为 JavaScript 是动态的，有多个执行上下文，而`instanceof`在这两种情况下都会失败。如果您以后转到抽象工厂，也会带来问题。**
*   **避免`extends`——不要多次扩展一个层次结构。"优先选择对象组合而不是类继承."~ [“设计模式:可复用面向对象软件的要素”](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented-ebook/dp/B000SEIBB8/ref=as_li_ss_tl?s=digital-text&ie=UTF8&qid=1500478917&sr=1-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=7443052c45c6e7d9cb7f6b06fa58b488)**
*   **避免导出您的类。在内部使用`class`来提高性能，但是导出一个创建实例的工厂来阻止用户扩展你的类，并避免强迫调用者使用`new`。**
*   **避开`new`。只要有意义就尽量避免直接使用，不要强迫你的呼叫者使用。(改为导出工厂)。**

**在以下情况下可以使用 class:**

*   **你正在为一个框架构建 UI 组件，比如 React 或 Angular。这两个框架都将组件类包装到工厂中，并为您管理实例化，因此您不必在自己的代码中使用`new`。**
*   ****你永远不会从你自己的类或组件中继承。相反，尝试对象组合、函数组合、高阶函数、高阶组件或模块——它们都是比类继承更好的代码重用模式。****
*   ****你需要优化性能。**记住导出一个工厂，这样调用者就不必使用`new`也不会被诱入`extends`陷阱。**

**在大多数其他情况下，工厂会为你提供更好的服务。**

**工厂比 JavaScript 中的类或构造函数简单。总是从最简单的解决方案开始，只在需要时才发展到更复杂的解决方案。**

**[***接下来:带函数的可组合数据类型>***](/javascript-scene/composable-datatypes-with-functions-aec72db3b093)**

# **后续步骤**

**想了解更多关于用 JavaScript 进行对象组合的知识吗？**

**[跟埃里克·艾略特学习 JavaScript】。如果你不是会员，你就错过了！](http://ericelliottjs.com/product/lifetime-access-pass/)**

**[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)**

*****埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验********

****他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。****