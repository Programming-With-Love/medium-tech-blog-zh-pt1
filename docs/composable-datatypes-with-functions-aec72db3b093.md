# 带函数的可组合数据类型

> 原文：<https://medium.com/javascript-scene/composable-datatypes-with-functions-aec72db3b093?source=collection_archive---------1----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列 **s** [**(现在一本书！)**](https://leanpub.com/composingsoftware) 关于从基础开始学习 JavaScript ES6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [<上一个](/javascript-scene/why-composition-is-harder-with-classes-c3e627dcd0aa) | < < [从第 1 部分开始](/javascript-scene/composing-software-an-introduction-27b72500d6ea) | [下一个> >](/javascript-scene/javascript-monads-made-simple-7856be57bfe8)

在 JavaScript 中，最简单的组合方式是函数组合，函数只是一个可以添加方法的对象。换句话说，您可以这样做:

```
const t = value => {
  const fn = () => value; fn.toString = () => `t(${ value })`; return fn;
}; const someValue = t(2);console.log(
  someValue.toString() // "t(2)"
);
```

这是一个返回数字数据类型实例的工厂。但是请注意，这些实例不是简单的对象。相反，它们是函数，像任何其他函数一样，您可以组合它们。让我们假设它的主要用例是对其成员求和。也许在他们作曲的时候把他们相加是有意义的。

首先，我们建立一些规则(四=表示“相当于”):

*   `t(x)(t(0)) ==== t(x)`
*   `t(x)(t(1)) ==== t(x + 1)`

您可以使用我们已经创建的方便的`.toString()`方法在 JavaScript 中表达这一点:

*   `t(x)(t(0)).toString() === t(x).toString()`
*   `t(x)(t(1)).toString() === t(x + 1).toString()`

我们可以把这些转化成一种简单的单元测试:

```
const assert = {
  same: (actual, expected, msg) => {
    if (actual.toString() !== expected.toString()) {
      throw new Error(`NOT OK: ${ msg }
        Expected: ${ expected }
        Actual:   ${ actual }
      `);
    } console.log(`OK: ${ msg }`);
  }
}; {
  const msg = 'a value t(x) composed with t(0) ==== t(x)';
  const x = 20;
  const a = t(x)(t(0));
  const b = t(x);
  assert.same(a, b, msg);
}{
  const msg = 'a value t(x) composed with t(1) ==== t(x + 1)';
  const x = 20;
  const a = t(x)(t(1));
  const b = t(x + 1);
  assert.same(a, b, msg);
}
```

这些测试一开始会失败:

```
NOT OK: a value t(x) composed with t(0) ==== t(x)
        Expected: t(20)
        Actual:   20
```

但是我们可以通过三个简单的步骤让它们通过:

1.  将`fn`函数改为返回`t(value + n)`的`add`函数，其中`n`是传递的参数。
2.  向`t`类型添加一个`.valueOf()`方法，这样新的`add()`函数可以将`t()`的实例作为参数。`+`运算符将使用`n.valueOf()`的结果作为第二个操作数。
3.  用`Object.assign()`将方法分配给`add()`功能。

当你把所有这些放在一起，它看起来像这样:

```
const t = value => {
  const add = n => t(value + n); return Object.assign(add, {
    toString: () => `t(${ value })`,
    valueOf: () => value
  });
};
```

然后测试通过:

```
"OK: a value t(x) composed with t(0) ==== t(x)"
"OK: a value t(x) composed with t(1) ==== t(x + 1)"
```

现在您可以用函数组合来组合`t()`的值:

```
// Compose functions from top to bottom:
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);// Sugar to kick off the pipeline with an initial value:
const sumT = (...fns) => pipe(...fns)(t(0));sumT(
  t(2),
  t(4),
  t(-1)
).valueOf(); // 5
```

# 您可以对任何数据类型执行此操作

你的数据采取什么形状并不重要，只要有一些有意义的合成操作就行。对于列表或字符串，它可以是连接。对于 DSP，可能是信号求和。当然，许多不同的操作可能对相同的数据有意义。问题是，哪种操作最能代表构图的概念？换句话说，这样表达的话，哪种操作最有利？：

```
const result = compose(
  value1,
  value2,
  value3
);
```

# 可组合货币

货币安全是一个开源库，实现了这种可组合的功能数据类型。JavaScript 的`Number`类型无法准确表示美元的某些分数。

```
.1 + .2 === .3 // false
```

Moneysafe 通过将美元金额提高到美分来解决这个问题:

```
npm install --save moneysafe
```

然后:

```
import { $ } from 'moneysafe';$(.1) + $(.2) === $(.3).cents; // true
```

分类帐语法利用了 Moneysafe 将值提升到可组合函数中的事实。它公开了一个简单的函数组合实用程序，称为分类帐:

```
import { $ } from 'moneysafe';
import { $$, subtractPercent, addPercent } from 'moneysafe/ledger';$$(
  $(40),
  $(60),
  // subtract discount
  subtractPercent(20),
  // add tax
  addPercent(10)
).$; // 88
```

返回值是提取的货币类型的值。它公开了方便的`.$` getter，该 getter 将内部浮点美分值转换成美元，四舍五入到最接近的美分。

结果是一个直观的界面来执行分类帐风格的货币计算。

# 测试你的理解能力

克隆货币安全:

```
git clone [git@github.com](mailto:git@github.com):ericelliott/moneysafe.git
```

运行安装程序:

```
npm install
```

使用观察控制台运行单元测试。它们都应该通过:

```
npm run watch
```

在新的终端窗口中，删除实现:

```
rm source/[moneysafe.js](https://github.com/ericelliott/moneysafe/blob/master/source/moneysafe.js) && touch source/moneysafe.js
```

再看一下手表控制台测试。您应该会看到一个错误。

你的任务是使用单元测试和文档作为指导，从头开始重新实现`moneysafe.js`。

我为会员录制了一个 7 集的视频漫游系列。这是第一集:

会员们，金钱安全演练在[散弹枪系列](https://ericelliottjs.com/premium-content/shotgun-the-moneysafe-series/)中有售。

不是会员？[现在报名](https://ericelliottjs.com/product/lifetime-access-pass/)。

[***接下来:JavaScript 单子制作简单>***](/javascript-scene/javascript-monads-made-simple-7856be57bfe8)

# 后续步骤

想了解更多关于 JavaScript 函数组合的知识吗？

[跟着 Eric Elliott 学 JavaScript】。如果你不是会员，你就错过了！](http://ericelliottjs.com/product/lifetime-access-pass/)

[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他把大部分时间花在他想去的任何地方和世界上最漂亮的女人一起工作。**