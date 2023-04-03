# 宝宝的第一反应

> 原文：<https://medium.com/javascript-scene/baby-s-first-reaction-2103348eccdd?source=collection_archive---------3----------------------->

## React 的“Hello，World”示例

# React 入门

让我们面对现实吧。开始使用 React 要比想象中困难得多。首先你必须设置你的环境(参见[“如何使用 ES6 进行同构 JavaScript 应用”](/javascript-scene/how-to-use-es6-for-isomorphic-javascript-apps-2a9c3abe5ea2))，然后你必须找出正确的语法来创建一个组件，然后你必须找出如何管理状态。在你停止爬行开始奔跑之前，有很多东西要学。

## 婴儿的第一步

1.  设置 Babel —阅读[“如何将 ES6 用于同构 JavaScript 应用”](/javascript-scene/how-to-use-es6-for-isomorphic-javascript-apps-2a9c3abe5ea2)。
2.  了解如何创建基本组件。让我们保持它非常简单，只是试图渲染“你好，世界！”，并可以选择用不同的单词替换“world”。
3.  1 & 2 是你学习 React 所需要的，但这不会帮助你构建一个真正的应用程序。没有数据就没有 app。**状态**(又名数据)管理值得[一本书的一大块全部留给它自己](https://leanpub.com/learn-javascript-react-nodejs-es6/)，所以这篇文章将只向你展示*绝对最小*你必须学会让一些东西工作。

## 先爬后走

您需要做的第一件事是创建一个组件。下面是您一直在寻找的简单示例:

## 我的牛奶里有 HTML 吗？

那一小段 HTML 实际上并不是 HTML。这是 **JSX** :一种标记语法，看起来像 HTML，但行为像写得很好的 JavaScript。阅读[“JSX 看起来令人厌恶，但对你有好处”](/javascript-scene/jsx-looks-like-an-abomination-1c1ec351a918)。你需要巴贝尔帮你转过去。(滚动回步骤 1)。

您可能想知道如何让您的网页实际呈现这个组件。首先，您需要一些 HTML 来加载一些脚本标签:

如果你懂一些 HTML，这看起来应该很熟悉。

> [如果你以前从未见过 HTML
> 这里有一本书适合你](http://amzn.to/1FHdMgT)

那些 *`cdnjs.cloudflare.com`* 脚本标签指向流行的 JavaScript CDN(内容交付网络)上的 React 库。

还有几个其他有趣的观点。在正文的顶部，有一个名为*`内容`*的 *`div`* 。

在最后一个脚本块中，我们调用 *`React.render()`* 并将您的自定义元素作为第一个参数传递，并使用 DOM API 告诉 React 哪个 DOM 元素将成为 React 的**根节点。** React 将在该节点中呈现您的组件，并将所有 UI 事件委托给它。单个事件侦听器拾取 React 元素树中的所有事件活动，并将其传递给事件处理程序。

没错。React 拥有**自动事件授权。**想都不用想。

因为 React 需要控制它的根节点才能正常工作，所以不要用 JavaScript 直接操纵根节点是很重要的，不要使用 body 标签作为 React 的根节点也是很重要的，因为同一页面上的其他 JavaScript 很可能会与 React 冲突并导致奇怪的错误。

# 使用变量

“你好，世界！”是一个很好的开始，但有点老套。让我们试试别的东西。首先，我们将使用该组件制作一个模板，这样我们就可以填充{ *空格* }:

当然，我们需要对我们的 *`React.render()`* 调用进行调整:

# ***你好！***

![](img/e57f0805baed33ffb4946b4956c285f2.png)

Jorge Elías — Hello (CC BY 2.0)

## 你们是怎么互动的？

如果你不能玩玩具，它们就不会很有趣。别担心，React 有很好的特性来处理用户交互。我们在[“JSX 看起来像一个令人憎恶的东西”](/javascript-scene/jsx-looks-like-an-abomination-1c1ec351a918)中谈了一点 React 的事件系统。让我们用一些类似的代码让你改变我们打招呼的对象。当用户点击“小狗”这个词时，它会变成一个输入字段，让他们键入一个替代品。

要做到这一点，我们需要维持某种状态。React 允许在组件中保持状态，但是在外部**存储中保持状态通常是一个更好的主意。**

我们要去购物吗！？

不抱歉。一个**商店**只是一个*商店*你的状态的地方。要做到这一点，我们需要给主应用程序添加一些内容。

React 没有告诉你如何在你的应用程序中处理状态。你可能听说过[通量](https://facebook.github.io/flux/docs/overview.html)。通量不是 React 的一部分。它甚至不是一个库(尽管有可用的 [flux helper 库](https://github.com/facebook/flux))。这是一个*架构。*

关于实现 Flux 架构的正确方法，有许多相互冲突的想法，并且许多实现都有足够的不同，它们与 Flux 只有很远的关系。有些人根本不用助焊剂。您可能也听说过 Immutable.js 和 RxJS。这对一个婴儿来说太难了，所以我们将在另一篇文章中讨论这个问题。

# 游戏时间到了！

让我们开始互动版。

首先，我们将从 HTML 中移除内联 JS，并将其移动到名为 *`app-client.js`* 的脚本中:

注意，因为我们不再有内联脚本，所以我们能够移除 JSX 转换器。我们将使用 Babel 编译脚本，这样 JSX 在加载到浏览器之前就已经被转换了。在你公开你的应用程序之前，你一定要这样做。

接下来，我们将更新 JSX，使其具有交互性:

在交互式 JSX 示例中，我们添加了一些内容:

## 风格

我们需要样式来控制用户是否看到编辑模式或显示模式。让我们来看看我们是如何创造这些风格的:

如你所见，我们只是检查一个“模式”变量，并根据值显示或隐藏相应的元素。我喜欢这样做有几个原因。

1.  因为 React 只更新发生变化的 DOM，所以将两个视图都保存在 DOM 中会在呈现之间保留视图状态。
2.  如果您显示和隐藏带有子组件的组件，当您在 DOM 中保留更多标记时，可能会有性能优势，因为 React 更新时需要更改的内容会更少。

## 事件监听器

回头看看新的 JSX，您会注意到有几个事件侦听器。阅读[“JSX 看起来令人厌恶，但对你有好处”](/javascript-scene/jsx-looks-like-an-abomination-1c1ec351a918)了解 React 的自动事件委托，以及为什么这种标记是处理事件的一种非常好的方式。

在这个例子中，我们定义了几个事件处理程序。 *`onClick`* 使用新的 ES6 arrow 函数语法内联定义。不错！

*`onKeyUp`* 引用同名函数。让我们来看看:

如您所见，如果按下的键不是“Enter”键，该功能不会执行任何操作。否则，我们将新单词设置为表单输入字段的值，然后将模式改回*“display”*。简单到目前为止！

让我们看看经过这些修改后，整个 *`Hello`* 组件是什么样子的:

该文件中还有一些值得一提的内容:

1.  没有*`类`。*创建 React 组件不需要类。阅读文章的其余部分，了解我为什么不使用它们。
2.  我们出口的是工厂，而不是组件本身。在这个例子中，它让调用者传入 *`React`* 实例，这样可以避免在同一个页面上有多个 React 实例的常见问题。这会导致令人困惑的错误。但它也允许您在以后需要时向组件添加配置选项。
3.  *`propTypes '。* React 允许您指定组件属性的类型。在调试模式下，如果你试图将错误的类型传递给你的组件，你将在浏览器的开发控制台得到一个错误。它还使你的组件接口自文档化，并可能有助于可视化开发工具，允许你拖放组件到应用程序布局。
4.  *`.componentDidUpdate()`* 除了 *`.render()`* 方法，React 还提供了多种生命周期挂钩。在这种情况下，我们要等到组件更新之后，才能访问 DOM 并聚焦输入字段。每当您指定一个元素 *`ref`* 属性时，它将被添加到 *`this.refs`.*

> **提示:**不要试图在 render 方法中访问 refs。会造成错误。您应该在 DOM 呈现后运行的生命周期挂钩中访问它们。

这一切都很好，但是应用程序如何处理我们的更改呢？

我将要向您展示的只是一个占位符。当你学习 Flux 和 stores 或其他 React 友好的数据模型时，你会想要使用它们。这是我们新的 *`app-client`* 文件的源文件:

这里没什么花里胡哨的。我们需要 *`Hello`* 组件，定义保存我们数据的变量，定义一些动作，然后调用 *`React.render()`* ，传入道具。

在这个例子中，action 函数直接访问数据，并直接调用 *`render()`。这是我能想到的最简单的例子，不用解释事件发射器、反应流、库 API 和一堆婴儿可能会窒息的小部件就能让这个组件工作。你不会想在真正的应用程序中这样做。说到这里……*

## 当心坏的例子

你知道那个总是向人扔玩具的蹒跚学步的小孩吗？你不会想学那个例子的。不幸的是，互联网上充斥着你应该尽量避免的坏例子。

如果你查看 React 文档或 google 上的例子，你会被两种不同形式的例子轰炸。第一个， *`React.createClass()`* (非正式)**已弃用。这意味着有一种新的更好的方法，并且正在被淘汰。**

第二种形式你可能会看到很多使用 ES6 *`class`* 关键字。我不认为这是个好主意。为什么[避课](/javascript-scene/how-to-fix-the-es6-class-keyword-2d42bb3f4caf)？

> [类惹是生非](/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3)、
> 他们是一个
> 、**的尴尬合体。**

## 为什么上课很尴尬？

React 与**不可变数据结构**和**反应流**配合得最好——这些概念是从**函数式编程中借鉴来的。**对于一篇“Hello，world”帖子来说，这些都是很大的话题，所以你可以[在后面的其他文章中读到关于不变性](/javascript-scene/the-dao-of-immutability-9f91a70c88cd)和[函数式编程](/javascript-scene/the-two-pillars-of-javascript-pt-2-functional-programming-a63aa53a41a4)。

现在，相信我。关于 React 架构和生态系统，课程会让你产生错误的想法。尽可能地坚持使用 [**无状态函数**](https://github.com/reactjs/react-future/blob/master/01%20-%20Core/03%20-%20Stateless%20Functions.js) 和可组合组件，这样你就为将来的 React 做好了准备。

## 什么是无状态函数？

状态只是数据的一个花哨的词。有状态函数保存数据。你可能会说这就像把你的玩具扔得满地都是。有人可能会被绊倒而受伤。

当您使用无状态的函数和组件时，不会有任何麻烦。用成熟的术语来说，抓住状态不放会导致错误，因为程序的不同部分可能会试图以冲突的方式操作同一状态。

## 完整的源代码

当然，我已经将完整的 [React hello world 示例](https://github.com/ericelliott/react-hello)发布到 GitHub。如果你仔细看，你会在 *`package.json`* 和 *`.eslintrc`* 文件中看到一些其他的巧妙之处。享受吧。

# 你好，反应过来！

![](img/b22a79c937f87ed9b7e0fb28c64e8362.png)

Ren Kuo — p — (CC BY-SA 2.0)

> 学习 JavaScript app 开发
> 用 Node，ES6 & React。
> 
> [现在就预订
> 终身访问所有
> 我的 JavaScript 课程。](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【学习 JavaScript 同构 App 开发用节点，ES6，&【React】*](https://leanpub.com/learn-javascript-react-nodejs-es6/)*。他为 Adobe Systems******尊巴健身*******华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**