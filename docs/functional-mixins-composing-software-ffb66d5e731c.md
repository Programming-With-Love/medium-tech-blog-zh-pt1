# 功能混合蛋白

> 原文：<https://medium.com/javascript-scene/functional-mixins-composing-software-ffb66d5e731c?source=collection_archive---------0----------------------->

## 编写软件

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分**s**[(现在一本书！)](https://leanpub.com/composingsoftware) 从基础开始学习 JavaScript ES6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [<上一个](/javascript-scene/functors-categories-61e031bac53f) | [< <从第 1 部分开始](/javascript-scene/composing-software-an-introduction-27b72500d6ea) | [下一个>](/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1)

**功能混合**是在管道中连接在一起的可组合工厂功能；每个函数都像装配线上的工人一样添加一些属性或行为。函数式 mixin 不依赖或不需要基工厂或构造函数:只需将任意对象传入 mixin，就会返回该对象的增强版本。

功能性 mixin 特性:

*   数据隐私/封装
*   继承私有状态
*   从多个来源继承
*   没有菱形问题(属性冲突模糊)-最后一个获胜
*   没有基类要求

# 动机

所有现代软件开发实际上都是组合:我们将一个大的、复杂的问题分解成更小、更简单的问题，然后组合解决方案形成一个应用程序。

组成的原子单位有两种:

*   功能
*   数据结构

应用程序结构是由这些原子单元的组合定义的。通常，复合对象是使用类继承产生的，其中类从父类继承其大部分功能，并扩展或覆盖部分。这种方法的问题在于，它会导致 **is-a** 思维，例如，“管理员就是雇员”，导致许多设计问题:

*   紧密耦合问题:因为子类依赖于父类的实现，所以类继承是面向对象设计中最紧密的耦合。
*   **脆弱的基类问题:**由于紧密耦合，对基类的更改可能会破坏大量的子类——潜在地在由第三方管理的代码中。作者可以破解他们不知道的代码。
*   **不灵活的层次问题:**对于单祖先分类法，如果有足够的时间和进化，所有的类分类法对于新的用例来说最终都是错误的。
*   **必然的复制问题:**由于不灵活的层次结构，新的用例经常通过复制而不是扩展来实现，导致类似的类出现意想不到的分歧。一旦复制开始，新类应该从哪个类继承，或者为什么继承就不明显了。
*   **大猩猩/香蕉问题:**“…面向对象语言的问题是它们拥有所有这些隐含的环境。你想要一个香蕉，但你得到的是一只大猩猩拿着香蕉和整个丛林。”~乔·阿姆斯特朗，[《工作中的编码员》](http://www.amazon.com/gp/product/1430219483?ie=UTF8&camp=213733&creative=393185&creativeASIN=1430219483&linkCode=shr&tag=eejs-20&linkId=3MNWRRZU3C4Q4BDN)

如果一名行政人员是雇员，你如何处理聘请外部顾问临时履行行政职责的情况？如果你预先知道每一个需求，也许类继承可以工作，但是我从来没有见过这种情况发生。如果使用足够多，随着新问题和更有效的流程的发现，应用程序和需求不可避免地会随着时间的推移而增长和发展。

Mixins 提供了一种更加灵活的方法。

# 什么是混合蛋白？

> *“重对象组合轻类继承”四人帮，* [*“设计模式:可复用面向对象软件的要素”*](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4)

**混合**是*对象组合的一种形式，*其中组件特性混合到一个复合对象中，因此每个混合的属性成为复合对象的属性。

OOP 中的术语“mixins”来自 mixin 冰激凌店。你不用在不同的预混合桶里放一大堆冰淇淋口味，而是香草冰淇淋，以及一堆可以混合在一起为每位顾客创造定制口味的独立配料。

对象混合是相似的:你从一个空的对象开始，混合一些特性来扩展它。因为 JavaScript 支持动态对象扩展和没有类的对象，所以在 JavaScript 中使用对象混合非常容易——以至于它是 JavaScript 中最常见的继承形式。让我们看一个例子:

```
const chocolate = {
  hasChocolate: () => true
};const caramelSwirl = {
  hasCaramelSwirl: () => true
};const pecans = {
  hasPecans: () => true
};const iceCream = Object.assign({}, chocolate, caramelSwirl, pecans);/*
// or, if your environment supports object spread...
const iceCream = {...chocolate, ...caramelSwirl, ...pecans};
*/console.log(`
  hasChocolate: ${ iceCream.hasChocolate() }
  hasCaramelSwirl: ${ iceCream.hasCaramelSwirl() }
  hasPecans: ${ iceCream.hasPecans() }
`);
```

哪些日志:

```
 hasChocolate: true
  hasCaramelSwirl: true
  hasPecans: true
```

# 什么是函数继承？

功能继承是通过对对象实例应用扩充功能来继承特性的过程。该函数提供了一个闭包作用域，您可以使用它来保持一些数据的私有性。扩充功能使用动态对象扩展来用新的属性和方法扩展对象实例。

让我们看一个道格拉斯·克洛克福特的例子，他创造了这个术语:

```
// Base object factory
function base(spec) {
    var that = {}; // Create an empty object
    that.name = spec.name; // Add it a "name" property
    return that; // Return the object
}// Construct a child object, inheriting from "base"
function child(spec) {
    // Create the object through the "base" constructor
    var that = base(spec); 
    that.sayHello = function() { // Augment that object
        return 'Hello, I\'m ' + that.name;
    };
    return that; // Return it
}// Usage
var result = child({ name: 'a functional object' });
console.log(result.sayHello()); // "Hello, I'm a functional object"
```

因为`child()`和`base()`紧密耦合，当你加上`grandchild()`、`greatGrandchild()`等...，您将选择解决类继承的大多数常见问题。

# 什么是功能性混音？

函数混合是可组合的函数，它将新的属性或行为与给定对象的属性混合在一起。函数式 mixin 不依赖也不需要基工厂或构造函数:只需将任意对象传递给 mixin，它就会被扩展。

让我们看一个例子:

```
const flying = o => {
  let isFlying = false; return Object.assign({}, o, {
    fly () {
      isFlying = true;
      return this;
    }, isFlying: () => isFlying, land () {
      isFlying = false;
      return this;
    }
  });
};const bird = flying({});
console.log( bird.isFlying() ); // false
console.log( bird.fly().isFlying() ); // true
```

注意，当我们调用`flying()`时，我们需要传入一个要扩展的对象。功能混合是为功能组合而设计的。让我们创造一些东西来作曲:

```
const quacking = quack => o => Object.assign({}, o, {
  quack: () => quack
});const quacker = quacking('Quack!')({});
console.log( quacker.quack() ); // 'Quack!'
```

# 组成函数混合

可以用简单的函数组合来组合函数混合:

```
const createDuck = quack => quacking(quack)(flying({}));const duck = createDuck('Quack!');console.log(duck.fly().quack());
```

不过，读起来有点别扭。调试或重新安排合成顺序也可能有点棘手。

当然，这是标准的函数组合，我们已经知道一些使用`compose()`或`pipe()`的更好的方法。如果我们使用`pipe()`来颠倒函数的顺序，那么组合将看起来像`Object.assign({}, ...)`或`{...object, ...spread}`——保持相同的优先顺序。在属性冲突的情况下，中的最后一个对象获胜。

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
// OR...
// import pipe from `lodash/fp/flow`;const createDuck = quack => pipe(
  flying,
  quacking(quack)
)({});const duck = createDuck('Quack!');console.log(duck.fly().quack());
```

# 何时使用功能混合

你应该总是使用尽可能简单的抽象来解决你正在处理的问题。从一个纯函数开始。如果您需要具有持久状态的对象，请尝试使用工厂函数。如果您需要构建更复杂的对象，请尝试函数混合。

以下是一些很好的函数混合用例:

*   应用程序状态管理，例如 Redux 存储。
*   某些跨领域的问题和服务，例如集中式记录器。
*   可组合的函数数据类型，例如 JavaScript `Array`类型实现`Semigroup`、`Functor`、`Foldable`。一些代数结构可以根据其他代数结构派生，这意味着某些派生可以组成一个新的数据类型，而无需定制。

**React users:** `class`适合生命周期挂钩，因为调用者不需要使用`new`，并且记录的最佳实践是避免从 React 提供的基本组件之外的任何组件继承。

我使用并推荐带有功能组合的 hoc(高阶组件)来组合 UI 组件。

# 警告

大多数问题都可以用纯函数很好地解决。功能混合就不一样了。像类继承一样，函数混合也有自己的问题。事实上，使用函数混合可以忠实地再现类继承的所有特性和问题。

不过，你可以通过以下建议来避免这种情况:

*   使用最简单的实际实现。从左边开始，只在需要时移到右边:纯函数>工厂>函数混合>类。
*   避免在对象、混合或数据类型之间创建 **is-a** 关系。
*   避免 mixin 之间的隐式依赖——只要有可能，函数 mixin 应该是自包含的，并且不知道其他 mixin。
*   “函数式混合”并不意味着“函数式编程”。
*   当您使用`Object.assign()`或对象扩展语法(`{...}`)访问属性时，可能会有副作用。您还将跳过任何不可枚举的属性。ES2017 增加了`[Object.getOwnPropertyDescriptors()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors)`来解决这个问题。

我主要依靠功能组合来组合行为和应用程序结构，但也经常使用高阶组件(hoc)形式的功能混合(混合到组件属性中)和表达中间件(混合到请求和响应对象中)。

我从不使用类继承，除非我直接从第三方基类继承，比如`React.Class`。我从不建立自己的继承层次结构。

# 班级

在 JavaScript 中，类继承很少(也许*永远不会*)是最好的方法，但是这种选择有时是由你无法控制的库或框架做出的。在这种情况下，如果库:

1.  不要求您扩展自己的类(即，不要求您构建多级类层次结构)，并且
2.  不需要您直接使用`new`关键字——换句话说，框架会为您处理实例化

Angular 2+和 React 都满足这些要求，所以只要不扩展自己的类，就可以安全地使用它们的类。如果您愿意，React 允许您避免使用类，但是您的组件可能无法利用 React 基类中内置的优化，并且您的组件看起来不像文档示例中的组件。在任何情况下，如果有意义的话，您应该总是更喜欢 React 组件的函数形式。

## 课堂表现

在一些浏览器中，类可以提供 JavaScript 引擎优化，这在其他情况下是不可用的。几乎在所有情况下，这些优化都不会对应用程序的性能产生重大影响。事实上，可能很多年都不需要担心性能差异。无论如何构建对象，对象创建和属性访问总是非常快(每秒百万次操作)。

也就是说，类似 RxJS、Lodash 等通用实用程序库的作者应该研究使用`class`创建对象实例可能带来的性能优势。除非你已经测量出一个显著的瓶颈，你可以证明使用`class`可以大大减少这个瓶颈，否则你应该优化干净、灵活的代码，而不是担心性能。

# 隐式依赖

您可能会被诱惑去创建旨在一起工作的功能混合。假设您想为您的应用程序构建一个配置管理器，当您试图访问不存在的配置属性时，它会记录警告。

有可能像这样建造它:

```
// in its own module...
const withLogging = logger => o => Object.assign({}, o, {
  log (text) {
    logger(text)
  }
}); // in a different module with no explicit mention of
// withLogging -- we just assume it's there...
const withConfig = config => (o = {
  log: (text = '') => console.log(text)
}) => Object.assign({}, o, {
  get (key) {
    return config[key] == undefined ? // vvv implicit dependency here... oops! vvv
      this.log(`Missing config key: ${ key }`) :
      // ^^^ implicit dependency here... oops! ^^^ config[key]
    ;
  }
});// in yet another module that imports withLogging and
// withConfig...
const createConfig = ({ initialConfig, logger }) =>
  pipe(
    withLogging(logger),
    withConfig(initialConfig)
  )({})
;// elsewhere...
const initialConfig = {
  host: 'localhost'
};const logger = console.log.bind(console);const config = createConfig({initialConfig, logger});console.log(config.get('host')); // 'localhost'
config.get('notThere'); // 'Missing config key: notThere'
```

然而，也可以这样构建它:

```
// import withLogging() explicitly in withConfig module
import withLogging from './with-logging';const addConfig = config => o => Object.assign({}, o, {
  get (key) {
    return config[key] == undefined ? 
      this.log(`Missing config key: ${ key }`) :
      config[key]
    ;
  }
});const withConfig = ({ initialConfig, logger }) => o =>
  pipe( // vvv compose explicit dependency in here vvv
    withLogging(logger),
    // ^^^ compose explicit dependency in here ^^^ addConfig(initialConfig)
  )(o)
;// The factory only needs to know about withConfig now...
const createConfig = ({ initialConfig, logger }) =>
  withConfig({ initialConfig, logger })({})
; // elsewhere, in a different module...
const initialConfig = {
  host: 'localhost'
};const logger = console.log.bind(console);const config = createConfig({initialConfig, logger});console.log(config.get('host')); // 'localhost'
config.get('notThere'); // 'Missing config key: notThere'
```

正确的选择取决于很多因素。要求函数 mixin 使用提升的数据类型是有效的，但是如果是这样的话，应该在函数签名和 API 文档中明确 API 契约。

这就是隐式版本在签名中有一个默认值`o`的原因。由于 JavaScript 缺乏类型注释功能，我们可以通过提供默认值来伪装它:

```
const withConfig = config => (o = {
  log: (text = '') => console.log(text)
}) => Object.assign({}, o, {
  // ...
```

如果您使用的是 TypeScript 或 Flow，那么最好为您的对象需求声明一个显式接口。

# 函数混合和函数编程

在函数混合的上下文中,“函数式”并不总是具有与“函数式编程”相同的纯粹含义。函数混合通常在 OOP 风格中使用，带有副作用。许多函数混合会改变你传递给它们的对象参数。顾客小心上当。

出于同样的原因，一些开发人员更喜欢函数式编程风格，不会维护对传入对象的标识引用。你应该对你的 mixins 和使用它们的代码进行编码，假设它们是两种风格的随机混合。

这意味着如果你需要返回对象实例，总是返回`this`而不是闭包函数代码中对实例对象的引用，很可能它们不是对相同对象的引用。此外，总是假设对象实例将通过使用`Object.assign()`或`{...object, ...spread}`语法的赋值来复制。这意味着，如果您设置了不可枚举的属性，它们可能不会对最终对象起作用:

```
const a = Object.defineProperty({}, 'a', {
  enumerable: false,
  value: 'a'
});const b = {
  b: 'b'
};console.log({...a, ...b}); // { b: 'b' }
```

同样，如果您使用的函数混合不是在函数代码中创建的，就不要假设代码是纯的。假设基对象可能会发生变异，并假设可能会有副作用&没有引用透明性保证，也就是说，记忆由函数混合组成的工厂通常是不安全的。

# 结论

Functional mixins 是可组合的工厂函数，它向对象添加属性和行为，比如装配线上的工作站。它们是从多个源特性( **has-a，uses-a，can-do** )组合行为的好方法，而不是继承给定类的所有特性( **is-a** )。

请注意，“函数式混合”并不意味着“函数式编程”——它仅仅意味着“使用函数的混合”。可以使用函数式编程风格编写函数式混合，避免副作用并保持引用透明性，但这并不能保证。第三方混音可能会有副作用和不确定性。

*   与简单的对象混合不同，函数混合支持真正的数据隐私(封装)，包括继承私有数据的能力。
*   与单祖先类继承不同，函数混合也支持从许多祖先继承的能力，类似于类装饰符、特征或多重继承。
*   与 C++中的多重继承不同，菱形问题在 JavaScript 中很少出现问题，因为当冲突出现时有一个简单的规则:最后添加的 mixin 获胜。
*   与类装饰、特征或多重继承不同，不需要基类。

从最简单的实施开始，仅在需要时转向更复杂的实施:

函数>对象>工厂函数>函数混合>类

[**接下来:JavaScript 工厂函数与 ES6+ >**](/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1)

# 后续步骤

想了解更多关于用 JavaScript 编写软件的知识吗？

[跟 Eric Elliott 学 JavaScript】。如果你不是会员，你就错过了！](http://ericelliottjs.com/product/lifetime-access-pass/)

[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**