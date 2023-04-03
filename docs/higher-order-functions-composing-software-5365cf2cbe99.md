# 高阶函数(构成软件)

> 原文：<https://medium.com/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99?source=collection_archive---------0----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分 **s** [**(现在一本书！)**](https://leanpub.com/composingsoftware) 关于从基础开始学习 JavaScript 6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30) *|* [*下一篇>*](/javascript-scene/curry-and-function-composition-2c208d774983)

**高阶函数**是以函数为自变量，或者返回函数的函数。高阶函数与一阶函数相反，一阶函数不接受函数作为参数，也不返回函数作为输出。

前面我们看到了`.map()`和`.filter()`的例子。两者都以一个函数作为自变量。它们都是高阶函数。

让我们看一个一阶函数的例子，它从单词列表中过滤所有 4 个字母的单词:

```
const censor = words => {
  const filtered = [];
  for (let i = 0, { length } = words; i < length; i++) {
    const word = words[i];
    if (word.length !== 4) filtered.push(word);
  }
  return filtered;
};censor(['oops', 'gasp', 'shout', 'sun']);
// [ 'shout', 'sun' ]
```

现在，如果我们想选择所有以“s”开头的单词呢？我们可以创造另一个功能:

```
const startsWithS = words => {
  const filtered = [];
  for (let i = 0, { length } = words; i < length; i++) {
    const word = words[i];
    if (word.startsWith('s')) filtered.push(word);
  }
  return filtered;
};startsWithS(['oops', 'gasp', 'shout', 'sun']);
// [ 'shout', 'sun' ]
```

您可能已经识别出许多重复的代码。这里形成了一种模式，可以抽象成一种更通用的解决方案。这两个功能有很多共同之处。它们都遍历一个列表，并在给定的条件下过滤它。

迭代和过滤似乎都需要被抽象出来，这样它们就可以被共享和重用来构建各种类似的功能。毕竟，从事物列表中选择事物是一项非常普通的任务。

幸运的是，JavaScript 有一流的函数。那是什么意思？就像数字、字符串或对象一样，函数可以是:

*   作为标识符(变量)值分配
*   分配给对象属性值
*   作为参数传递
*   从函数返回

基本上，我们可以像在程序中使用任何其他数据一样使用函数，这使得抽象变得容易得多。例如，我们可以创建一个函数，通过传入一个处理不同位的函数来抽象遍历列表和累积返回值的过程。我们称这个函数为*缩减器:*

```
const reduce = (reducer, initial, arr) => {
  // shared stuff
  let acc = initial;
  for (let i = 0, { length } = arr; i < length; i++) { // unique stuff in reducer() call
    acc = reducer(acc, arr[i]); // more shared stuff
  }
  return acc;
};reduce((acc, curr) => acc + curr, 0, [1,2,3]); // 6
```

这个`reduce()`实现需要一个 reducer 函数、累加器的初始值和一个要迭代的数据数组。对于数组中的每一项，调用缩减器，并向其传递累加器和当前数组元素。返回值被分配给累加器。当对列表中的所有值应用缩减后，将返回累积值。

在使用示例中，我们调用 reduce 并向其传递函数`(acc, curr) => acc + curr`，该函数获取累加器和列表中的当前值，并返回一个新的累加值。接下来我们传递一个初始值，`0`，最后是要迭代的数据。

抽象出迭代和值累积之后，现在我们可以实现一个更一般化的`filter()`函数:

```
const filter = (
  fn, arr
) => reduce((acc, curr) => fn(curr) ?
  acc.concat([curr]) :
  acc, [], arr
);
```

在`filter()`函数中，除了作为参数传入的`fn()`函数之外，所有东西都是共享的。那个`fn()`参数叫做谓词。一个**谓词**是一个返回布尔值的函数。

我们用当前值调用`fn()`，如果`fn(curr)`测试返回`true`，我们将`curr`值连接到累加器数组。否则，我们只返回当前的累加器值。

现在我们可以用`filter()`实现`censor()`来过滤掉 4 个字母的单词:

```
const censor = words => filter(
  word => word.length !== 4,
  words
);
```

哇！抽象出所有常见的东西，`censor()`是一个很小的函数。

`startsWithS()`也是如此:

```
const startsWithS = words => filter(
  word => word.startsWith('s'),
  words
);
```

如果你注意的话，你可能知道 JavaScript 已经为我们做了这些抽象工作。我们有`Array.prototype`方法、`.reduce()`和`.filter()`和`.map()`以及一些更好的测量方法。

高阶函数也常用于抽象如何操作不同的数据类型。例如，`.filter()`不必对字符串数组进行操作。它可以很容易地过滤数字，因为你可以传入一个知道如何处理不同数据类型的函数。还记得`highpass()`的例子吗？

```
const highpass = cutoff => n => n >= cutoff;
const gt3 = highpass(3);
[1, 2, 3, 4].filter(gt3); // [3, 4];
```

换句话说，您可以使用高阶函数来使函数多态。正如你所看到的，高阶函数比一阶函数有更多的可重用性和通用性。一般来说，在实际的应用程序代码中，您将结合使用高阶函数和非常简单的一阶函数。

> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一张*](/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)*|**下一张>*

# 在 EricElliottJS.com 了解更多信息

EricElliottJS.com 会员可以参加互动代码挑战视频课程。如果你还不是会员，今天就注册吧。

***Eric Elliott****是一位分布式系统专家，著有书籍* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](https://ericelliottjs.com/product/programming-javascript-applications-ebook/) *。作为*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发人员远程工作和拥抱工作/生活平衡所需的技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、****【Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *等顶级录音艺术家提供软件体验****

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**