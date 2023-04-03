# 函子和类别

> 原文：<https://medium.com/javascript-scene/functors-categories-61e031bac53f?source=collection_archive---------2----------------------->

## 编写软件

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分 **s** [**(现在一本书！)**](https://leanpub.com/composingsoftware) 从基础开始学习 JavaScript ES6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)|[<上一张](/javascript-scene/abstract-data-types-and-the-software-crisis-671ea7fc72e7) | [下一张>](/javascript-scene/javascript-monads-made-simple-7856be57bfe8)

一个**仿函数数据类型**是你可以映射的。它是一个容器，有一个接口，可以用来对里面的值应用一个函数。当你看到一个函子的时候，你应该想到*“mapable”。*函子类型通常用一个`.map()`方法表示为一个对象，它从输入映射到输出，同时保留结构。实际上，“保留结构”意味着返回值是相同类型的函子(尽管容器内的值可能是不同的类型)。

函子提供了一个盒子，里面有零个或更多的东西，以及一个映射接口。数组是仿函数的一个很好的例子，但是许多其他类型的对象也可以被映射，包括承诺、流、树、对象等等。JavaScript 的内置数组和 promise 对象的行为类似于函子。对于集合(数组、流等。)，`.map()`通常迭代集合，将给定的函数应用于集合中的每个值，但不是所有的函子都迭代。函子实际上是在特定的上下文中应用一个函数。

承诺用名字`.then()`代替`.map()`。你通常可以认为`.then()`是一个异步的`.map()`方法，除非你有一个嵌套的承诺，在这种情况下，它会自动打开外部承诺。同样，对于不是承诺的值，`.then()`就像一个异步的`.map()`。对于承诺本身的值，`.then()`的行为类似于单子的`.flatMap()`方法(有时也称为`.chain()`)。所以，承诺不完全是函子，也不完全是单子，但在实践中，你通常可以把它们当作任一种。先不要担心单子是什么。单子是函子的一种，你需要先学习函子。

有很多库也可以将各种其他东西转化为函子。

在 Haskell 中，函子类型被定义为:

```
fmap :: (a -> b) -> f a -> f b
```

给定一个函数，它接受一个`a`并返回一个`b`和一个内部有零个或多个`a`的函子:`fmap`返回一个内部有零个或多个`b`的盒子。`f a`和`f b`位可以读作“一个`a`的函子”和“一个`b`的函子”，意思是`f a`在盒子里面有`a` s，`f b`在盒子里面有`b` s。

使用仿函数很简单——只需调用`map()`:

```
const f = [1, 2, 3];
f.map(double); // [2, 4, 6]
```

# 函子定律

类别有两个重要属性:

1.  身份
2.  作文

由于函子是范畴之间的映射，所以函子必须尊重同一性和复合性。合在一起，它们被称为函子定律。

# 身份

如果将恒等式函数(`x => x`)传递给`f.map()`，其中`f`是任意一个函子，那么结果应该等同于`f`(具有相同的含义):

```
const f = [1, 2, 3];
f.map(x => x); // [1, 2, 3]
```

# 作文

函子必须服从合成定律:`F.map(x => f(g(x)))`等价于`F.map(g).map(f)`。

函数组合是一个函数对另一个函数的结果的应用，例如给定一个`x`和函数`f`和`g`，组合`(f ∘ g)(x)`(通常简称为`f ∘ g`-`(x)`是隐含的)就是`f(g(x))`。

大量的函数式编程术语来自于范畴论，范畴论的本质是复合。范畴理论一开始很吓人，但是很简单。比如从跳水板上跳下来或者坐过山车。以下是范畴理论的几个要点:

*   类别是对象和对象之间的箭头的集合(其中“对象”可以表示字面上的任何东西)。
*   箭头被称为态射。态射可以被认为是函数，并在代码中表示为函数。
*   对于任何一组相连的对象`a -> b -> c`，都必须有一个直接来自`a -> c`的合成。
*   所有的箭头都可以表示为组合(即使只是带有对象标识箭头的组合)。类别中的所有对象都有标识箭头。

假设您有一个函数`g`接受一个`a`并返回一个`b`，另一个函数`f`接受一个`b`并返回一个`c`；还必须有一个函数`h`代表`f`和`g`的组合。所以，作文出自`a -> c`，就是作文`f ∘ g`(`f`*`g`之后)。所以，`h(x) = f(g(x))`。函数组合是从右向左工作，而不是从左向右，这也是为什么`f ∘ g`在 `g`之后又被频繁称为`f` *的原因。**

*构图是联想的。基本上，这意味着当你在编写多个函数时(如果你觉得有趣，可以使用变形)，你不需要括号:*

```
*h∘(g∘f) = (h∘g)∘f = h∘g∘f*
```

*让我们再来看看 JavaScript 中的合成法则:*

*给定一个函子，`F`:*

```
*const F = [1, 2, 3];*
```

*以下内容是等效的:*

```
*F.map(x => f(g(x)));// is equivalent to...F.map(g).map(f);*
```

# *内函子*

*内函子是从一个范畴映射回同一范畴的函子。*

*函子可以从一个类别映射到另一个类别:`X -> Y`*

*内函子从一个范畴映射到同一个范畴:`X -> X`*

*单子是内函子。请记住:*

> **“单子就是内函子范畴中的幺半群。有什么问题？”**

*希望这句话开始变得更有意义了。我们稍后将讨论幺半群和幺半群。*

# *构建您自己的函子*

*这里有一个函子的简单例子:*

```
*const Identity = value => ({
  map: fn => Identity(fn(value))
});*
```

*如你所见，它满足函子定律:*

```
*// trace() is a utility to let you easily inspect
// the contents.
const trace = x => {
  console.log(x);
  return x;
};const u = Identity(2);// Identity law
u.map(trace);             // 2
u.map(x => x).map(trace); // 2const f = n => n + 1;
const g = n => n * 2;// Composition law
const r1 = u.map(x => f(g(x)));
const r2 = u.map(g).map(f);r1.map(trace); // 5
r2.map(trace); // 5*
```

*现在，您可以映射任何数据类型，就像您可以映射数组一样。不错！*

*这是 JavaScript 中函子所能达到的最简单程度，但是它缺少了 JavaScript 中数据类型的一些特性。我们来补充一下。如果`+`操作符可以处理数字和字符串值，那不是很酷吗？*

*为了实现这一点，我们需要做的就是实现`.valueOf()`——这似乎也是一种从仿函数中解开值的便捷方式:*

```
*const Identity = value => ({
  map: fn => Identity(fn(value)), valueOf: () => value,
});const ints = (Identity(2) + Identity(4));
trace(ints); // 6const hi = (Identity('h') + Identity('i'));
trace(hi); // "hi"*
```

*很好。但是如果我们想在控制台中检查一个`Identity`实例呢？如果上面写着`"Identity(value)"`，那就太酷了，对吧。让我们添加一个`.toString()`方法:*

```
*toString: () => `Identity(${value})`,*
```

*酷毙了。我们可能还应该启用标准的 JS 迭代协议。我们可以通过添加自定义迭代器来实现:*

```
*[Symbol.iterator]: function* () {
  yield value;
}*
```

*现在这将起作用:*

```
*// [Symbol.iterator] enables standard JS iterations:
const arr = [6, 7, ...Identity(8)];
trace(arr); // [6, 7, 8]*
```

*如果您想获取一个`Identity(n)`并返回一个包含`n + 1`、`n + 2`等等的标识数组，该怎么办？很简单，对吧？*

```
*const fRange = (
  start,
  end
) => Array.from(
  { length: end - start + 1 },
  (x, i) => Identity(i + start)
);*
```

*啊，但是如果你想让它适用于任何函子呢？如果我们有一个规范，规定数据类型的每个实例都必须有一个对其构造函数的引用，那会怎么样？那么你可以这样做:*

```
*const fRange = (
  start,
  end
) => Array.from(
  { length: end - start + 1 },

  // change `Identity` to `start.constructor`
  (x, i) => start.constructor(i + start)
);const range = fRange(Identity(2), 4);
range.map(x => x.map(trace)); // 2, 3, 4*
```

*如果你想测试一个值是否是函子呢？我们可以在`Identity`上添加一个静态方法来检查。我们应该加入一个静态的`.toString()`:*

```
*Object.assign(Identity, {
  toString: () => 'Identity',
  is: x => typeof x.map === 'function'
});*
```

*让我们把这些放在一起:*

```
*const Identity = value => ({
  map: fn => Identity(fn(value)),
  valueOf: () => value,
  toString: () => `Identity(${value})`,
  [Symbol.iterator]: function* () {
    yield value;
  },
  constructor: Identity
});Object.assign(Identity, {
  toString: () => 'Identity',
  is: x => typeof x.map === 'function'
});*
```

*注意，你不需要所有这些额外的东西来证明某个东西是函子或者内函子。严格来说是为了方便。对于仿函数，*所需要的*就是一个满足仿函数法则的`.map()`接口。*

# *为什么是函子？*

*函子很棒有很多原因。最重要的是，它们是一种抽象，您可以用它来实现许多有用的东西，并且可以处理任何数据类型。例如，如果你想开始一个操作链，但是只有当仿函数中的值不是`undefined`或`null`时，该怎么办？*

```
*// Create the predicate
const exists = x => (x.valueOf() !== undefined && x.valueOf() !== null);const ifExists = x => ({
  map: fn => exists(x) ? x.map(fn) : x
});const add1 = n => n + 1;
const double = n => n * 2;// Nothing happens...
ifExists(Identity(undefined)).map(trace);
// Still nothing...
ifExists(Identity(null)).map(trace);// 42
ifExists(Identity(20))
  .map(add1)
  .map(double)
  .map(trace)
;*
```

*当然，函数式编程就是组合微小的函数来创建更高层次的抽象。如果你想要一个通用的映射，可以和任何仿函数一起工作呢？这样，您可以部分应用参数来创建新函数。*

*简单。选择你最喜欢的自动咖喱，或者使用之前的魔咒:*

```
*const curry = (
  f, arr = []
) => (...args) => (
  a => a.length === f.length ?
    f(...a) :
    curry(f, a)
)([...arr, ...args]);*
```

*现在我们可以自定义地图:*

```
*const map = curry((fn, F) => F.map(fn));const double = n => n * 2;const mdouble = map(double);
mdouble(Identity(4)).map(trace); // 8*
```

# *结论*

*函子是我们可以映射的东西。更具体地说，函子是从范畴到范畴的映射。函子甚至可以从一个类别映射回同一个类别(即*内函子*)。*

*类别是对象的集合，对象之间有箭头。箭头代表态射(又名函数，又名合成)。范畴中的每个对象都有一个同一性态射(`x => x`)。对于任何对象链`A -> B -> C`，一定存在一个组合`A -> C`。*

*函子是很好的高阶抽象，允许您创建各种通用函数，适用于任何数据类型。*

*[**接下来:函数混合>**](/javascript-scene/functional-mixins-composing-software-ffb66d5e731c)*

# *通过实时 1:1 辅导提升您的技能*

*DevAnywhere 是达到高级 JavaScript 技能的最快方法:*

*   *现场课程*
*   *弹性工时*
*   *一对一指导*
*   *构建真正的生产应用*

*[![](img/03504ae5b049cdb99861a7b575be3a08.png)](https://devanywhere.io/)

[https://devanywhere.io/](https://devanywhere.io/)* 

****埃里克·艾略特*** *是* [*【编程 JavaScript 应用】*](http://pjabook.com) *(O'Reilly)的作者，也是* [的联合创始人*devanywhere . io*](https://devanywhere.io/)*。他为 Adobe Systems 的****Adobe Systems*******Zumba Fitness*******华尔街日报*******ESPN*******BBC****等顶级录音师贡献了软件经验******

**他和世界上最美丽的女人一起在任何他想去的地方工作。**