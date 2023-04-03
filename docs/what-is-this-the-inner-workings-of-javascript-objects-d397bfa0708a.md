# 这是什么？JavaScript 对象的内部工作方式

> 原文：<https://medium.com/javascript-scene/what-is-this-the-inner-workings-of-javascript-objects-d397bfa0708a?source=collection_archive---------3----------------------->

![](img/df85d3dc93b07c6091cc725b0aff5939.png)

*Photo: Curious by Liliana Saeb (CC BY 2.0)*

JavaScript 是一种多范例语言，支持面向对象编程和动态绑定。动态绑定是一个强大的概念，它允许 JavaScript 代码的结构在运行时改变，但这种额外的能力和灵活性是以一些混乱为代价的，而很多混乱都集中在 JavaScript 中的行为上。

# 动态绑定

动态绑定是确定在运行时而不是编译时调用的方法的过程。JavaScript 通过`this`和原型链实现了这一点。特别是，方法中的`this`的含义是在运行时确定的，规则根据方法的定义而变化。

让我们玩一个游戏。我把这个游戏叫做“什么是`this`？”

```
const a = {
  a: 'a'
};const obj = {
  getThis: () => this,
  getThis2 () {
    return this;
  }
};obj.getThis3 = obj.getThis.bind(obj);
obj.getThis4 = obj.getThis2.bind(obj);const answers = [
  1, obj.getThis(),
  2, obj.getThis.call(a),
  3, obj.getThis2(),
  4, obj.getThis2.call(a),
  5, obj.getThis3(),
  6, obj.getThis3.call(a),
  7, obj.getThis4(),
  8, obj.getThis4.call(a),
];
```

在你继续之前，写下你的答案。完成后，`console.log()`检查你的答案。你猜对了吗？

让我们从第一个案例开始，一步步往下。在 ES6 模块的上下文中，`obj.getThis()`返回`undefined`。在脚本标签中，它是`window`。在节点 REPL，是`global`。

为什么？箭头函数永远不能有自己的`this`绑定。相反，它们总是委托给词法范围。在 ES6 模块的根作用域中，词法作用域将有一个未定义的`this`。`obj.getThis.call(a)`也是`undefined`，同理。对于箭头功能，`this`不能重新分配，即使有`.call()`或`.bind()`。它将总是委托给词法`this`。

`obj.getThis2()`通过正常的方法调用过程获得它的绑定。如果没有先前的`this`绑定，并且函数可以绑定`this`(即，它不是一个箭头函数)，那么`this`将绑定到使用`.`或`[squareBracket]`属性访问语法调用该方法的对象。

有点棘手。方法调用一个带有给定的`this`值和可选参数的函数。换句话说，它从`.call()`参数中获取其`this`绑定，因此`obj.getThis2.call(a)`返回`a`对象。

在`obj.getThis3 = obj.getThis.bind(obj);`中，我们试图绑定一个箭头函数，我们已经确定这个函数不起作用，所以对于`obj.getThis3()`和`obj.getThis3.call(a)`，我们回到`undefined`(或`window`或`global`)。

您可以绑定常规方法，因此`obj.getThis4()`如预期的那样返回`obj`，并且因为它已经与`obj.getThis4 = obj.getThis2.bind(obj);`绑定，`obj.getThis4.call(a)`考虑第一次绑定并返回`obj`而不是`a`。

# 曲线球

同样的挑战，但是这一次，`class`使用了公共字段语法(在撰写本文时， [Stage 3](https://github.com/tc39/proposal-class-fields) ，在 Chrome 中默认可用，使用`@babel/plugin-proposal-class-properties`):

```
class Obj {
  getThis = () => this
  getThis2 () {
    return this;
  }
}const obj2 = new Obj();
obj2.getThis3 = obj2.getThis.bind(obj2);
obj2.getThis4 = obj2.getThis2.bind(obj2);const answers = [
  1, obj2.getThis(),
  2, obj2.getThis.call(a),
  3, obj2.getThis2(),
  4, obj2.getThis2.call(a),
  5, obj2.getThis3(),
  6, obj2.getThis3.call(a),
  7, obj2.getThis4(),
  8, obj2.getThis4.call(a),
];
```

在你继续之前写下你的答案。

准备好了吗？

除了`obj2.getThis2.call(a)`，这些都返回对象实例。异常返回`a`对象。箭头函数*仍然委托给词法* `*this*` *。*区别在于词法`this`对于类属性是不同的。在幕后，该类属性赋值被编译成如下形式:

```
class Obj {
  constructor() {
    this.getThis = () => this;
  }
...
```

换句话说，箭头函数是在构造函数的上下文中定义的*。*既然是类，那么创建实例的唯一方法就是使用`new`关键字(省略`new`会抛出错误)。

关键字`new`做的最重要的事情之一是实例化一个新的对象实例，并在构造函数中将`this`绑定到它。这种行为，结合我们上面已经提到的其他行为，应该可以解释其余的。

# 结论

你做得怎么样？你拿到了吗？很好地理解`this`在 JavaScript 中的行为将为您节省大量调试棘手问题的时间。如果你有任何一个答案错了，练习会对你有好处。玩这些例子，然后回来再次测试自己，直到你们都能通过测试，并向其他人解释为什么这些方法返回它们所返回的内容。

如果这比你想象的要难，你并不孤单。我在这个话题上测试了相当多的开发者，我认为到目前为止只有一个开发者通过了测试。

最初可以用`.call()`、`.bind()`或`.apply()`重定向的动态方法查找，随着`class`和 arrow 函数行为的加入，变得更加复杂。这可能有助于划分一点。请记住，arrow 函数总是将`this`委托给词法范围，而`class` `this`实际上是词法范围内的构造函数。如果你对什么是`this`有疑问，记得使用你的调试器来验证对象是你认为的那样。

还要记住，在 JavaScript 中，不使用`this`也可以做很多事情。根据我的经验，几乎任何东西都可以用纯函数来重新实现，这些纯函数将它们所应用的所有参数作为显式参数(你可以把`this`看作是具有可变状态的隐式参数)。封装在纯函数中的逻辑是确定性的，这使得它更易测试，并且没有副作用，这意味着与操作`this`不同，你不太可能破坏任何其他东西。每一次你改变`this`，你就冒了依赖于`this`值的其他东西将被破坏的风险。

也就是说，`this`有时是有用的。例如，在大量对象之间共享方法。即使在函数式编程中，`this`也可以用来访问对象上的其他方法来实现代数派生，从而在现有代数的基础上构建新的代数。例如，一个通用的`.flatMap()`可以通过访问`this.map()`和`this.flatten()`得到:

```
flatMap (f) {
  return this.map(f).flatten();
}
```

***Eric Elliott*** *是一位分布式系统专家，著有《编写软件》*[](https://leanpub.com/composingsoftware)**[*【编程 JavaScript 应用】*](http://pjabook.com) *等书籍。作为*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，他教授开发人员远程工作和拥抱工作/生活平衡所需的技能。他为加密项目组建开发团队并提供建议，并为 Adobe Systems、* ***、Zumba Fitness、*** ***、华尔街日报、*** **【T42、******BBC、*** *和顶级录音艺术家(包括* ***Usher、弗兰克·奥申、Metallica******

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**