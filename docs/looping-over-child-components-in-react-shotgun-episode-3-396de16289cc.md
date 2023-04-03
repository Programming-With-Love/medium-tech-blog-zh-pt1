# 在 React: Shotgun 第 3 集的子组件上循环

> 原文：<https://medium.com/javascript-scene/looping-over-child-components-in-react-shotgun-episode-3-396de16289cc?source=collection_archive---------3----------------------->

![](img/991d0b9dbb03bab579fb88c41f2f5453.png)

Photo: Midnight Cowboy — Brandon Bailey (CC-BY-2.0)

Shotgun 是一个新节目，当我为真正的应用程序和库解决真正的编程挑战时，你可以和我一起乘坐 shotgun。这个节目只对 EricElliottJS.com 的成员开放，但是我在这里记录了这次冒险。

## 赶上:

*   [第一集](/javascript-scene/shotgun-javascript-video-experience-c8b6a7771d49)
*   [第二集](/javascript-scene/shotgun-episode-2-writing-tests-that-don-t-break-6fbce334c0c8)

## 在子组件上循环

我最喜欢 JSX 的一点是，它不是一个简单的令牌替换模板引擎。这是一个编译到 JS 的 DSL，专门设计来让 UI 工作感觉像写 HTML:只是更好。

这一设计决策的结果之一是，JSX 不需要像许多模板语言那样的循环或条件结构。相反，您使用常规的 JavaScript 来处理这些事情。在最新的一期中，我演示了如何使用 *`Array.prototype.map()`* 遍历 list 子元素，并将它们呈现在一个列表中。结果看起来像这样:

这意味着，对于 *`cardList`* 数组中的每一项，返回一个对应的 *`CardListItem`* ，这是另一个 React 组件。

下面是 *`CardListItem()`* 的样子:

注意，列表中的每一项都有一个 *`key`* 属性。每个 *`key`* 必须是唯一的。这是 React 的一个要求，这样它就有一种惟一识别组件的方法，即使属性发生了变化，列表呈现也因此受到影响。换句话说，React 使用这些键来记住哪些动态组件是哪些动态组件，并跟踪它们的状态。阅读[“为什么键很重要”](http://blog.arkency.com/2014/10/react-dot-js-and-dynamic-children-why-the-keys-are-important/)了解当你忽略它们时会出现什么问题。

正如你将在截屏中看到的，当你忘记*、*键时，React 会发出警告，就像我在第一遍中经常做的那样。(感谢所有有益的警告，React team！)

## JSX 的条件班

您可能想知道那个 *`makeClasses()`* 调用是关于什么的:

只是让条件类变得轻而易举的捷径。该函数如下所示:

正如您所看到的，它只是将一串字符串作为参数，并返回一个包含这些参数的经过精心筛选的空格分隔的字符串。我将这个技巧与三元表达式结合使用，就像你在上面看到的比较 *`card.id`* 和 *`currentCardId '的那个一样。*

## 测试数据的工厂默认值

同时，回到单元测试，我们最初为每个测试手工构建道具，看起来像这样:

这种方法的问题是，随着组件复杂性的增加，所需的道具可能会悄悄进来，这将迫使您重构所有的测试。或者，您可以创建如下所示的工厂:

我称之为**默认工厂**。作为设置传入的任何内容都将覆盖默认属性。其他一切都将使用默认值。

现在大多数*`道具`*的创作看起来是这样的:

```
const props = makeProps();
```

要覆盖像 *`isCompleted`* 这样的道具，你可以这样调用它:

```
const props = makeProps({ isCompleted: true });
```

在重构所有测试以使用新工厂之后，让` *cardList`* 成为必需的参数没有问题。如果没有工厂，我们将需要为每个测试添加大约 14 行代码。有了工厂，我们实际上能够使大多数测试变得更小，而不是更大。

另一个好处是:如果我们不得不改变道具的结构(一个真实的可能性)，我们将需要在更少的地方更新它:只有直接处理改变的道具的测试。

如果你是会员，现在就可以随意观看第三集。

如果你还不是会员，看看预告视频，加入我们的。Shotgun 与[终身访问通行证](https://ericelliottjs.com/product/lifetime-access-pass/)捆绑在一起，您可以访问我们的所有视频:网络广播系列和课程内容，涵盖 TDD、原型 OO、函数式编程、React、通用应用程序开发、ES6+等主题。

> [向 Eric Elliott 学习 JavaScript](http://ericelliottjs.com/product/lifetime-access-pass/)
> 成为终身会员:
> 网络广播
> 视频体验
> 书籍&更多…

***Eric Elliott*** *著有《编程 JavaScript 应用程序》* *(O'Reilly)，以及* [*《学习通用 JavaScript 应用程序开发与节点&*](https://leanpub.com/learn-javascript-react-nodejs-es6/)*。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**