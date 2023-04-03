# JavaScript 风格的元素

> 原文：<https://medium.com/javascript-scene/elements-of-javascript-style-caa8821cb99f?source=collection_archive---------1----------------------->

![](img/99895210bc15a0d4e4dd59b2a6dce9b5.png)

Out of the Blue — Iñaki Bolumburu (CC BY-NC-ND 2.0)

> **注:**本帖现为[《作曲软件》一书](https://leanpub.com/composingsoftware)的一部分。

1920 年，[《风格要素》作者小威廉·斯特伦克](https://www.amazon.com/Elements-Style-Fourth-William-Strunk/dp/020530902X/ref=as_li_ss_tl?ie=UTF8&qid=1493260884&sr=8-1&keywords=the+elements+of+style&linkCode=ll1&tag=eejs-20&linkId=f7eb0eacba0eab243899626551113119)。它为经受住时间考验的英语语言风格制定了指导方针。您可以通过将类似的标准应用于您的代码风格来改进您的代码。

以下是指导方针，不是一成不变的法律。如果这样做能澄清准则的含义，可能有正当的理由偏离它们，但是[要保持警惕和自知](/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75)。这些指导方针经受住了时间的考验，理由很充分:它们通常是正确的。只有在有充分理由的情况下才背离它们——而不仅仅是一时兴起或个人风格偏好。

几乎所有来自**组合的基本原则**的指导方针都适用于源代码:

*   以段落为写作单位:每个主题一段。
*   省略不必要的词语。
*   使用主动语态。
*   避免一连串松散的句子。
*   将相关单词放在一起。
*   用肯定的形式陈述。
*   在平行概念上使用平行结构。

我们可以将几乎相同的概念应用于代码风格:

1.  使功能成为组成的单位。每个功能一份工作。
2.  省略不必要的代码。
3.  使用主动语态。
4.  避免一连串松散的声明。
5.  将相关代码放在一起。
6.  把陈述和表达用积极的形式。
7.  对并行概念使用并行代码。

# 1.使功能成为组成的单位。每个功能一份工作。

> 软件开发的本质是组合。我们通过将模块、功能和数据结构组合在一起来构建软件。
> 
> 理解如何编写和组合函数是软件开发人员的一项基本技能。

模块只是一个或多个函数或数据结构的集合，数据结构是我们表示程序状态的方式，但是在我们应用函数之前，不会发生任何有趣的事情。

在 JavaScript 中，有三种函数:

*   通信功能:执行输入输出的功能。
*   程序功能:组合在一起的指令列表。
*   映射函数:给定一些输入，返回一些相应的输出。

虽然所有有用的程序都需要 I/O，并且许多程序遵循一些过程序列，但是大多数函数应该是映射函数:给定一些输入，函数将返回一些相应的输出。

**每个函数一件事**:如果你的函数是为了 I/O，不要把那个 I/O 和映射(计算)混在一起。如果你的函数是为了映射，不要把它和 I/O 混在一起，根据定义，过程函数违反了这个准则。过程函数也违反了另一条准则:避免一连串松散的语句。

理想函数是一个简单、确定的纯函数:

*   给定相同的输入，总是返回相同的输出
*   没有副作用

又见，[“什么是纯函数？”](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)

# 2.省略不必要的代码。

> “苍劲有力的文字是简洁的。一个句子不应该包含不必要的单词，一个段落不应该包含不必要的句子，就像一幅图画不应该有不必要的线条，一台机器不应该有不必要的部件一样。这并不要求作者把所有的句子都写得很短，或者避开所有的细节，只略述主题，而是要求每一个字都有意义。”【多余的话省略。]
> ~小威廉·斯特伦克《风格的要素》

简洁的代码在软件中是至关重要的，因为更多的代码会产生更多的漏洞。更少的代码=更少的 bug 藏身之处=更少的 bug。

简洁的代码更易读，因为它有更高的信噪比:读者必须筛选更少的语法噪音才能理解意思。*更少的代码=更少的语法噪音=更强的意义传递信号。*

借用风格元素的一句话:简洁的代码更有*活力。*

```
function secret (message) {
  return function () {
    return message;
  }
};
```

可以简化为:

```
const secret = msg => () => msg;
```

对于那些熟悉简洁箭头函数(2015 年在 ES6 中引入)的人来说，这更具可读性。它省略了不必要的语法:大括号、`function`关键字和`return`语句。

第一个包括不必要的语法。大括号、`function`关键字和`return`语句对于那些熟悉简洁箭头语法的人来说毫无用处。它的存在只是为了让那些还不熟悉 ES6 的人熟悉代码。

ES6 从 2015 年开始成为语言标准。是时候[熟悉一下](/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75)了。

## 省略不必要的变量

有时候，我们很想给那些实际上不需要命名的东西命名。问题是[人脑在工作记忆](http://www.nature.com/neuro/journal/v17/n3/fig_tab/nn.3655_F2.html)中可用的资源数量有限，每个变量必须以离散量子的形式存储，占据可用工作记忆槽中的一个。

由于这个原因，有经验的开发人员学会消除不需要存在的变量。

例如，在大多数情况下，您应该省略仅为命名返回值而创建的变量。你的函数名应该提供足够的关于函数将返回什么的信息。请考虑以下情况:

```
const getFullName = ({firstName, lastName}) => {
  const fullName = firstName + ' ' + lastName;
  return fullName;
};
```

vs…

```
const getFullName = ({firstName, lastName}) => (
  firstName + ' ' + lastName
);
```

开发人员减少变量的另一个常用方法是利用函数组合和无点风格。

**无点风格**是一种定义函数的方式，无需引用函数操作的参数。实现无点风格的常见方法包括涂抹和函数组合。

让我们看一个使用咖喱的例子:

```
const add2 = a => b => a + b;// Now we can define a point-free inc()
// that adds 1 to any number.
const inc = add2(1);inc(3); // 4
```

看一下`inc()`函数的定义。注意，它没有使用`function`关键字或`=>`语法。没有地方列出参数，因为函数内部不使用参数列表。相反，它返回一个知道如何处理参数的函数。

让我们看另一个使用函数组合的例子。**函数合成**是将一个函数应用于另一个函数应用的结果的过程。不管你是否意识到，你一直在使用函数组合。例如，每当你链接像`.map()`或`promise.then()`这样的方法时，你都会用到它。在它最基本的形式中，看起来是这样的:`f(g(x))`。在代数中，这种组合通常写成`f ∘ g`(通常发音为，“f *在* g 之后”或“f *与* g 组合”)。

当您将两个函数组合在一起时，就不需要创建一个变量来保存两个函数应用程序之间的中间值。让我们看看函数组合如何清理一些代码:

```
const g = n => n + 1;
const f = n => n * 2;// With points:
const incThenDoublePoints = n => {
  const incremented = g(n);
  return f(incremented);
};incThenDoublePoints(20); // 42// compose2 - Take two functions and return their composition
const compose2 = (f, g) => x => f(g(x));// Point-free:
const incThenDoublePointFree = compose2(f, g);incThenDoublePointFree(20); // 42
```

你可以用任何函子做同样的事情。 [**仿函数**](/javascript-scene/functors-categories-61e031bac53f) 是任何可以映射的东西，例如数组(`Array.map()`)或承诺(`promise.then()`)。让我们使用映射链编写另一个版本的`compose2`函数组合:

```
const compose2 = (f, g) => x => [x].map(g).map(f).pop();const incThenDoublePointFree = compose2(f, g);incThenDoublePointFree(20); // 42
```

每次使用承诺链，你都在做同样的事情。

几乎每个函数式编程库都至少有两个版本的 compose 实用程序:`compose()`从右向左应用函数，而`pipe()`从左向右应用函数。

洛达什称它们为`compose()`和`flow()`。当我从 Lodash 使用它们时，我总是这样导入它:

```
import pipe from 'lodash/fp/flow';
pipe(g, f)(20); // 42
```

然而，这并没有太多的代码，它做同样的事情:

```
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);
pipe(g, f)(20); // 42
```

如果这个函数组合的东西对你来说听起来很陌生，并且你不确定你将如何使用它，仔细考虑一下:

> 软件开发的本质是组合。我们通过组合更小的模块、函数和数据结构来构建应用程序。

由此你可以得出结论，理解功能和对象组成的工具就像一个房屋建筑者理解钻头和钉枪一样重要。

当你用命令式代码把函数和中间变量拼凑在一起时，就像用胶带和胶水把这些碎片粘在一起一样。

请记住:

*   如果你能用更少的代码表达同样的意思，而不改变或混淆意思，你应该这样做。
*   如果你能用更少的变量表达同样的意思，而不改变或混淆意思，你应该这样做。

# 3.使用主动语态

> "主动语态通常比被动语态更直接有力."~小威廉·斯特伦克《风格的要素》

尽可能直接地给事物命名。

*   `myFunction.wasCalled()`比`myFunction.hasBeenCalled()`好
*   `createUser()`比`User.create()`好
*   `notify()`比`Notifier.doNotification()`好

将谓词和布尔命名为是或否问题:

*   `isActive(user)`比`getActiveStatus(user)`好
*   `isFirstRun = false;`比`firstRun = false;`好

使用动词形式命名函数:

*   `increment()`比`plusOne()`好
*   `unzip()`比`filesFromZip()`好
*   `filter(fn, array)`比`matchingItemsFromArray(fn, array)`好

**事件处理程序**

事件处理程序和生命周期方法是动词规则的例外，因为它们被用作限定符；他们不表达做什么，而是表达什么时候做。它们应该被命名为“何时行动”、“动词”。

*   `element.onClick(handleClick)`比`element.click(handleClick)`好
*   `component.onDragStart(handleDragStart)`比`component.startDrag(handleDragStart)`好

在第二种形式中，看起来我们试图触发事件，而不是对其做出反应。

## 生命周期方法

考虑以下组件假设生命周期方法的替代方法，该方法用于在组件更新之前调用处理函数:

*   `componentWillBeUpdated(doSomething)`
*   `componentWillUpdate(doSomething)`
*   `beforeUpdate(doSomething)`

在第一个例子中，我们使用被动语态(将更新而不是将更新)。这是一个拗口的问题，并不比其他选择更清楚。

第二个例子要好得多，但是这个生命周期方法的要点是调用一个处理程序。`componentWillUpdate(handler)`读起来好像会更新处理程序，但这不是我们的意思。我们的意思是，“在组件更新之前，调用处理程序”。`beforeComponentUpdate()`更清楚地表达意图。

我们可以进一步简化。因为这些是方法，所以主体(组件)是内置的。在方法名中引用它是多余的。想想如果你直接调用这些方法会是什么样的结果:`component.componentWillUpdate()`。这就像说，“吉米吉米晚饭吃牛排。”你不需要听到两次主题的名字。

*   `component.beforeUpdate(doSomething)`比`component.beforeComponentUpdate(doSomething)`好

**函数混合**是向对象添加属性和方法的函数。这些功能在流水线中一个接一个地应用，就像装配线一样。每个函数 mixin 都将`instance`作为输入，并在将它传递给管道中的下一个函数之前添加一些东西。

我喜欢用形容词来命名功能混合。你可以经常使用“ing”或“able”后缀来找到有用的形容词。示例:

*   `const duck = composeMixins(flying, quacking);`
*   `const box = composeMixins(iterable, mappable);`

# 4.避免一连串松散的声明

> “……一个系列很快就会变得单调乏味。”
> ~小威廉·斯特伦克《风格的要素》

开发人员经常将过程中的事件序列串在一起:一组松散相关的语句，设计为一个接一个地运行。过多的过程是意大利面条式代码的配方。

这样的序列经常被许多平行的形式重复，每一个都微妙地，有时是出乎意料地不同。例如，一个用户界面组件与几乎所有其他用户界面组件共享相同的核心需求。它的关注点可以分解成生命周期的各个阶段，并由不同的功能来管理。

考虑以下顺序:

```
const drawUserProfile = ({ userId }) => {
  const userData = loadUserData(userId);
  const dataToDisplay = calculateDisplayData(userData);
  renderProfileData(dataToDisplay);
};
```

这个函数实际上处理三件不同的事情:加载数据、根据加载的数据计算视图状态以及呈现。

在大多数现代前端应用架构中，这些问题都是单独考虑的。通过分离这些关注点，我们可以很容易地为每个关注点混合和匹配不同的功能。

例如，我们可以完全替换渲染器，而不会影响程序的其他部分，例如，React 丰富的自定义渲染器:用于原生 iOS 和 Android 应用程序的 ReactNative、用于 WebVR 的 AFrame、用于服务器端渲染的 ReactDOM/Server 等…

这个函数的另一个问题是，如果不先加载数据，就不能简单地计算要显示的数据并生成标记。如果你已经加载了数据呢？你最终做了在随后的通话中不需要做的工作。

分离关注点也使得它们可以独立测试。我喜欢在编写代码时对我的应用程序进行单元测试，并显示每次更改的测试结果。然而，如果我们将*渲染代码*绑定到*数据加载代码*，我不能简单地将一些假数据传递给渲染代码用于测试目的。我必须对整个组件进行端到端的测试——由于浏览器加载、异步网络 I/O 等原因，这个过程可能会非常耗时

我不会从我的单元测试中得到即时的反馈。分离功能允许你在彼此隔离的情况下测试单元。

这个例子已经有了独立的函数，我们可以将它们提供给应用程序中不同的生命周期挂钩。当应用程序安装组件时，可以触发加载。计算和呈现可以响应视图状态更新而发生。

结果是软件的职责更加清晰:每个组件可以重用相同的结构和生命周期挂钩，软件性能更好；我们不重复不需要在后续循环中重复的工作。

# 5.将相关代码放在一起。

许多框架和样板文件规定了一种程序组织方法，其中文件按技术类型分组。如果你正在构建一个小的计算器或待办事项应用程序，这是很好的，但对于较大的项目，通常最好是将文件按功能分组。

例如，下面是待办事项应用程序按类型和功能划分的两种备选文件层次结构:

**按类型分组:**

```
.
├── components
│   ├── todos
│   └── user
├── reducers
│   ├── todos
│   └── user
└── tests
    ├── todos
    └── user
```

**按功能分组:**

```
.
├── todos
│   ├── component
│   ├── reducer
│   └── test
└── user
    ├── component
    ├── reducer
    └── test
```

当您按功能将文件组合在一起时，您可以避免上下滚动文件列表来查找您需要编辑以使单个功能工作的所有文件。

> 按功能将相关文件放在一起。

# 6.把陈述和表达用积极的形式。

> “做出明确的断言。避免平淡、无色、犹豫、不明确的语言。使用*这个词而不是*作为否定或对立的手段，永远不要作为逃避的手段。”
> ~小威廉·斯特伦克《风格的要素》

*   `isFlying`比`isNotFlying`好
*   `late`比`notOnTime`好

## If 语句

```
if (err) return reject(err);// do something...
```

…优于:

```
if (!err) {
  // ... do something
} else {
  return reject(err);
}
```

## Ternaries

```
{
  [Symbol.iterator]: iterator ? iterator : defaultIterator
}
```

…优于:

```
{
  [Symbol.iterator]: (!iterator) ? defaultIterator : iterator
}
```

## 喜欢强烈的否定陈述

有时我们只关心一个丢失的变量，所以使用一个正的名字会迫使我们用`!`操作符否定它。在这种情况下，更喜欢强烈的否定形式。单词“not”和`!`操作符创建弱表达式。

*   `if (missingValue)`比`if (!hasValue)`好
*   `if (anonymous)`比`if (!user)`好
*   `if (isEmpty(thing))`比`if (notDefined(thing))`好

## 在函数调用中避免空的和未定义的参数

不要要求函数调用方传递`undefined`或`null`来代替可选参数。改为首选命名选项对象:

```
const createEvent = ({
  title = 'Untitled',
  timeStamp = Date.now(),
  description = ''
}) => ({ title, description, timeStamp });// later...
const birthdayParty = createEvent({
  title: 'Birthday Party',
  description: 'Best party ever!'
});
```

…优于:

```
const createEvent = (
  title = 'Untitled',
  timeStamp = Date.now(),
  description = ''
) => ({ title, description, timeStamp });// later...
const birthdayParty = createEvent(
  'Birthday Party',
  undefined, // This was avoidable
  'Best party ever!'  
);
```

# 对并行概念使用并行代码

> “……平行结构要求内容和功能相似的表达在表面上应该相似。形式的相似性使读者更容易识别内容和功能的相似性。”
> ~小威廉·斯特伦克《风格的要素》

应用中很少有以前没有解决的问题。我们最终会一遍又一遍地做同样的事情。当这种情况发生时，这是一个抽象的机会。识别相同的部分，并构建一个抽象，允许您只提供不同的部分。这正是库和框架为我们做的。

UI 组件就是一个很好的例子。不到十年前，使用 jQuery 将 UI 更新与应用程序逻辑和网络 I/O 混合在一起是很常见的。然后人们开始意识到我们可以在客户端将 MVC 应用于 web 应用程序，人们开始将模型与 UI 更新逻辑分开。

最终，web 应用程序采用了组件模型方法，这种方法让我们可以使用像 JSX 或 HTML 模板这样的东西来声明性地建模我们的组件。

我们最终得到的是一种对每个组件以相同方式表达 UI 更新逻辑的方式，而不是对每个组件使用不同的命令式代码。

对于那些熟悉组件的人来说，很容易看到每个组件是如何工作的:有一些表达 UI 元素的声明性标记，用于连接行为的事件处理程序，以及用于附加回调的生命周期挂钩，这些回调将在我们需要时运行。

当我们为类似的问题重复类似的模式时，任何熟悉该模式的人都应该能够很快了解代码做了什么。

# 结论:代码应该简单，而不是简单化

> 苍劲的文笔简洁。一个句子不应该包含不必要的单词，一个段落不应该包含不必要的句子，就像一幅图画不应该有不必要的线条，一台机器不应该有不必要的部件一样。这并不要求作者把所有的句子都写得很短，或者避开所有的细节，只略述主题，而是要求每一个字都有意义。【着重号另加。】
> ~小威廉·斯特伦克《风格的要素》

ES6 于 2015 年标准化，但在 2017 年，许多开发人员以编写更易于阅读的代码为幌子，避免使用简洁的箭头函数、隐式返回、rest 和 spread 运算符等功能，因为[更熟悉](/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75)。这是一个大错误。熟悉来自实践，熟悉之后，ES6 中的简洁特性明显优于 ES5 的替代方案:*与语法繁重的替代方案相比，简洁的代码很简单*。

代码应该简单，而不是简单化。

给出的简明代码是:

*   不易出错
*   更容易调试

鉴于这些错误:

*   修理起来非常昂贵
*   往往会导致更多的错误
*   中断正常功能开发的流程

而且鉴于简洁的代码也是:

*   更容易写
*   更容易阅读
*   更易于维护

值得进行培训投资，让开发人员快速使用诸如简洁语法、currying & composition 等技术。当我们因为熟悉而没有这样做的时候，我们会用高人一等的口气和我们代码的读者说话，这样他们就能更好地理解代码，就像一个成年人和一个蹒跚学步的孩子说婴儿话一样。

假设读者对实现一无所知，但不要假设读者很笨，或者读者不懂语言。

要清楚，但不要把它弄得模糊不清。把事情简单化既浪费又侮辱人。在实践和熟悉方面进行投资，以便获得更好的编程词汇和更有活力的风格。

> 代码应该简单，而不是简单化。

# 通过实时 1:1 辅导提升您的技能

DevAnywhere 是达到高级 JavaScript 技能的最快方法:

*   现场课程
*   弹性工时
*   一对一指导
*   构建真正的生产应用

[![](img/03504ae5b049cdb99861a7b575be3a08.png)](https://devanywhere.io/)

[https://devanywhere.io/](https://devanywhere.io/)

***埃里克·艾略特*** *是* [*【编程 JavaScript 应用】*](http://pjabook.com) *(O'Reilly)的作者，也是*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他和世界上最美丽的女人一起在任何他想去的地方工作。**