# 咖喱与功能组合

> 原文：<https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983?source=collection_archive---------0----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列 **s** [**(现在一本书！)**](https://leanpub.com/composingsoftware) 关于从基础开始学习 JavaScript 6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！[*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99)*|**下一篇>*

随着主流 JavaScript 中函数式编程的迅速崛起，curried 函数在许多应用程序中变得很常见。了解它们是什么，它们是如何工作的，以及如何很好地使用它们是很重要的。

# 什么是咖喱功能？

curried 函数是一个一次接受多个参数*的函数。*给定一个有 3 个参数的函数，curried 版本将接受一个参数并返回一个接受下一个参数的函数，后者返回一个接受第三个参数的函数。最后一个函数返回将该函数应用于其所有参数的结果。

你可以用更多或更少的参数做同样的事情。例如，给定两个数字，`a`和`b`，返回`a`和`b`的和:

```
// add = a => b => Number
const add = a => b => a + b;
```

要使用它，我们必须使用函数应用程序语法应用这两个函数。在 JavaScript 中，函数引用后的括号`()`触发函数调用。当一个函数返回另一个函数时，可以通过添加一组额外的括号来立即调用返回的函数:

```
const result = add(2)(3); // => 5
```

首先，函数取`a`，然后*返回一个新函数，*再取`b`返回`a`和`b`之和。每次一个参数。如果函数有更多的参数，它可以简单地继续返回新的函数，直到提供了所有的参数，应用程序可以完成。

`add`函数接受一个参数，然后返回自身的一个**分部应用**，其中`a` **固定在闭包范围内**。一个**闭包**是一个与其词法范围捆绑在一起的函数。闭包是在运行时函数创建期间创建的。Fixed 意味着在闭包的绑定范围内为变量赋值。

上例中的括号代表函数调用:`add`被`2`调用，返回部分应用的函数，其中`a`被固定为`2`。我们没有将返回值赋给变量或使用它，而是通过将`3`传递给括号中的函数来立即调用返回的函数，这完成了应用程序并返回`5`。

# 什么是局部应用？

一个**部分应用**是一个已经被应用到它的一些，但不是全部参数的函数。换句话说，它是一个在其闭包范围内有固定参数*的函数*。某些参数固定的函数被称为*部分应用*。

# 有什么区别？

分部应用程序一次可以根据需要接受或多或少的参数。另一方面，Curried 函数总是返回一元函数:一个带一个参数的函数。

所有的约束函数都返回部分应用，但并不是所有的部分应用都是约束函数的结果。

对定制函数的一元要求是一个重要的特性。

# 什么是无点风格？

无点风格是一种编程风格，其中函数定义不引用函数的参数。让我们看看 JavaScript 中的函数定义:

```
function foo (/* parameters are declared here*/) {
  // ...
}const foo = (/* parameters are declared here */) => // ...const foo = function (/* parameters are declared here */) {
  // ...
}
```

如何在不引用所需参数的情况下在 JavaScript 中定义函数？嗯，我们不能使用`function`关键字，也不能使用箭头函数(`=>`)，因为它们需要声明任何形式参数(这会引用它的参数)。所以我们需要做的是调用一个返回函数的函数。

创建一个函数，无论你传递给它什么数字，它都用无点方式递增 1。记住，我们已经有一个名为`add`的函数，它接受一个数字并返回一个部分应用的函数，它的第一个参数固定为您传入的任何值。我们可以用它来创建一个名为`inc()`的新函数:

```
// inc = n => Number
// Adds 1 to any number.
const inc = add(1);inc(3); // => 4
```

作为一般化和特殊化的机制，这变得很有趣。返回的函数只是更一般的`add()`函数的*专门版本*。我们可以使用`add()`创建尽可能多的专用版本:

```
const inc10 = add(10);
const inc20 = add(20);inc10(3); // => 13
inc20(3); // => 23
```

当然，这些都有它们自己的闭包范围(闭包是在函数创建时创建的——当`add()`被调用时)，所以最初的`inc()`继续工作:

```
inc(3) // 4
```

当我们用函数调用`add(1)`创建`inc()`时，`add()`中的`a`参数在被赋值给`inc`的返回函数中被*固定为*到`1`。

然后当我们调用`inc(3)`时，`add()`内的`b`参数被替换为实参值`3`，应用完成，返回`1`和`3`之和。

所有的 curried 函数都是高阶函数的一种形式，它允许您为手边的特定用例创建原始函数的专门版本。

# 我们为什么要咖喱？

Curried 函数在函数组合的上下文中特别有用。

在代数中，给定两个函数，`g`和`f`:

```
g: a -> b
f: b -> c
```

您可以将这些函数组合在一起创建一个新函数，`h`从`a`直接到`c`:

```
// Algebra definition, borrowing the `.` composition operator
// from Haskellh: a -> c
h = f . g = f(g(x))
```

在 JavaScript 中:

```
const g = n => n + 1;
const f = n => n * 2;const h = x => f(g(x));h(20); //=> 42
```

代数定义:

```
f . g = f(g(x))
```

可以翻译成 JavaScript:

```
const compose = (f, g) => x => f(g(x));
```

但是一次只能完成两个功能。在代数中，可以写成:

```
f . g . h
```

我们可以写一个函数来组合任意多的函数。换句话说，`compose()`创建了一个函数管道，一个函数的输出连接到下一个函数的输入。

我通常是这样写的:

```
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);
```

这个版本接受任意数量的函数并返回一个取初始值的函数，然后使用`reduceRight()`从右到左迭代`fns`中的每个函数`f`，并依次将其应用到累计值`y`。我们在这个函数中用累加器`y`累加的是由`compose()`返回的函数的返回值。

现在我们可以这样写作文了:

```
const g = n => n + 1;
const f = n => n * 2;// replace `x => f(g(x))` with `compose(f, g)`
const h = compose(f, g);h(20); //=> 42
```

# 找到；查出

使用无指针风格的函数组合创建了非常简洁、易读的代码，但是它可能会以易于调试为代价。如果你想检查函数之间的值呢？是一个方便的工具，可以让你做到这一点。它采取了一种约定俗成的功能形式:

```
const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};
```

现在我们可以检查管道:

```
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};const g = n => n + 1;
const f = n => n * 2;/*
Note: function application order is
bottom-to-top:
*/
const h = compose(
  trace('after f'),
  f,
  trace('after g'),
  g
);h(20);
/*
after g: 21
after f: 42
*/
```

`compose()`是一个很好的工具，但是当我们需要组合两个以上的函数时，如果我们能按照从上到下的顺序阅读它们，有时会很方便。我们可以通过颠倒函数的调用顺序来实现。还有另一个叫做`pipe()`的合成工具，它以相反的顺序合成:

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
```

现在我们可以这样写上面的代码:

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};const g = n => n + 1;
const f = n => n * 2;/*
Now the function application order
runs top-to-bottom:
*/
const h = pipe(
  g,
  trace('after g'),
  f,
  trace('after f'),
);h(20);
/*
after g: 21
after f: 42
*/
```

# 咖喱和功能组合在一起

即使在函数组合的上下文之外，currying 当然也是一个有用的抽象，我们可以用它来专门化函数。例如，一个 curried 版本的`map()`可以专门做许多不同的事情:

```
const map = fn => mappable => mappable.map(fn);const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
const log = (...args) => console.log(...args);const arr = [1, 2, 3, 4];
const isEven = n => n % 2 === 0;const stripe = n => isEven(n) ? 'dark' : 'light';
const stripeAll = map(stripe);
const striped = stripeAll(arr); 
log(striped);
// => ["light", "dark", "light", "dark"]const double = n => n * 2;
const doubleAll = map(double);
const doubled = doubleAll(arr);
log(doubled);
// => [2, 4, 6, 8]
```

但是 curried 函数的真正强大之处在于它们简化了函数的组合。一个函数可以接受任意数量的输入，但只能返回一个输出。为了使函数可组合，输出类型必须与预期的输入类型一致:

```
f: a => b
g:      b => c
h: a    =>   c
```

如果上面的`g`函数需要两个参数，那么`f`的输出不会与`g`的输入一致:

```
f: a => b
g:     (x, b) => c
h: a    =>   c
```

在这个场景中，我们如何让`x`进入`g`？通常回答是要*库里* `*g*` *。*

请记住，定制函数的定义是一个一次接受多个参数的函数*通过接受第一个参数并返回一系列函数，每个函数接受下一个参数，直到收集完所有参数。*

这个定义的关键词是“一次一个”。curried 函数对于函数组合如此方便的原因是，它们将需要多个参数的函数转换为可以接受单个参数的函数，从而使它们适合函数组合管道。以前面的`trace()`函数为例:

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};const g = n => n + 1;
const f = n => n * 2;const h = pipe(
  g,
  trace('after g'),
  f,
  trace('after f'),
);h(20);
/*
after g: 21
after f: 42
*/
```

`trace()`定义了两个参数，但一次只接受一个，这允许我们对内联函数进行专门化。如果没有咖喱，我们就不能这样使用它。我们必须像这样编写管道:

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);const trace = (label, value) => {
  console.log(`${ label }: ${ value }`);
  return value;
};const g = n => n + 1;
const f = n => n * 2;const h = pipe(
  g,
  // the trace() calls are no longer point-free,
  // introducing the intermediary variable, `x`.
  x => trace('after g', x),
  f,
  x => trace('after f', x),
);h(20);
```

但是简单地奉承一个功能是不够的。您还需要确保函数期望参数以正确的顺序来专门化它们。看看如果我们再次搜索`trace()`，但是颠倒参数顺序会发生什么:

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);const trace = value => label => {
  console.log(`${ label }: ${ value }`);
  return value;
};const g = n => n + 1;
const f = n => n * 2;const h = pipe(
  g,
  // the trace() calls can't be point-free,
  // because arguments are expected in the wrong order.
  x => trace(x)('after g'),
  f,
  x => trace(x)('after f'),
);h(20);
```

如果您遇到困难，可以使用一个名为`flip()`的函数来解决这个问题，这个函数只需颠倒两个参数的顺序:

```
const flip = fn => a => b => fn(b)(a);
```

现在我们可以创建一个`flippedTrace()`函数:

```
const flippedTrace = flip(trace);
```

像这样使用它:

```
const flip = fn => a => b => fn(b)(a);
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);const trace = value => label => {
  console.log(`${ label }: ${ value }`);
  return value;
};
const flippedTrace = flip(trace);const g = n => n + 1;
const f = n => n * 2;const h = pipe(
  g,
  flippedTrace('after g'),
  f,
  flippedTrace('after f'),
);h(20);
```

但是更好的方法是首先正确地编写函数。这种风格有时被称为“数据最后”，这意味着您应该首先获取专门化的参数，然后获取函数将最后处理的数据。这给了我们函数的原始形式:

```
const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};
```

对一个`label`的`trace()`的每个应用创建一个在流水线中使用的跟踪函数的专用版本，其中标签被固定在返回的`trace`的部分应用中。所以这个:

```
const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};const traceAfterG = trace('after g');
```

…相当于这样:

```
const traceAfterG = value => {
  const label = 'after g';
  console.log(`${ label }: ${ value }`);
  return value;
};
```

如果我们把`trace('after g')`换成`traceAfterG`，意思是一样的:

```
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);const trace = label => value => {
  console.log(`${ label }: ${ value }`);
  return value;
};// The curried version of trace()
// saves us from writing all this code...
const traceAfterG = value => {
  const label = 'after g';
  console.log(`${ label }: ${ value }`);
  return value;
};const g = n => n + 1;
const f = n => n * 2;const h = pipe(
  g,
  traceAfterG,
  f,
  trace('after f'),
);h(20);
```

# 结论

**简化函数**是一个一次接受多个参数的函数，通过接受第一个参数，并返回一系列函数，每个函数接受下一个参数，直到所有参数都已固定，并且函数应用程序可以完成，此时返回结果值。

**部分应用**是一个已经被应用到它的一些——但不是全部——参数的函数。已经应用了该函数的自变量被称为*固定参数*。

**无点风格**是一种不参考参数定义函数的方式。通常，通过调用返回函数的函数来创建无点函数，例如 curried 函数。

**规约函数非常适合函数合成，**因为它们允许您轻松地将一个 n 元函数转换为函数合成管道所需的一元函数形式:管道中的函数必须正好有一个参数。

**数据最后函数**便于函数组合，因为它们可以方便地以无点方式使用。

> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一张*](/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99)*|**下一张>*

# 在 EricElliottJS.com 了解更多信息

EricElliottJS.com 会员可以参加互动代码挑战视频课程。如果你还不是会员，今天就注册吧。

***Eric Elliott****是一位分布式系统专家，著有《编写软件》*[](https://leanpub.com/composingsoftware)**[*【编程 JavaScript 应用】*](https://ericelliottjs.com/product/programming-javascript-applications-ebook/) *等书籍。作为*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发人员远程工作和拥抱工作/生活平衡所需的技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、****【Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *等顶级录音艺术家提供软件体验*****

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**