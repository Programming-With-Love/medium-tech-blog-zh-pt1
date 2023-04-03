# 你的 JavaScript

> 原文：<https://medium.com/compendium/ramda-your-javascript-e72bfaef01d5?source=collection_archive---------0----------------------->

![](img/b5596a6cdd256468524630773d2aef17.png)

如今，在开发人员的世界里，总会有一些令人兴奋的东西等待你去发现:新的框架、库、代码风格、鼓舞人心的设计、方法论等等。最近几个月，我对 JavaScript 中的*函数式编程*最感兴趣。

## 介绍

函数式编程范式对我来说是全新的，但它让我不再像传统那样思考编写代码。我相信以一种*功能*的方式编写【和思考】会让你的代码更具可读性；你最终会在团队中的开发人员之间有一个更加一致的语言。稍后调试和重构您的代码会更容易。你也可能会写更少的代码，做出更多有用的东西。做函数式编程是不一样的，但是当你习惯了之后，也会有很多乐趣。

在这篇文章中，我想向你展示 RamdaJs 可以做的一些很酷的事情——为了简单和懒惰，我现在称之为 *Ramda* 。首先，值得一提的是，Ramda 是一个 JavaScript 库，可以帮助你编写功能性的、简洁的、可重用的代码。Ramda 是声明性的(而不是命令性的，这是目前最常见的方法)。因此，为了更好地理解为什么 Ramda 是有用的，我们应该有一个函数式编程原则的基本知识，然后是一些关于 Ramda 的例子。

**我们将进入以下主题:**

*   Currying
*   纯函数
*   作文
*   不变
*   示例:注释列表

# Currying

Currying 是一种技术，在这种技术中，我们可以“考虑”将一个接受许多参数的函数转换为一个接受较少参数的更具体的函数。在 currying 的帮助下，我们可以将一个大的嵌套函数拆分成更小的函数。然后我们去除复杂性，使一个函数简洁且可重用。

## 简化的功能

curried 函数在收到所有需要的参数之前不会做任何事情。它将一次处理一个参数*。*

假设我们有一个带三个参数的函数，这个函数的一个简化版本将带一个参数并返回一个带下一个参数的函数，然后返回一个带第三个参数的函数。

一个有三个参数的函数，将每个函数的数目相加，可以写成:

```
// add = a => b => c => Number
const add = a => b => c => a + b + c;
```

为了让这个函数充分发挥作用，我们需要应用它的所有参数。我们可以用几种方法做到这一点，下面所有的例子都是允许的:

```
add(1, 2, 3) // 6
add(4)(1, 10) // 15
add(2, 3)(2) // 7
add(3)(2)(1) // 6add(2)(3) // partially applied
```

Ramda 之所以伟大，是因为它们的函数是自动生成的，这允许我们快速构建一系列简单明了的小函数或我们已经创建的旧函数。

# 纯函数

一个*纯函数*是函数式编程中的一个基本概念。所谓的*纯粹的*，我们的意思是一个函数应该没有所谓的“副作用”

纯函数的一些基本规则是:

*   它不会给未完成的变量赋值，即在自己的作用域之外，在 **{** 和 **}** 之间。
*   它不读写数据库，也不执行 API 调用。
*   它不会改变传入的参数。(这是一个严格的规则，在我们的函数中并不总是能够避免使用副作用。但是在大多数情况下，所有的函数都应该尽可能的干净。)

有时，您甚至可能希望将函数嵌套在使用父函数作用域中的变量的其他函数中。在这种情况下，您应该考虑将函数拆分成更小的函数，而不是通过它们传递值。

# 作文

*组合*是指我们采用一个*返回*函数，并将其作为参数传递给另一个函数。看看下面的代码:

```
const flip = x => x.split('').reverse().join('')
const exclaim = x => `${x}!`**const flipAndShout = x => exclaim(flip(x))**flipAndShout('dlroW olleH') // "Hello, World!"
```

这里我们调用两个函数，它们都对值 *x* 做了一些事情。这段代码很难读懂。如果我们给 **flipAndShout** 变量添加更多的函数，那就更有挑战性了。

下一个例子是一个合成函数:

```
const compose = (f, g) => {
  return x => f(g(x))
}
```

参数`f`和`g`是函数，`x`是贯穿它们的值。在这里，函数`g`将首先运行，然后是函数`f`，这将使调用从右向左流动。为了了解如何实现这一点，我们可以重构前一个示例中的代码，并使用我们的`compose`函数:

```
const flip = x => x.split('').reverse().join('')
const exclaim = x => `${x}!`**const flipAndShout = compose(exclaim, flip)**flipAndShout('dlroW olleH') // "Hello, World!"
```

这比一堆函数调用更容易阅读。我们只需快速浏览一下，就能看到 **flipAndShout** 函数将返回什么。稍后我们将看一些 Ramda 的复合函数的例子。

# 不变

不变性和不可变数据的概念在函数式编程中也很重要。

不变性意味着现有数据不应改变。我们用某个值(字符串，数组，数字)初始化一个变量；它不应该*改变*数据类型(或被转换为),也不应该被覆盖——甚至是对象属性或数组中的元素。如果现有数据被更改，我们可能会在调试时遇到一些大问题，如果有人在某个时候需要重构代码，代码可能不那么可读。

# 我们现在可以跳 Ramda 了吗…？

没错。

Ramda 是一个优秀的库，可以帮助你开始用 JavaScript 进行功能性思考。Ramda 提供了一个很棒的工具箱，里面有很多有用的功能和体面的文档。如果你想在我们进行的过程中尝试它的功能，可以看看拉姆达的 [REPL](https://ramdajs.com/repl/) 。

## 装置

Ramda 可以通过几种方式安装，通过 Yarn 或者 NPM(但是他们也在他们的网站上提供一些 CDN ):

```
yarn add ramda// or npm install ramda
```

## 使用

`require`或`import`它进入你的项目:

```
const R = require('ramda')// orimport R from 'ramda'
```

或者，如果更适合您，您可以导入每个函数。然后，您不需要为每个使用的函数预先添加`R.`:

```
import { map, filter, prop } from 'ramda'
```

如果你使用的是 [REPL](https://ramdajs.com/repl/) ，没有必要导入任何东西。您可以编写函数(或者直接从这里复制/粘贴),就像它们已经现成可用一样。

# 示例:注释列表

总有一天，所有开发人员都会创建一个笔记列表。我们将笔记添加到我们的*列表*，当我们完成一个笔记时，我们将它存档。

我们需要一个数据集，既包含常规笔记，又包含这样一个笔记列表的提醒。在我们的例子中，我们将使用下面的 notesList 数组。具有有效(非空)“到期日期”属性的对象是提醒，而没有的对象是常规注释:

```
const notesList = [
  {
    title: "Buy milk and bread",
    createdAt: '2020-04-04',
    dueDate: null,
    archived: false,
  },
  {
    title: "Pick up a package at the post office",
    createdAt: '2020-04-04',
    dueDate: null,
    archived: false,
  },
  {
    title: "Take a walk with Yoda",
    createdAt: '2020-04-04',
    dueDate: null,
    archived: true,
  },
  {
    title: "Read 15 minutes",
    createdAt: '2020-03-07',
    dueDate: '2020-03-08',
    archived: false,
  },
  {
    title: "Do 30 minutes workout",
    createdAt: '2020-03-01',
    dueDate: '2020-03-04',
    archived: false,
  },
]
```

## 过滤

对这个列表的一个典型操作是过滤掉我们已经完成的内容(在这个例子中是“存档”)。通过使用内置数组方法，我们可以实现我们想要的:

```
const filterNotes = notesList.filter(note => !note.archived);
```

借助 Ramda，我们可以做到以下几点:

```
const filterNotes = R.filter(R.propEq('archived', false));
```

嗯，这里发生了什么？我们没有向使用 Ramda 语法的`filterNotes`函数传递任何参数。这是因为该功能属于*自由点类型。*

*Pointfree-style 也是一种函数式编程风格，其中函数定义(函数体)不引用函数的参数。我们可以通过调用返回函数的函数，比如 curried 函数，得到一个无点样式。让我们提醒自己这个帖子前面的例子:*

```
// add = a => b => c => Number
const add = a => b => c => a + b + c;
```

因此，我们不需要为函数定义分配参数。或者换句话说，我们不需要“告诉”Ramda 函数它需要什么数据。

我们仍然需要调用带有参数的`filterNotes`函数来使其工作，参数必须是对象列表，并且对象必须至少包含一个`archived`属性。

## 按属性排序

现在我们已经过滤掉了所有不是`archived`的音符。现在让我们按照创建日期对笔记进行分类。为此，我们可以使用 Ramda 的`sortBy`和`prop`函数:

```
const filterNotes = filter(propEq('archived', false));**const sortByCreatedAt = sortBy(prop(‘createdAt’));
const filteredNotes = filterNotes(notesList);****sortByCreated(filteredNotes);**
```

## 功能组成

上面的代码可读性不是很好，但是我们怎样才能让它变得更好呢？前面我们讲了作文。通过函数组合，我们可以构建其他小函数的函数管道。Ramda 提供了几个函数来进行函数组合，这些函数被称为`pipe`和`compose`。

这两个函数都接受一个或多个参数并返回一个函数。`pipe`※*由左向右*构图。它接受第一个参数，将结果传递给下一个参数，并像这样继续下去，直到最后一个参数。`compose`略有不同，从右向左应用参数*。*

你用哪一个都没关系，但是要选择最适合你的。我更喜欢在例子中使用`compose`，因为这是我的眼睛处理得最好的。

我们可以在 ***过滤器上使用`compose`注释*** 和***sortByCreatedAt***:

```
const filterNotes = filter(propEq('archived', false));**const sortByCreatedAt = sortBy(prop('createdAt'));**
**const sortNotes = compose(sortByCreatedAt, filterNotes);**sortNotes(notesList);
```

如果我们想对笔记进行降序排序，同时重用我们已经编写的代码。我们如何实现这一目标？Ramda 的`reverse`函数在这里就派上用场了。`reverse`获取一个列表并以相反的顺序返回它:

```
const filterNotes = filter(propEq('archived', false));**const sortByCreatedAt = sortBy(prop('createdAt'));**
**const sortByCreatedAtDescending = compose(reverse, sortByCreated);**const sortNotes = compose(**sortByCreatedAtDescending**, filteredNotes);sortNotes(notesList);
```

## 按类别分组

Ramda 有一个名为 *groupBy* 的函数，它将一个列表分割成子列表，然后将结果存储在一个对象中。

对于我们的示例，我们可以传递一个函数，该函数为每个对象中的“dueDate”属性创建一个条件语句，然后返回一个笔记应该分组到哪个“key”中。

我们的函数可以这样写:

```
const groupByType = groupBy(n => n.duedate ? 'reminders' : 'notes')
```

并用剩下的代码实现它:

```
const filterNotes = filter(propEq('archived', false));
const sortByCreatedAt = sortBy(prop('createdAt'));**const groupByType = groupBy(n => n.dueDate ? 'reminders' : 'notes')****const filterByType = compose(groupByType, sortByCreatedAt);**
const sortedNotes = compose(
      **filterByType**,
      filterNotes
    );sortedNotes(notesList)
```

我们的代码看起来很棒。现在我们将所有的笔记和提醒分组(每种类型作为对象键)，我们得到这样的结果:

```
{
    notes: [
        {
            archived: false,
            createdAt: "2020-04-04",
            dueDate: null,
            title: "Buy milk and bread"
        },
        {
            archived: false,
            createdAt: "2020-04-04",
            dueDate: null,
            title: "Pick up a package at the post office"
        }
    ],
    reminders: [
        {
            archived: false,
            createdAt: "2020-03-01",
            dueDate: "2020-03-04",
            title: "Do 30 minutes workout"
        },
        {
            archived: false,
            createdAt: "2020-03-07",
            dueDate: "2020-03-08",
            title: "Read 15 minutes"
        }
    ]
}
```

[查看拉姆达 REPL 的结果](https://ramdajs.com/repl/?v=0.27.0#?const%20notesList%20%3D%20%5B%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Buy%20milk%20and%20bread%22%2C%0A%20%20%20%20createdAt%3A%20%272020-04-04%27%2C%0A%20%20%20%20dueDate%3A%20null%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Pick%20up%20package%20at%20mail%20office%22%2C%0A%20%20%20%20createdAt%3A%20%272020-04-04%27%2C%0A%20%20%20%20dueDate%3A%20null%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Take%20a%20walk%20with%20Yoda%22%2C%0A%20%20%20%20createdAt%3A%20%272020-04-04%27%2C%0A%20%20%20%20dueDate%3A%20null%2C%0A%20%20%20%20archived%3A%20true%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Read%2015%20minutes%22%2C%0A%20%20%20%20createdAt%3A%20%272020-03-07%27%2C%0A%20%20%20%20dueDate%3A%20%272020-03-08%27%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Do%2030%20minutes%20workout%22%2C%0A%20%20%20%20createdAt%3A%20%272020-03-01%27%2C%0A%20%20%20%20dueDate%3A%20%272020-03-04%27%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%5D%0A%0Aconst%20filterNotes%20%3D%20filter%28whereEq%28%7B%20archived%3A%20false%20%7D%29%29%3B%0Aconst%20sortByCreatedAt%20%3D%20sortBy%28prop%28%27createdAt%27%29%29%3B%0Aconst%20groupByType%20%3D%20groupBy%28n%20%3D%3E%20n.dueDate%20%3F%20%27reminders%27%20%3A%20%27notes%27%29%0Aconst%20filterByType%20%3D%20compose%28groupByType%2C%20sortByCreatedAt%29%3B%0Aconst%20sortedNotes%20%3D%20compose%28%0A%20%20%20%20%20%20filterByType%2C%0A%20%20%20%20%20%20filterNotes%0A%20%20%20%20%29%3B%0AsortedNotes%28notesList%29)

## 收尾工作

现在，我们的`notesList`中的一些对象属性可能并不总是必需的。

我们可以有一个用户界面，其中我们只需要一些属性来直观地显示它们(其余的不用)。这些性质我们可以认为是*重要的*信息。`[project](https://ramdajs.com/docs/#project)`是一个类似于 SQL `select`的 Ramda 函数，可以从每个对象中选择我们想要的(重要)属性(它也是 Ramda `pick`和`map`的组合):

```
const importantFields = project(['title', 'createdAt'**,** 'dueDate']);
```

让我们用剩下的代码来实现它:

```
const filterNotes = filter(propEq('archived', false));
const sortByCreatedAt = sortBy(prop('createdAt'));
const sortByCreatedAtDescending = compose(reverse, sortByCreatedAt);
const groupByType = groupBy(n => n.dueDate ? 'reminders' : 'notes')**const importantFields = project(['title', 'createdAt', 'dueDate']);**const filterByType = compose(groupByType, sortByCreatedAt)
const sortedNotes = compose(
      **importantFields**,
      filterByType,
      filterNotes
    )
sortedNotes(notesList)
```

[在 REPL 看最终结果](https://ramdajs.com/repl/?v=0.27.0#?const%20notesList%20%3D%20%5B%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Buy%20milk%20and%20bread%22%2C%0A%20%20%20%20createdAt%3A%20%272020-04-04%27%2C%0A%20%20%20%20dueDate%3A%20null%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Pick%20up%20package%20at%20mail%20office%22%2C%0A%20%20%20%20createdAt%3A%20%272020-04-04%27%2C%0A%20%20%20%20dueDate%3A%20null%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Take%20a%20walk%20with%20Yoda%22%2C%0A%20%20%20%20createdAt%3A%20%272020-04-04%27%2C%0A%20%20%20%20dueDate%3A%20null%2C%0A%20%20%20%20archived%3A%20true%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Read%2015%20minutes%22%2C%0A%20%20%20%20createdAt%3A%20%272020-03-07%27%2C%0A%20%20%20%20dueDate%3A%20%272020-03-08%27%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%20%20%7B%0A%20%20%20%20title%3A%20%22Do%2030%20minutes%20workout%22%2C%0A%20%20%20%20createdAt%3A%20%272020-03-01%27%2C%0A%20%20%20%20dueDate%3A%20%272020-03-04%27%2C%0A%20%20%20%20archived%3A%20false%2C%0A%20%20%7D%2C%0A%5D%0A%0Aconst%20filterNotes%20%3D%20filter%28R.whereEq%28%7B%20archived%3A%20false%20%7D%29%29%3B%0Aconst%20sortByCreatedAt%20%3D%20sortBy%28prop%28%27createdAt%27%29%29%3B%0Aconst%20sortByCreatedAtDescending%20%3D%20compose%28reverse%2C%20sortByCreatedAt%29%3B%0Aconst%20groupByType%20%3D%20groupBy%28n%20%3D%3E%20n.dueDate%20%3F%20%27reminders%27%20%3A%20%27notes%27%29%0Aconst%20importantFields%20%3D%20project%28%5B%27title%27%2C%20%27createdAt%27%2C%20%27dueDate%27%5D%29%3B%0Aconst%20filterByType%20%3D%20compose%28groupByType%2C%20sortByCreatedAt%29%0Aconst%20sortedNotes%20%3D%20compose%28%0A%20%20%20%20%20%20map%28importantFields%29%2C%0A%20%20%20%20%20%20filterByType%2C%0A%20%20%20%20%20%20filterNotes%0A%20%20%20%20%29%0AsortedNotes%28notesList%29)

现在我们有了一个包含两个数组的对象，按创建日期和活动状态排序；多棒啊，不是吗？作为我们的最终结果，看看它有多干净，我们得到了我们需要的数据:

```
{
    notes: [
        {
            createdAt: "2020-04-04",
            dueDate: null,
            title: "Buy milk and bread"
        },
        {
            createdAt: "2020-04-04",
            dueDate: null,
            title: "Pick up package at mail office"
        }
    ],
    reminders: [
        {
            createdAt: "2020-03-01",
            dueDate: "2020-03-04",
            title: "Do 30 minutes workout"
        },
        {
            createdAt: "2020-03-07",
            dueDate: "2020-03-08",
            title: "Read 15 minutes"
        }
    ]
}
```

感谢阅读！

*代号 foh shizzle*