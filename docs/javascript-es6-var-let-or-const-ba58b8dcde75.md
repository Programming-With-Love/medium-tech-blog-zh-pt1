# JavaScript ES6+: var，let，还是 const？

> 原文：<https://medium.com/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75?source=collection_archive---------0----------------------->

![](img/c648031168b4e0523ae241c6386dc664.png)

Hard to Choose — Manolo Guijarro (CC BY-NC-ND 2.0)

也许成为一名更好的程序员你能学到的最重要的事情是**保持事情简单**。在标识符的上下文中，这意味着**一个单一的标识符应该只用来表示一个单一的概念。**

有时很容易创建一个标识符来表示一些数据，然后使用该标识符作为临时存储从一种表示转换到另一种表示的值的地方。

例如，您可能需要一个查询字符串参数值，并从存储整个 URL 开始，然后只存储查询字符串，然后是值。应该避免这种做法。

如果您对 URL 使用一个标识符，对查询字符串使用另一个标识符，最后使用一个标识符来存储您需要的参数值，这就更容易理解了。

这就是为什么在 ES6 中我更喜欢*【常数】*而不是*【let`*。在 JavaScript 中， ***`const`* 表示标识符不能被重新分配。**(不要与*不可变值混淆。*与那些由 Immutable.js 和 Mori 产生的真正的不可变数据类型不同， *`const`* 对象可以具有变异的属性。)

如果我不需要重新分配，***【const`*是我的默认选择**而不是*【let`*，因为我希望代码中的用法尽可能清晰。

当我需要重新分配一个变量时，我使用 *`let`* 。因为我**用一个变量来表示一件事，***` let`*的用例倾向于循环或数学算法。

我在生产 ES6 代码中不使用 *`var`* 。循环在块范围内是有价值的，但是我想不出有哪种情况比 *`let`* 更适合我。在 REPL(读取求值打印循环)环境中，如调试器或节点控制台中，当您只是在试验时，它会很方便，重新分配可能会很有用。

***【const`***是表示**标识符不会被重新分配的信号。**

***`let`*** 是一个信号，表示**变量可能被重新分配**，比如循环中的计数器，或者算法中的值交换。它还表明变量将只在中定义的块中使用**，而不总是整个包含函数。**

***`var`*** 现在是在 JavaScript 中定义变量时可用的最弱信号**。该变量可能会也可能不会被重新分配，该变量可能会也可能不会用于整个函数，或者仅用于一个块或循环。**

## **警告:**

**对于 ES6 中的 *`let`* 和 *`const`* ，使用 *`typeof`:* 检查标识符的存在不再安全**

```
function foo () {
  typeof bar;
  let bar = ‘baz’;
}foo(); // ReferenceError: can't access lexical declaration
       // `bar' before initialization
```

**但是你会没事的，因为你采纳了我在【JavaScript 应用程序编程”中的建议，并且你总是在尝试使用标识符之前初始化它们…**

## **附言**

**如果你需要通过取消设置来释放一个值，你可以考虑用 *`let`* 而不是 *`const`,* 但是如果你真的需要对垃圾收集器进行微观管理，你可能应该看看“Slay'n the Waste Monster ”,而不是:**

**[![](img/649c1c875d8140aa42e9e3d9ffedf8e5.png)](https://ericelliottjs.com/premium-content/lesson-pure-functions)

[Start your free lesson on EricElliottJS.com](https://ericelliottjs.com/premium-content/lesson-pure-functions)** 

*****埃里克·埃利奥特*** *是一位科技产品和平台顾问，著有* [*【作曲软件】*](https://leanpub.com/composingsoftware) *，*[*EricElliottJS.com*](https://ericelliottjs.com)*和*[*devanywhere . io*](https://devanywhere.io)*，也是 dev 团队导师。他曾为* ***Adobe Systems、*** ***【华尔街日报】、*******【ESPN】、*******【BBC】、*** *以及包括* ***Usher、【Metallica】*在内的顶级录音艺术家******

****他和世界上最美丽的女人享受着与世隔绝的生活方式。****