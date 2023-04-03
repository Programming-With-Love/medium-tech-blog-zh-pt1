# 一个函数式程序员对 JavaScript(编写软件)的介绍

> 原文：<https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30?source=collection_archive---------0----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分 **s** [**(现在一本书！)**](https://leanpub.com/composingsoftware) 关于从基础开始学习 JavaScript 6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！[*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0) *|* [*下一篇>*](/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99)

对于那些不熟悉 JavaScript 或 ES6+的人来说，这只是一个简单的介绍。无论您是初学者还是有经验的 JavaScript 开发人员，您都可能会学到一些新东西。下面的内容仅仅是皮毛，让你兴奋起来。如果你想知道更多，你只能更深入地探索。前面还有很多。

学习编码的最好方法是编码。我建议你使用交互式 JavaScript 编程环境，如 [CodePen](https://codepen.io/) 或 [Babel REPL](https://babeljs.io/repl/) 。

或者，您可以不使用节点或浏览器控制台副本。

# 表达式和值

表达式是计算结果为值的一段代码。

以下是 JavaScript 中所有有效的表达式:

```
7;7 + 1; // 87 * 2; // 14'Hello'; // Hello
```

表达式的值可以给定一个名称。这样做时，首先计算表达式，并将结果值赋给名称。为此，我们将使用`const`关键字。这不是唯一的方法，但却是您最常用的方法，所以我们现在还是坚持使用`const`:

```
const hello = 'Hello';
hello; // Hello
```

# var、let 和 const

JavaScript 支持另外两个变量声明关键字:`var`和`let`。我喜欢按照选择的顺序来考虑它们。默认情况下，我选择最严格的声明:`const`。用`const`关键字声明的变量不能被重新赋值。最终值必须在声明时赋值。这听起来可能有些僵硬，但这种限制是一件好事。这是一个信号，告诉你，“赋予这个名字的值不会改变”。它可以帮助你马上完全理解名字的含义，而不需要阅读整个函数或块范围。

有时重新分配变量很有用。例如，如果您正在使用手动的、命令式的迭代，而不是功能性更强的方法，那么您可以迭代一个用`let`赋值的计数器。

因为`var`告诉你的变量最少，所以是最弱的信号。自从我开始使用 ES6 以来，我从来没有在一个真正的软件项目中故意声明过一个`var`。

请注意，一旦用`let`或`const`声明了一个变量，任何再次声明它的尝试都将导致错误。如果您喜欢在 REPL(读取、评估、打印循环)环境中有更多的实验灵活性，您可以使用`var`而不是`const`来声明变量。允许重新声明`var`。

本文将使用`const`,以便让您养成在实际程序中默认使用`const`的习惯，但是出于交互实验的目的，您可以随意替换`var`。

# 类型

到目前为止，我们已经看到了两种类型:数字和字符串。JavaScript 还有布尔值(`true`或`false`)、数组、对象等等。我们稍后将讨论其他类型。

数组是有序的值列表。把它想象成一个可以装很多物品的盒子。下面是数组文字符号:

```
[1, 2, 3];
```

当然，这是一个可以命名的表达式:

```
const arr = [1, 2, 3];
```

JavaScript 中的对象是键:值对的集合。它还有一个文字符号:

```
{
  key: 'value'
}
```

当然，您可以给一个对象指定一个名称:

```
const foo = {
  bar: 'bar'
}
```

如果您想将现有变量分配给同名的对象属性键，有一个快捷方式。您可以只键入变量名，而不是同时提供键和值:

```
const a = 'a';
const oldA = { a: a }; // long, redundant way
const oA = { a }; // short an sweet!
```

为了好玩，我们再来一次:

```
const b = 'b';
const oB = { b };
```

对象可以很容易地组合成新对象:

```
const c = {...oA, ...oB}; // { a: 'a', b: 'b' }
```

这些点是对象扩展操作符。它遍历`oA`中的属性并将它们分配给新对象，然后对`oB`做同样的事情，覆盖新对象上已经存在的任何键。在撰写本文时，对象扩散是一个新的实验性功能，可能还不能在所有流行的浏览器中使用，但如果它对您不起作用，有一个替代品:`Object.assign()`:

```
const d = Object.assign({}, oA, oB); // { a: 'a', b: 'b' }
```

在`Object.assign()`的例子中只需要多输入一点点，如果你正在编写大量的对象，它甚至可以节省你一些输入。注意，当你使用`Object.assign()`时，你必须传递一个目标对象作为第一个参数。它是属性将被复制到的对象。如果您忘记并忽略了目标对象，那么您在第一个参数中传递的对象将会发生变异。

根据我的经验，改变现有对象而不是创建新对象通常是一个错误。最起码是容易出错的。小心`Object.assign()`。

# 解构

对象和数组都支持析构，这意味着您可以从它们中提取值并将它们赋给命名变量:

```
const [t, u] = ['a', 'b'];
t; // 'a'
u; // 'b'const blep = {
  blop: 'blop'
};

// The following is equivalent to:
// const blop = blep.blop;
const { blop } = blep;
blop; // 'blop'
```

和上面的数组例子一样，你可以一次析构多个赋值。这里有一行你会在很多 Redux 项目中看到:

```
const { type, payload } = action;
```

下面是它在一个 reducer 环境中的用法(后面会有更多关于这个主题的内容):

```
const myReducer = (state = {}, action = {}) => {
  const { type, payload } = action;
  switch (type) {
    case 'FOO': return Object.assign({}, state, payload);
    default: return state;
  }
};
```

如果您不想为新绑定使用不同的名称，可以分配一个新名称:

```
const { blop: bloop } = blep;
bloop; // 'blop'
```

阅读:将`blep.blop`指定为`bloop`。

# 比较和术语

您可以使用严格相等运算符(有时称为“三重等于”)来比较值:

```
3 + 1 === 4; // true
```

还有一个草率的等式运算符。它的正式名称是“相等”运算符。非正式地说，“双倍相等”。Double equals 有一两个有效的用例，但是默认使用`===`操作符几乎总是更好。

其他比较运算符包括:

*   `>`大于
*   `<`小于
*   `>=`大于或等于
*   `<=`小于或等于
*   `!=`不相等
*   `!==`不严格相等
*   `&&`逻辑与
*   `||`逻辑或

三元表达式是一种允许您使用比较器提问的表达式，它根据表达式是否为真来计算不同的答案:

```
14 - 7 === 7 ? 'Yep!' : 'Nope.'; // Yep!
```

# 功能

JavaScript 有函数表达式，可以分配给名称:

```
const double = x => x * 2;
```

这与数学函数`f(x) = 2x`的意思相同。大声说出来，这个函数读作`x`的`f`等于`2x`。这个函数只有当你把它应用到一个特定的`x`值时才有意思。要在其他方程中使用这个函数，你可以写`f(2)`，它和`4`有相同的意思。

换句话说，`f(2) = 4`。您可以将数学函数视为从输入到输出的映射。`f(x)`在这种情况下是`x`的输入值到相应输出值的映射，该输出值等于输入值和`2`的乘积。

在 JavaScript 中，函数表达式的值就是函数本身:

```
double; // [Function: double]
```

您可以使用`.toString()`方法查看函数定义:

```
double.toString(); // 'x => x * 2'
```

如果你想将一个函数应用于一些参数，你必须用一个函数调用来调用它。函数调用将函数应用于其参数，并计算返回值。

您可以使用`<functionName>(argument1, argument2, ...rest)`调用功能。例如，要调用我们的 double 函数，只需添加括号并向 double 传递一个值:

```
double(2); // 4
```

与一些函数式语言不同，这些括号是有意义的。没有它们，函数就不会被调用:

```
double 4; // SyntaxError: Unexpected number
```

# 签名

函数有签名，签名包括:

1.  一个*可选的*函数名。
2.  括号中的参数类型列表。参数可以选择命名。
3.  返回值的类型。

JavaScript 中不需要指定类型签名。JavaScript 引擎会在运行时计算出类型。如果你提供足够的线索，签名也可以通过 IDEs(集成开发环境)和 [Tern.js](http://ternjs.net/) 等开发工具使用数据流分析推断出来。

JavaScript 缺乏自己的函数签名符号，因此有一些竞争标准:JSDoc 在历史上非常受欢迎，但它非常冗长，而且没有人愿意让文档注释与代码保持同步，因此许多 JS 开发人员已经停止使用它。

TypeScript 和 Flow 是目前最大的竞争者。我不确定如何用这两种语言表达我需要的一切，所以我使用 [Rtype](https://github.com/ericelliott/rtype) ，这只是为了便于记录。有些人选择了哈斯克尔的纯咖喱食品[欣德利-米尔纳型](http://web.cs.wpi.edu/~cs4536/c12/milner-damas_principal_types.pdf)。我希望看到一个好的 JavaScript 标准化符号系统，即使只是为了文档的目的，但是我不认为目前的任何解决方案能够胜任这项任务。现在，眯着眼睛，尽最大努力跟上奇怪的类型签名，它们可能看起来与您正在使用的略有不同。

```
functionName(param1: Type, param2: Type) => Type
```

double 的签名是:

```
double(x: n) => Number
```

尽管事实上 JavaScript 并不要求对签名进行注释，但是了解什么是签名*以及它们的含义*对于有效地交流如何使用函数以及如何组成函数仍然很重要。**

# **默认参数值**

**JavaScript 支持默认参数值。以下函数的工作方式类似于 identity 函数(返回与您传入的值相同的值的函数)，除非您用`undefined`调用它，或者根本不传递任何参数，否则它将返回零:**

```
**const orZero = (n = 0) => n;**
```

**要设置默认值，只需在函数签名中用`=`操作符将其赋给参数，如上面的`n = 0`所示。当您以这种方式分配默认值时，类型推断工具(如 [Tern.js](http://ternjs.net/) 、Flow 或 TypeScript)可以自动推断您的函数的类型签名，即使您没有显式声明类型注释。**

**结果是，通过在编辑器或 IDE 中安装正确的插件，您将能够在键入函数调用时看到内嵌显示的函数签名。您还将能够根据函数的调用签名一眼就明白如何使用函数。在任何有意义的地方使用默认赋值可以帮助您编写更多自文档化的代码。**

> **注意:带默认值的参数不计入函数的`.length`属性，这将丢弃依赖于`.length`值的实用程序，如 autocurry。一些 curry 实用程序(如`lodash/curry`)允许您传递一个自定义 arity 来解决这个限制。**

# **命名参数**

**JavaScript 函数可以将对象文字作为参数，并在参数签名中使用析构赋值，以实现命名参数的等效。请注意，您也可以使用默认参数功能为参数分配默认值:**

```
**const createUser = ({
  name = 'Anonymous',
  avatarThumbnail = '/avatars/anonymous.png'
}) => ({
  name,
  avatarThumbnail
});const george = createUser({
  name: 'George',
  avatarThumbnail: 'avatars/shades-emoji.png'
});george;
/*
{
  name: 'George',
  avatarThumbnail: 'avatars/shades-emoji.png'
}
*/**
```

# **休息和传播**

**JavaScript 中函数的一个常见特性是能够使用 rest 操作符收集函数签名中的一组剩余参数:`...`**

**例如，以下函数只丢弃第一个参数，并将其余参数作为数组返回:**

```
**const aTail = (head, ...tail) => tail;
aTail(1, 2, 3); // [2, 3]**
```

**Rest 将单个元素聚集到一个数组中。Spread 的作用正好相反:它将数组中的元素分散到各个元素中。考虑一下这个:**

```
**const shiftToLast = (head, ...tail) => [...tail, head];
shiftToLast(1, 2, 3); // [2, 3, 1]**
```

**JavaScript 中的数组有一个迭代器，使用 spread 操作符时会调用这个迭代器。对于数组中的每一项，迭代器都会传递一个值。在表达式`[...tail, head]`中，迭代器将每个元素按顺序从`tail`数组复制到由周围的文字符号创建的新数组中。因为 head 已经是一个单独的元素了，我们只要把它放到数组的末尾就完成了。**

# **Currying**

**curried 函数是一个一次接受多个参数的函数:它接受一个参数，然后返回一个接受下一个参数的函数，依此类推，直到提供了所有参数，此时，应用程序完成，并返回最终值。**

**可以通过返回另一个函数来启用 Curry 和部分应用程序:**

```
**const highpass = cutoff => n => n >= cutoff;
const gt4 = highpass(4); // highpass() returns a new function**
```

**你不必使用箭头函数。JavaScript 还有一个`function`关键字。我们使用箭头函数是因为`function`关键字需要更多的输入。这相当于上面的`highPass()`定义:**

```
**const highpass = function highpass(cutoff) {
  return function (n) {
    return n >= cutoff;
  };
};**
```

**JavaScript 中的箭头大致意思是“函数”。根据您使用的函数类型，函数行为有一些重要的差异(`=>`没有自己的`this`，不能用作构造函数)，但是我们将在后面讨论这些差异。现在，当你看到`x => x`时，想想“一个接受`x`并返回`x`的函数”。所以你可以把`const highpass = cutoff => n => n >= cutoff;`读作:**

**`“highpass`是一个取`cutoff`并返回一个取`n`并返回`n >= cutoff`结果的函数。**

**由于`highpass()`返回一个函数，您可以用它来创建一个更专门化的函数:**

```
**const gt4 = highpass(4);gt4(6); // true
gt4(3); // false**
```

**Autocurry 让你自动搜索功能，以获得最大的灵活性。假设你有一个函数`add3()`:**

```
**const add3 = curry((a, b, c) => a + b + c);**
```

**有了 autocurry，您可以以几种不同的方式使用它，它将根据您传入的参数数量返回正确的结果:**

```
**add3(1, 2, 3); // 6
add3(1, 2)(3); // 6
add3(1)(2, 3); // 6
add3(1)(2)(3); // 6**
```

**抱歉，Haskell 的粉丝们，JavaScript 缺少内置的 autocurry 机制，但是你可以从 Lodash 导入一个:**

```
**$ npm install --save lodash**
```

**然后，在您的模块中:**

```
**import curry from 'lodash/curry';**
```

**或者，你可以使用下面的魔法:**

```
**// Tiny, recursive autocurry
const curry = (
  f, arr = []
) => (...args) => (
  a => a.length === f.length ?
    f(...a) :
    curry(f, a)
)([...arr, ...args]);**
```

# **功能组成**

**你当然可以构造函数。函数组合是将一个函数的返回值作为参数传递给另一个函数的过程。在数学符号中:**

```
**f . g**
```

**用 JavaScript 翻译过来就是:**

```
**f(g(x))**
```

**它由内而外进行评估:**

1.  **`x`被评估**
2.  **`g()`适用于`x`**
3.  **`f()`应用于`g(x)`的返回值**

**例如:**

```
**const inc = n => n + 1;
inc(double(2)); // 5**
```

**值`2`被传入`double()`，产生`4`。`4`被传入`inc()`，后者评估为`5`。**

**您可以将任何表达式作为参数传递给函数。在应用函数之前，将对表达式求值:**

```
**inc(double(2) * double(2)); // 17**
```

**由于`double(2)`评估为`4`，您可以将其理解为`inc(4 * 4)`评估为`inc(16)`，然后`inc(16)`评估为`17`。**

**函数组合是函数式编程的核心。稍后我们会有更多的内容。**

# **数组**

**数组有一些内置的方法。方法是与对象关联的函数；通常是关联对象的属性；**

```
**const arr = [1, 2, 3];
arr.map(double); // [2, 4, 6]**
```

**在这种情况下，`arr`是对象，`.map()`是对象的属性，具有用于值的函数。当您调用它时，函数被应用到实参，以及一个名为`this`的特殊参数，该参数在方法被调用时自动设置。`this`值是`.map()`访问数组内容的方式。**

**注意，我们将`double`函数作为一个值传递给`map`，而不是调用它。这是因为`map`将一个函数作为参数，并将其应用于数组中的每一项。它返回一个包含由`double()`返回的值的新数组。**

**注意原来的`arr`值不变:**

```
**arr; // [1, 2, 3]**
```

# **方法链接**

**你也可以链接方法调用。方法链接是对函数的返回值直接调用方法的过程，而不需要通过名称引用返回值:**

```
**const arr = [1, 2, 3];
arr.map(double).map(double); // [4, 8, 12]**
```

**一个**谓词**是一个返回布尔值(`true`或`false`)的函数。`.filter()`方法接受一个谓词并返回一个新列表，只选择通过谓词的项目(返回`true`)包含在新列表中:**

```
**[2, 4, 6].filter(gt4); // [4, 6]**
```

**通常，您会想要从列表中选择项目，然后将这些项目映射到新列表:**

```
**[2, 4, 6].filter(gt4).map(double); [8, 12]**
```

**注意:在本文的后面，您将看到一种更有效的方法，使用一种叫做*转换器*的东西来同时选择和映射，但是首先还有其他的东西要探索。**

# **结论**

**如果你现在头晕，不要担心。我们仅仅触及了许多值得更多探索和思考的事物的表面。我们很快会回头更深入地探讨这些话题。**

> **[*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0) *|* [*下一篇>*](/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99)**

# **在 EricElliottJS.com[了解更多信息](http://ericelliottjs.com/)**

**EricElliottJS.com 会员可以参加互动代码挑战视频课程。如果你还不是会员，今天就注册吧。**

*****Eric Elliott*** *是一位分布式系统专家，著有以下书籍:* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](https://ericelliottjs.com/product/programming-javascript-applications-ebook/) *。作为*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发人员远程工作和拥抱工作/生活平衡所需的技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、* ***、Zumba Fitness、*** ***、华尔街日报、*******【ESPN、*******BBC、*** *以及包括* ***亚瑟、弗兰克·奥申、金属乐队在内的顶级录音艺术家提供软件体验********

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**