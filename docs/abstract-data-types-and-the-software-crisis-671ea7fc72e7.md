# 抽象数据类型和软件危机

> 原文：<https://medium.com/javascript-scene/abstract-data-types-and-the-software-crisis-671ea7fc72e7?source=collection_archive---------0----------------------->

## 抽象如何帮助我们管理软件复杂性

![](img/95bf053802e9e2c9c4d4d23d46684d9e.png)

Image: [MattysFlicks — Smoke Art — Cubes to Smoke](https://www.flickr.com/photos/68397968@N07/11432696204) ([CC BY 2.0](https://creativecommons.org/licenses/by/2.0/))

> **注:**这是《作曲软件》系列的一部分**s**[(现在一本书！)](https://leanpub.com/composingsoftware) 从基础开始学习 JavaScript 6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！[买书](https://leanpub.com/composingsoftware) | [索引](/javascript-scene/composing-software-the-book-f31c77fc3ddc)|[|<上一张](/javascript-scene/abstraction-composition-cb2849d5bdd6) | [下一张>](/javascript-scene/functors-categories-61e031bac53f)

# 抽象数据类型

> ***不要与*** 混淆
> 
> **代数数据类型**(有时缩写为 ADT 或 AlgDT)。代数数据类型是指编程语言(如 Rust、Haskell、F#)中显示特定代数结构的某些属性的复杂类型。例如，总和类型和产品类型。
> 
> **代数结构。代数结构是从抽象代数中研究和应用的，像 ADT 一样，抽象代数也通常根据公理的代数描述来指定，但是其应用远远超出了计算机和代码的世界。一个代数结构是不可能完全用软件建模的。相比之下，抽象数据类型作为规范和指南来正式验证工作软件。**

抽象数据类型(ADT)是由公理定义的抽象概念，这些公理表示一些数据和对这些数据的操作。ADT*不是*根据具体实例定义的，并且*不指定*具体的数据类型、结构或实现中使用的算法。相反，ADT 只根据它们的操作以及这些操作必须遵守的公理来定义数据类型。

# 常见 ADT 示例

*   目录
*   堆
*   长队
*   一组
*   地图
*   溪流

ADT 可以表示对任何类型数据的任何操作集。换句话说，所有可能的 ADT 的穷尽列表是无限的，原因与所有可能的英语句子的穷尽列表是无限的相同。ADT 是对未指定数据的一组操作的抽象概念，而不是一组特定的具体数据类型。一个常见的误解是，很多大学课程和数据结构教材中教授的 ADT 的具体例子就是 ADT。许多这样的教科书将数据结构标记为“ADT ”,然后跳过 ADT，用具体的术语描述数据结构，而没有向学生展示数据类型的实际抽象表示。糟糕！

ADT 可以表达许多有用的代数结构，包括半群、幺半群、函子、幺半群等。[幻想世界规范](https://github.com/fantasyland/fantasy-land)是由 ADTs 描述的代数结构的有用目录，以鼓励 JavaScript 中的互操作实现。库构建者可以使用提供的公理来验证他们的实现。

# 为什么是 ADTs？

抽象数据类型是有用的，因为它们为我们提供了一种以数学上合理、精确和明确的方式正式定义可重用模块的方法。这允许我们共享一种公共语言来引用有用的软件构建块的大量词汇:当我们在领域、框架甚至编程语言之间移动时，学习和携带这些有用的思想。

# ADTs 的历史

在 20 世纪 60 年代和 70 年代初，许多程序员和计算机科学研究者都对软件危机感兴趣。正如 Edsger Dijkstra 在他的图灵奖演讲中所说:

> “软件危机的主要原因是计算机的功能强大了几个数量级！说得直白一点:只要没有机器，编程根本不成问题；当我们只有几台较弱的计算机时，编程成了一个温和的问题，而现在我们有了巨型计算机，编程成了一个同样巨大的问题。”

他指的问题是软件很复杂。美国宇航局阿波罗登月舱和导航系统的印刷版本大约有一个文件柜的高度。代码太多了。想象一下试图阅读和理解其中的每一行。

现代软件要复杂得多。2015 年，脸书大约有 6200 万行代码。如果你每页打印 50 行，你将填满 124 万页。如果你把这些纸叠起来，你会得到每英尺 1800 页，或者 688 英尺。这比旧金山的[千禧塔](https://en.wikipedia.org/wiki/Millennium_Tower_(San_Francisco))还要高，T3 是撰写本文时旧金山最高的住宅建筑。

管理软件复杂性是几乎每个软件开发人员面临的主要挑战之一。在 20 世纪 60 年代和 70 年代，他们没有我们今天认为理所当然的语言、模式或工具。像 linters，intellisense，甚至静态分析工具还没有被发明出来。

许多软件工程师指出，他们用来构建东西的硬件大部分都能工作。但是软件通常是复杂、混乱和脆弱的。软件通常是:

*   超出预算
*   晚
*   婴儿车
*   缺失的需求
*   难以维护

如果你能以模块化的方式考虑软件，你就不需要理解整个系统来理解如何使系统的一部分工作。软件设计的原则被称为局部性。为了获得局部性，你需要*模块*，你可以独立于系统的其余部分来理解它们。您应该能够明确地描述一个模块，而不必过度指定它的实现。这就是 ADT 解决的问题。

从 20 世纪 60 年代一直延伸到今天，提高软件模块化的状态是一个核心问题。正是考虑到这些问题，包括 Barbara Liskov(SOLID OO design principles 的 Liskov Substitution Principle 中引用的相同 lis kov)、Alan Kay、Bertrand Meyer 和其他计算机科学传奇人物在内的人们致力于描述和指定各种工具来实现模块化软件，分别包括 ADT、面向对象编程和契约式设计。

1974 年到 1975 年间，ADT 出现在 Liskov 和她的学生对 CLU 编程语言的研究中。他们对软件模块规范的艺术状态做出了重大贡献——我们用来描述允许软件模块交互的接口的语言。形式上可证明的接口一致性使我们更加接近软件的模块化和互操作性。

2008 年，Liskov 因其在数据抽象、容错和分布式计算方面的工作获得了图灵奖。ADT 在这一成就中发挥了重要作用，今天，几乎每所大学的计算机科学课程都包含 ADT。

软件危机从来没有完全解决，任何专业开发人员都应该熟悉上面描述的许多问题，但是学习如何使用像对象、模块和 ADT 这样的工具肯定会有所帮助。

# ADT 的规格

可以使用几个标准来判断 ADT 规范的适用性。我称这些标准为**著名的**，但我只是发明了助记符。最初的标准是由 Liskov 和 Zilles 在他们著名的论文[“数据抽象的规范技术”中发表的](http://csg.csail.mit.edu/CSGArchives/memos/Memo-117.pdf)

*   **正式的。**规格一定要正规。规范中每个元素的含义必须定义得足够详细，以便目标受众有相当好的机会从规范中构建一个符合规范的实现。对于规范中的每个公理，必须能够用代码实现代数证明。
*   **适用。** ADTs 要广泛适用。对于许多不同的具体用例，ADT 通常应该是可重用的。在代码的特定部分用特定的语言描述特定的实现的 ADT 可能是过度指定的。相反，ADT 最适合描述通用数据结构、库组件、模块、编程语言特性等的行为。例如，描述堆栈操作的 ADT，或描述承诺行为的 ADT。
*   **极小。** ADT 规格应尽可能小。规范应该包括有趣的和广泛适用的行为部分，仅此而已。每一个行为都应该被精确和明确的描述，但是要尽可能的不具体。大多数 ADT 规范应该可以使用一些公理来证明。
*   **可扩展。**ADT 应该是可扩展的。需求中的小变化应该只导致规范中的小变化。
*   **陈述性的。**声明性规范描述*是什么，*不是*如何。*ADT 应该根据事物是什么以及输入和输出之间的关系映射来描述，而不是创建数据结构的步骤或每个操作必须执行的特定步骤。

一个好的 ADT 应该包括:

*   **人类可读的描述。如果没有人类可读的描述，ADT 可能会相当简洁。自然语言描述与代数定义相结合，可以互相检查，以消除规范中的任何错误或读者理解中的歧义。**
*   **定义。**明确定义规范中使用的任何术语，以避免任何歧义。
*   **抽象签名。描述预期的输入和输出，但不要将它们与具体的类型或数据结构联系起来。**
*   **公理。**公理不变量的代数定义，用于证明一个实现已经满足规范的要求。

# 堆栈 ADT 示例

堆栈是一个后进先出(LIFO)的项目堆，它允许用户通过将新项目推到堆栈顶部，或者从堆栈顶部弹出最近推送的项目来与堆栈进行交互。

堆栈通常用于解析、排序和数据整理算法。

# 定义

*   `a`:任意类型
*   `b`:任意类型
*   `item`:任何类型
*   `stack()`:空栈
*   `stack(a)`:一叠`a`
*   `[item, stack]`:一对`item`和`stack`

# 抽象签名

## 建筑

`stack`操作接受任意数量的项目，并返回这些项目的堆栈。通常，构造函数的抽象签名是根据其自身来定义的。请不要将这与递归函数混淆。

*   `stack(...items) => stack(...items)`

## 堆栈操作(返回堆栈的操作)

*   `push(item, stack()) => stack(item)`
*   `pop(stack) => [item, stack]`

# 公理

堆栈公理主要处理堆栈和项目标识、堆栈项目的顺序以及堆栈为空时 pop 的行为。

## 身份

推动和弹出没有副作用。如果您压入一个堆栈，并立即从同一个堆栈弹出，堆栈应该处于压入前的状态。

```
pop(push(a, stack())) = [a, stack()]
```

*   给定:将`a`压入堆栈，并立即从堆栈中弹出
*   应该:还一对`a`和`stack()`。

## 顺序

从堆栈中弹出应该遵守顺序:后进先出(LIFO)。

```
pop(push(b, push(a, stack())) = [b, stack(a)]
```

*   给定:将`a`推入堆栈，然后将`b`推入堆栈，然后从堆栈中弹出
*   应:回一对`b`和`stack(a)`。

## 空的

从空堆栈中弹出会导致未定义的项目值。具体来说，这可以用一个可能(项目)，什么都没有，或者两者都有来定义。在 JavaScript 中，习惯使用`undefined`。从空堆栈中弹出不会改变堆栈。

```
pop(stack()) = [undefined, stack()]
```

*   给定:从空堆栈中弹出
*   应:返回一对未定义的和`stack()`。

# 具体实施

一个抽象数据类型可以有许多具体的实现，在不同的语言、库、框架等中。下面是上述 stack ADT 的一个实现，使用了一个封装的对象和该对象上的纯函数:

```
const stack = (...items) => ({
  push: item => stack(...items, item),
  pop: () => {
    // create a item list
    const newItems = [...items]; // remove the last item from the list and
    // assign it to a variable
    const [item] = newItems.splice(-1); // return the pair
    return [item, stack(...newItems)];
  },
  // So we can compare stacks in our assert function
  toString: () => `stack(${ items.join(',') })`
});const push = (item, stack) => stack.push(item);
const pop = stack => stack.pop();
```

另一个是根据 JavaScript 现有的`Array`类型的纯函数实现堆栈操作:

```
const stack = (...elements) => [...elements];const push = (a, stack) => stack.concat([a]);const pop = stack => {
  const newStack = stack.slice(0);
  const item = newStack.pop();
  return [item, newStack];
};
```

两个版本都满足以下公理证明:

```
// A simple assert function which will display the results
// of the axiom tests, or throw a descriptive error if an
// implementation fails to satisfy an axiom.
const assert = ({given, should, actual, expected}) => {
  const stringify = value => Array.isArray(value) ?
    `[${ value.map(stringify).join(',') }]` :
    `${ value }`;

  const actualString = stringify(actual);
  const expectedString = stringify(expected);

  if (actualString === expectedString) {
    console.log(`OK:
      given: ${ given }
      should: ${ should }
      actual: ${ actualString }
      expected: ${ expectedString }
    `);
  } else {
    throw new Error(`NOT OK:
      given ${ given }
      should ${ should }
      actual: ${ actualString }
      expected: ${ expectedString }
    `);
  }
};// Concrete values to pass to the functions:
const a = 'a';
const b = 'b';// Proofs
assert({
  given: 'push `a` to the stack and immediately pop from the stack',
  should: 'return a pair of `a` and `stack()`',
  actual: pop(push(a, stack())),
  expected: [a, stack()]
})assert({
  given: 'push `a` to the stack, then push `b` to the stack, then pop from the stack',
  should: 'return a pair of `b` and `stack(a)`.',
  actual: pop(push(b, push(a, stack()))),
  expected: [b, stack(a)]
});assert({
  given: 'pop from an empty stack',
  should: 'return a pair of undefined, stack()',
  actual: pop(stack()),
  expected: [undefined, stack()]
});
```

# 结论

*   **抽象数据类型(ADT)** 是由公理定义的抽象概念，这些公理表示一些数据和对这些数据的操作。
*   **抽象数据类型关注的是什么，而不是如何**(它们是声明性的，不指定算法或数据结构)。
*   **常见的例子**包括列表、堆栈、集合等。
*   ADT 为我们提供了一种正式定义可重用模块的方法，这种方法在数学上是合理的、精确的、明确的。
*   ADT 产生于 20 世纪 70 年代 Liskov 和学生对 CLU 编程语言的研究。
*   **ADTs 要出名。正式的、广泛适用的、最小的、可扩展的和声明性的。**
*   **ADT 应该包括**人类可读的描述、定义、抽象签名和形式上可验证的公理。

> **额外提示:**如果您不确定是否应该封装一个函数，问问自己是否会将它包含在组件的 ADT 中。请记住，ADT 应该是最小的，所以如果它不是必需的，缺乏与其他操作的内聚性，或者它的规范可能会改变，就封装它。

# 词汇表

*   **公理**是数学上合理的陈述，必须成立。
*   **数学上合理的**意味着每一个术语都有很好的数学定义，因此可以根据它们写出明确的、可证明的事实陈述。

# 后续步骤

EricElliottJS.com 有许多小时的视频课程和类似主题的互动练习。如果你喜欢这个内容，请考虑加入。

***埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com)*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】********

*他和世界上最美丽的女人享受着与世隔绝的生活方式。*