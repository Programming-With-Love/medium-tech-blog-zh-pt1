# 在 JavaScript 中处理 null 和 undefined

> 原文：<https://medium.com/javascript-scene/handling-null-and-undefined-in-javascript-1500c65d51ae?source=collection_archive---------0----------------------->

![](img/0e64511f128becbcf8e990a339f1b899.png)

Image Credit: [NASA/CXC/M.Weiss](http://www.sun.org/images/black-hole-cygnus-x-1)

JavaScript 开发的一个方面是处理可选值，这是许多开发人员都很头疼的。最大限度地减少由可能是`null`、`undefined`或在运行时未初始化的值引起的错误的最佳策略是什么？

一些语言为这些情况提供了内置的启示。在一些静态类型语言中，你可以说`null`和`undefined`是非法值，并让你的编程语言在编译时抛出一个 TypeError，但即使在那些语言中，也不能阻止空输入在运行时流入程序。

为了更好地处理这个问题，我们需要了解这些值来自哪里。以下是一些最常见的来源:

*   用户输入
*   数据库/网络记录
*   未初始化状态
*   不返回任何值的函数

# 用户输入

当你处理用户输入时，验证是第一道也是最好的防线。我经常依靠模式验证器来帮助完成这项工作。例如，查看 [react-jsonschema-form](https://rjsf-team.github.io/react-jsonschema-form/) 。

# 输入的补水记录

我总是通过水化功能传递从网络、数据库或用户接收的输入。例如，我将使用能够处理`undefined`值的 [redux action creators](/javascript-scene/10-tips-for-better-redux-architecture-69250425af44) 来合并用户记录:

```
const setUser = ({ name = 'Anonymous', avatar = 'anon.png' } = {}) => ({
  type: setUser.type,
  payload: {
    name,
    avatar
  }
});
setUser.type = 'userReducer/setUser';
```

有时，您需要根据数据的当前状态显示不同的内容。如果有可能在所有数据初始化之前显示一个页面，您可能会发现自己处于这种情况。例如，当您向用户显示资金余额时，您可能会在数据加载之前意外地显示 0 美元余额。我已经多次看到这让用户感到不安。您可以创建基于当前状态生成不同输出的自定义数据类型:

```
const createBalance = ({
  // default state
  state = 'uninitialized',
  value = createBalance.empty
} = {}) => createBalance.isValidState(state) && ({
  __proto__: {
    uninitialized: () => '--',
    initialized: () => value,
    format () {
      return this[this.getState()](value);
    },
    getState: () => state,
    set: value => {
      const test = Number(value);
      assert(!Number.isNaN(test), `setBalance Invalid value: ${ value }`);
      return createBalance({
        state: 'initialized',
        value
      });
    }
  }
});
createBalance.empty = '0';
createBalance.isValidState = state => {
  if (!['uninitialized', 'initialized'].includes(state)) {
    throw new Error(`createBalance Invalid state: ${ state }`);
  }
  return true;
};const setBalance = value => createBalance().set(value);const emptyBalanceForDisplay = createBalance()
  .format();
console.log(emptyBalanceForDisplay); // '--'const balanceForDisplay = setBalance('25')
  .format(balance);
console.log(balanceForDisplay); // '25'// Uncomment these calls to see the error cases:
// setBalance('foo'); // Error: setBalance Invalid value: foo// Error: createBalance Invalid state: THIS IS NOT VALID
// createBalance({ state: 'THIS IS NOT VALID', value: '0' });
```

上面的代码是一个状态机，它不可能显示无效的状态。当您首次创建天平时，它将被设置为`uninitialized`状态。如果您试图在状态为`uninitialized`时显示余额，您将总是得到一个占位符值(`--`)。

要改变这一点，您必须通过调用`.set`方法或者我们在`createBalance`工厂下面定义的`setBalance`快捷方式来显式设置一个值。

状态本身被[封装](/javascript-scene/encapsulation-in-javascript-26be60e325b4)以保护它不受外界干扰，确保其他函数不能抓取它并将其设置为无效状态。

> *注意:如果您想知道为什么我们在这里使用字符串而不是数字，这是因为我用具有大量小数精度的大数字字符串来表示货币类型，以避免舍入误差，并准确地表示加密货币交易的值，加密货币交易可以具有任意有效的小数精度。*

如果你使用 Redux 或 Redux 架构，你可以用 [Redux-DSM](https://github.com/ericelliott/redux-dsm) 声明状态机。

# 避免创建`null`和`undefined`值

在您自己的函数中，您可以避免一开始就创建`null`或`undefined`值。有几种内置于 JavaScript 中的方法可以做到这一点。见下文。

# 避免空值

我从来没有在 JavaScript 中显式地创建过`null`值，因为我从来没有真正明白拥有两个不同的原始值有什么意义，这实际上意味着“这个值不存在”

自 2015 年以来，JavaScript 支持默认值，当您没有为有问题的参数或属性提供值时，会填充默认值。这些默认值不适用于`null`值。根据我的经验，这通常是一个错误。为了避免这个陷阱，不要在 JavaScript 中使用`null`。

如果您需要未初始化或空值的特殊情况，状态机是一个更好的选择。见上文。

# 新的 JavaScript 特性

有几个特性可以帮助您处理`null`或`undefined`值。在我写这篇文章的时候，这两个都是第三阶段的提议，但是如果你是从未来开始阅读，你也许可以使用它们。

在撰写本文时，可选链接是第 3 阶段的提议。它是这样工作的:

```
const foo = {};
// console.log(foo.bar.baz); // throws error
console.log(foo.bar?.baz) // undefined
```

## 零融合算子

还有一个第三阶段的提议将被添加到规范中，“无效合并操作符”基本上是“回退值操作符”的一种奇特说法。如果左边的值是`undefined`或`null`，则计算右边的值。它是这样工作的:

```
let baz;
console.log(baz); // undefined
console.log(baz ?? 'default baz');
// default baz// Combine with optional chaining:
console.log(foo.bar?.baz ?? 'default baz');
// default baz
```

如果未来还没有到来，你需要安装`@babel/plugin-proposal-optional-chaining`和`@babel/plugin-proposal-nullish-coalescing-operator`。

# 与承诺不同步

如果一个函数可能没有返回值，那么将它包装在一个。在函数式编程中，要么 monad 是一种特殊的抽象数据类型，它允许您附加两种不同的代码路径:成功路径或失败路径。JavaScript 有一个名为 Promise 的内置异步非单子数据类型。您可以使用它对未定义的值进行声明性错误分支:

```
const exists = x => x != null;const ifExists = value => exists(value) ?
  Promise.resolve(value) :
  Promise.reject(`Invalid value: ${ value }`);ifExists(null).then(log).catch(log); // Invalid value: null
ifExists('hello').then(log).catch(log); // hello
```

如果你愿意，你可以写一个同步版本，但是我不太需要它。我会把它作为一个练习留给你。如果你在[函子](/javascript-scene/functors-categories-61e031bac53f)和[单子](/javascript-scene/javascript-monads-made-simple-7856be57bfe8)方面有很好的功底，这个过程会比较容易。如果这听起来很吓人，不用担心。只用承诺。它们是内置的，大部分时间都能正常工作。

# 可能的数组

数组实现了一个`map`方法，该方法采用一个应用于数组每个元素的函数。如果数组为空，则永远不会调用该函数。换句话说，JavaScript 中的数组可以充当 Haskell 等语言中的“可能”的角色。

## 什么是可能？

Maybe 是一种特殊的抽象数据类型，它封装了一个可选值。数据类型有两种形式:

*   只是—一个包含值的可能
*   什么都没有——一个没有价值的可能

这个想法的要点是:

```
const log = x => console.log(x);
const exists = x => x != null;const Just = value => ({
  map: f => Just(f(value)),
});const Nothing = () => ({
  map: () => Nothing(),
});const Maybe = value => exists(value) ?
  Just(value) :
  Nothing();const empty = undefined;
Maybe(empty).map(log); // does not log
Maybe('Maybe Foo').map(log); // logs "Maybe Foo"
```

这只是一个演示概念的例子。你可以围绕可能构建一个有用的函数库，实现其他操作，如`flatMap`和`flat`(例如，当你组合多个可能返回的函数时，避免使用`Just(Just(value))`)。但是 JavaScript 已经有一种数据类型实现了很多现成的特性，所以我通常用数组来代替。

如果你想创建一个可能产生也可能不产生结果的函数(特别是当有多个结果时)，你可能有一个很好的用例来返回一个数组。

```
const log = x => console.log(x);
const exists = x => x != null;const arr = [1,2,3];
const find = (p, list) => [list.find(p)].filter(exists);
find(x => x > 3, arr).map(log); // does not log anything
find(x => x < 3, arr).map(log); // logs 1
```

我发现在空列表中不会调用`map`这一事实对于避免`null`和`undefined`值非常有用，但是请记住，如果数组包含`null`和`undefined`值，它将调用具有这些值的函数，因此如果您正在运行的函数可能产生`null`或`undefined`，您将需要从返回的数组中过滤掉它们，如上所示。这可能会改变集合的长度。

在 Haskell 中，有一个函数`maybe`(像`map`)将函数应用于一个值。但是这个值是可选的，并且封装在一个`Maybe`中。我们可以使用 JavaScript 的`Array`数据类型做本质上相同的事情:

```
// maybe = b => (a => b) => [a] => b
const maybe = (fallback, f = () => {}) => arr =>
  arr.map(f)[0] || fallback;// turn a value (or null/undefined) into a maybeArray
const toMaybeArray = value => [value].filter(exists);// maybe multiply the contents of an array by 2,
// default to 0 if the array is empty
const maybeDouble = maybe(0, x => x * 2);const emptyArray = toMaybeArray(null);
const maybe2 = toMaybeArray(2);// logs: "maybeDouble with fallback:  0"
console.log('maybeDouble with fallback: ', maybeDouble(emptyArray));
// logs: "maybeDouble(maybe2):  4"
console.log('maybeDouble(maybe2): ', maybeDouble(maybe2));
```

`maybe`接受一个回退值，然后接受一个映射到 maybe 数组的函数，再接受一个 maybe 数组(一个包含一个值或不包含任何值的数组)，并返回对数组内容应用函数的结果，或者返回回退值(如果数组为空)。

为了方便起见，我还定义了一个`toMaybeArray`函数，并修改了`maybe`函数，使其在本演示中更加明显。

如果您想在生产代码中做类似的事情，我已经创建了一个单元测试开源库来使它更容易。它叫做 [Maybearray](https://github.com/ericelliott/maybearray) 。Maybearray 相对于其他 JavaScript Maybe 库的优势在于，它使用原生 JavaScript 数组来表示值，因此您不必对它们进行任何特殊处理或做任何特殊的来回转换。当你在调试中遇到 Maybe 数组时，你不必问“这是什么古怪的类型？!"它只是一个值的数组或者一个空数组，你已经见过无数次了。

# 后续步骤

在[EricElliottJS.com](https://ericelliottjs.com/)上有更多的内容，包括许多视频、练习、录制的截屏和快速提示。如果你不是会员，现在是一个伟大的时间来看看你错过了什么！

[![](img/51899c049d6eec343d3330a2f8dde537.png)](https://ericelliottjs.com/premium-content/lesson-pure-functions)

[Start your free lesson on EricElliottJS.com](https://ericelliottjs.com/premium-content/lesson-pure-functions)

***艾里克·艾略特*** *著有《书籍》、* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](https://www.amazon.com/Programming-JavaScript-Applications-Architecture-Libraries-dp-1491950293/dp/1491950293/ref=as_li_ss_tl?_encoding=UTF8&language=en_US&linkCode=ll1&linkId=06971c7a0f2b13309e5af242b2483609&me=&qid=&tag=eejs-20) *。作为*[*【EricElliottJS.com】*](https://ericelliottjs.com/)*和*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发者必备的软件开发技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *以及包括* ***Usher、弗兰克·奥申、金属乐队在内的顶级录音******

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**