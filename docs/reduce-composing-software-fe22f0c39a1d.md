# 减少(合成软件)

> 原文：<https://medium.com/javascript-scene/reduce-composing-software-fe22f0c39a1d?source=collection_archive---------3----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分**s**T7**(现在一本书！)** 从基础开始学习 JavaScript ES6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [<上一篇](/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99#.su6cmn4f7) | [< <从第 1 部分开始](/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c#.2dfd6n6qe) | [下一篇>](/javascript-scene/functors-categories-61e031bac53f#.4hqndcx22)

**Reduce** (又名:fold，accumulate)函数式编程中常用的实用程序，它允许你迭代一个列表，将一个函数应用于一个累积值和列表中的下一项，直到迭代完成并且累积值被返回。用 reduce 可以实现很多有用的东西。通常，这是对一组项目进行任何重要处理的最优雅的方式。

Reduce 采用一个 reducer 函数和一个初始值，并返回累加值。对于`Array.prototype.reduce()`，初始列表由`this`提供，因此它不是参数之一:

```
array.reduce(
  reducer: (accumulator: Any, current: Any) => Any,
  initialValue: Any
) => accumulator: Any
```

让我们对一个数组求和:

```
[2, 4, 6].reduce((acc, n) => acc + n, 0); // 12
```

对于数组中的每个元素，调用缩减器，并向其传递累加器和当前值。reducer 的工作是以某种方式将当前值“折叠”成累积值。没有指定 How，而指定 how 是 reducer 函数的目的。缩减器返回新的累积值，并且`reduce()`移动到数组中的下一个值。reducer 可能需要一个初始值来开始，所以大多数实现都将初始值作为参数。

在这个求和缩减器的例子中，第一次调用缩减器时，`acc`从`0`开始(我们作为第二个参数传递给`.reduce()`的值)。减速器返回`0` + `2` ( `2`是数组中的第一个元素)，也就是`2`。对于下一次调用，`acc = 2, n = 4`，reducer 返回`2 + 4` ( `6`)的结果。最后一次迭代，`acc = 6, n = 6`，减速器返回`12`。由于迭代结束，`.reduce()`返回最终累加值，`12`。

在本例中，我们传入了一个匿名的 reducing 函数，但是我们可以对它进行抽象并给它一个名称:

```
const summingReducer = (acc, n) => acc + n;
[2, 4, 6].reduce(summingReducer, 0); // 12
```

正常情况下，`reduce()`从左到右工作。在 JavaScript 中，我们也有`[].reduceRight()`，它从右向左工作。换句话说，如果您将`.reduceRight()`应用到`[2, 4, 6]`，第一次迭代将使用`6`作为`n`的第一个值，并且它将返回并以`2`结束。

# Reduce 是通用的

Reduce 是通用的。使用 reduce 很容易定义`map()`、`filter()`、`forEach()`和许多其他有趣的东西:

**地图:**

```
const map = (fn, arr) => arr.reduce((acc, item, index, arr) => {
  return acc.concat(fn(item, index, arr));
}, []);
```

对于 map，我们的累积值是一个新数组，原始数组中的每个值都有一个新元素。新值是通过将传入的映射函数(`fn`)应用于`arr`参数中的每个元素而生成的。我们通过用当前元素调用`fn`来累加新数组，并将结果连接到累加器数组`acc`。

**过滤器:**

```
const filter = (fn, arr) => arr.reduce((newArr, item) => {
  return fn(item) ? newArr.concat([item]) : newArr;
}, []);
```

Filter 的工作方式与 map 非常相似，只是我们采用了一个谓词函数，如果元素通过了谓词检查(`fn(item)`返回`true`)，那么*有条件地*将当前值附加到新数组中。

对于上面的每个例子，您都有一个数据列表，应用某个函数对该数据进行迭代，并将结果合并到一个累积值中。许多应用程序跃入脑海。但是如果你的数据是一个函数列表呢？

**撰写:**

Reduce 也是一种构造函数的便捷方式。记住函数组合:如果你想将函数`f`应用于`x`的`g`的结果，即组合`f . g`，你可以使用下面的 JavaScript:

```
f(g(x))
```

Reduce 让我们能够抽象出处理任意数量函数的过程，因此您可以很容易地定义一个函数来表示:

```
f(g(h(x)))
```

为此，我们需要反向运行 reduce。也就是说，从右到左，而不是从左到右。谢天谢地，JavaScript 提供了一个`.reduceRight()`方法:

```
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
```

> 注意:如果 JavaScript 没有提供`[].reduceRight()`，您仍然可以使用`reduce()`实现`reduceRight()`。我将把它留给喜欢冒险的读者去弄清楚如何做到这一点。

**管道:**

`compose()`如果你想从里到外表现作品——也就是说，在数学符号的意义上。但是如果你想把它想象成一系列事件呢？

想象一下，我们想给一个数字加上`1`，然后使它翻倍。使用' compose()，将会是:

```
const add1 = n => n + 1;
const double = n => n * 2;const add1ThenDouble = compose(
  double,
  add1
);add1ThenDouble(2); // 6
// ((2 + 1 = 3) * 2 = 6)
```

看到问题了吗？第一步列在最后，所以为了理解这个顺序，你需要从列表的底部开始，然后一步步回到顶部。

或者，我们可以像平常一样减少从左到右，而不是从右到左:

```
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);
```

现在你可以这样写`add1ThenDouble()`:

```
const add1ThenDouble = pipe(
  add1,
  double
);add1ThenDouble(2); // 6
// ((2 + 1 = 3) * 2 = 6)
```

这一点很重要，因为有时如果你逆向写作，你会得到不同的结果:

```
const doubleThenAdd1 = pipe(
  double,
  add1
);doubleThenAdd1(2); // 5
```

稍后，我们将更详细地介绍`compose()`和`pipe()`。你现在应该明白的是`reduce()`是一个非常强大的工具，你真的需要学习它。请注意，如果你对 reduce 非常棘手，有些人可能很难跟上。

# 关于 Redux 的一句话

您可能听说过术语“reducer ”,用来描述 Redux 的重要状态更新位。在撰写本文时，Redux 是使用 React 和 Angular(后者通过`ngrx/store`)构建的 web 应用程序最流行的状态管理库/架构。

Redux 使用 reducer 函数来管理应用程序状态。Redux 样式的缩减器接受当前状态和一个操作对象，并返回新状态:

```
reducer(state: Any, action: { type: String, payload: Any}) => newState: Any
```

Redux 有一些你需要记住的 reducer 规则:

1.  不带参数调用的缩减器应该返回其有效的初始状态。
2.  如果 reducer 不打算处理动作类型，它仍然需要返回状态。
3.  redux reducer**必须是纯函数。**

让我们将求和缩减器重写为 Redux 风格的缩减器，减少过度动作对象:

```
const ADD_VALUE = 'ADD_VALUE';const summingReducer = (state = 0, action = {}) => {
  const { type, payload } = action; switch (type) {
    case ADD_VALUE:
      return state + payload.value;
    default: return state;
  }
};
```

Redux 很酷的一点是，缩减器只是标准的缩减器，你可以插入到任何尊重缩减器函数签名的`reduce()`实现中，包括`[].reduce()`。这意味着您可以创建一个 action 对象数组，并对它们进行缩减，以获得一个状态快照，该快照表示如果这些相同的操作被分派到您的存储时您将拥有的相同状态:

```
const actions = [
  { type: 'ADD_VALUE', payload: { value: 1 } },
  { type: 'ADD_VALUE', payload: { value: 1 } },
  { type: 'ADD_VALUE', payload: { value: 1 } },
];actions.reduce(summingReducer, 0); // 3
```

这使得单元测试 Redux 风格的减速器变得轻而易举。

# 结论

您应该开始看到 reduce 是一个非常有用和通用的抽象。它肯定比 map 或 filter 更难理解，但它是函数式编程实用工具带中的一个基本工具——您可以用它来制作许多其他优秀的工具。

[**接下来:函子&类别>**](/javascript-scene/functors-categories-61e031bac53f#.4hqndcx22)

# 后续步骤

想了解更多关于 JavaScript 函数式编程的知识吗？

[跟随 Eric Elliott](http://ericelliottjs.com/product/lifetime-access-pass/) 学习 JavaScript。如果你不是会员，你就错过了！

[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******Zumba Fitness*******【华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**