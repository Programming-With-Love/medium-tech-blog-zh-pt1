# setState()门

> 原文：<https://medium.com/javascript-scene/setstate-gate-abc10a9b2d82?source=collection_archive---------1----------------------->

## 导航 React setState()行为混乱

![](img/04066cdb1f84778247041564c6e1865a.png)

一切都是从上周开始的。3 名不同的 React 学习者在项目中尝试使用`setState()`时遇到了 3 种不同的障碍。我经常指导新的 React 用户，并向从其他架构过渡到 React 的团队提供咨询。

其中一个学员正在做一个非常适合 Redux 的产品项目，所以我建议我们用 Redux 代替`setState()`,这样可以从绘制 DOM 的组件中移除状态更新的时间，而不是解决如何用`setState()`来固定时间。然后，该模块只需根据商店中的道具来决定要呈现什么，时间复杂度被神奇地避开了。

激发了这条推特:

“React has a setState() problem: Asking newbies to use setState() is a recipe for headaches. Advanced users have learned to avoid it. ;)

之后，一些高级用户插话纠正我:

“React team member checking in. Please learn to use setState before other approaches.”

“Those ‘adanced’ users will get left behind when we turn on async scheduling by default in React 17”

关于第二点:

“Fiber has a strategy for pausing, splitting, rebasing, aborting updates that doesn’t work if you deviate from component state”

两者都有道理。其他人创造了迷因:

![](img/04066cdb1f84778247041564c6e1865a.png)

取笑令人沮丧的情况很好，但我们不要假装没有问题。

在我与另一位学员的下一次会面中，*他也对`setState()`的工作方式感到困惑*，并且已经放弃了，将他的状态填充到一个闭包中，当然，如果闭包状态改变，它不会自动触发渲染。

鉴于*不断涌入*困惑的 React 新手，我袖手旁观了我推文的第一部分，但如果我有机会再做一次，我会稍微修改第二部分*，*因为一些高级用户(特别是许多脸书和网飞用户)广泛使用`setState()`:

> React 有一个 setState()问题:要求新手使用 setState()是一个令人头疼的问题。高级用户有秘方。”

当然，Twitter 可能仍然会失去它的集体意识。毕竟 React 是*完美的*，我们都必须认同`setState()`本来就很美，否则就要面对嘲笑和鄙视。

如果`setState()`让你困惑，那是*你的错。你一定是疯了或傻了。(我有没有提到过[JavaScript 社区存在欺凌问题？](/javascript-scene/the-js-community-has-a-bullying-problem-96c10f11c85d#.wagjqz54o))*

让我们检查一下我们的自我，不要再为我们的精通而沾沾自喜，同时嘲笑每一个没有学到同样课程的人。

这种行为是荒谬的，精英主义的，对新来者非常没有吸引力。如果人们经常对 API 感到困惑，这可能是一个改进 API 的机会，或者至少是改进文档的机会。

让社区和我们的工具变得更加友好和有吸引力对每个人都有好处。

# setState()怎么了？

这个问题有两个答案:

1.  不多。它(大部分)表现得像是需要解决它被设计来解决的问题。
2.  学习曲线。刚接触 React 和`setState()`的用户在尝试做*用普通 JS 和直接 DOM 操作就能完成的事情时经常会遇到障碍。*

React 旨在简化应用程序的构建，但是:

*   你不能随便抓取一些 DOM 并以你喜欢的方式更新它们。
*   你不能在任何时候把状态设置成任何东西，取决于你喜欢的任何数据源。
*   在组件生命周期的任何阶段，您都不能只观察屏幕上呈现的 DOM 或元素的可见性，这限制了您何时以及如何使用`setState()`呈现依赖状态(您正在处理的状态可能还没有呈现到屏幕上)。

在所有这些情况下，混淆都是由 React 组件生命周期的*(故意的，好的)*限制造成的。

## 从属状态

当我们更新状态时，有时更新的值取决于 React 试图帮助我们的事情:

*   当前状态
*   同一周期中先前更新状态的尝试
*   呈现的 DOM(例如，组件坐标、可见性、计算的 CSS 值等)

当您有这些依赖状态时，如果您试图以一种简单的方式更新状态，React 的行为可能会以一种令人讨厌的难以调试的方式让您吃惊。通常，无论你尝试做什么，都不会奏效。您将得到不正确的状态，或者您将在控制台中看到一个错误。

我对`setState()`的不满在于，它的限制性行为在 API 文档中对新手来说并不明显，而且处理其限制性行为的通用模式也没有得到很好的解释。这迫使用户求助于试错法、谷歌和其他社区成员的帮助，而在`setState()`和它的 API 文档中可以有更好的指南。

> **更新:**
> [API 文档已经更新](https://github.com/facebook/react/pull/9329)，试图更好地解释`setState()`行为。以下引用了该文档的旧版本。

当前的`setState()`API 文档以此开头:

```
setState(nextState, callback)
```

> 执行 nextState 到当前状态的浅层合并。这是从事件处理程序和服务器请求回调触发 UI 更新的主要方法。

它在结尾非常简短地提到了它具有异步行为:

> 不能保证对`setState`调用的同步操作，调用可能会被批量处理以提高性能。

这两种情况的综合结果是许多用户域错误的根源:

```
// assuming state.count === 0
this.setState({count: state.count + 1});
this.setState({count: state.count + 1});
this.setState({count: state.count + 1});
// state.count === 1, not 3
```

它本质上相当于:

```
Object.assign(state,
  {count: state.count + 1},
  {count: state.count + 1},
  {count: state.count + 1}
); // {count: 1}
```

API 文档中没有明确提到这一点(在专门指南的其他地方有所涉及)。

API 文档还提到了一个替代`setState()`签名的函数:

> 也可以传递带有签名`function(state, props) => newState`的函数。这使原子更新排队，该原子更新在设置任何值之前参考 state 和 props 的先前值。
> 
> `...`
> 
> `setState()`不会立即变异`this.state`，而是创建一个待定状态转换。在调用此方法后访问`this.state`可能会返回现有值。

API 文档删除了一些面包屑，但它们并没有真正解释新手经常遇到的行为，从而清楚地将读者引导到正确的道路上，尽管 React 以在 dev 模式下生成有用的错误而闻名，但当`setState()`计时错误突然出现时，不会记录这样的警告。

生命周期时间问题是 StackOverflow 上关于`setState()`的很多问题的原因。当然 React 很受欢迎，所以那些问题被[问了](http://stackoverflow.com/questions/25996891/react-js-understanding-setstate) [很多](http://stackoverflow.com/questions/35248748/calling-setstate-in-a-loop-only-updates-state-1-time) [次](http://stackoverflow.com/questions/30338577/reactjs-concurrent-setstate-race-condition/30341560#30341560)，有各种质量和正确性的答案。

那么新手如何才能学会管理`setState()`时机问题的正确方法呢？

在名为[“状态和生命周期”](https://facebook.github.io/react/docs/state-and-lifecycle.html)的 React 文档中有更深入的信息:

> “…要解决这个问题，请使用第二种形式的`setState()`，它接受函数而不是对象。该函数将接收以前的状态作为第一个参数，应用更新时的属性作为第二个参数:"

```
// Correct
this.setState((prevState, props) => ({
  count: prevState.count + props.increment
}));
```

这种函数-参数形式(有时称为“函数型`setState()`”)的工作方式更像这样:

```
[
  {increment: 1},
  {increment: 1},
  {increment: 1}
].reduce((prevState, props) => ({
  count: prevState.count + props.increment
}), {count: 0}); // {count: 3}
```

不确定 reduce 如何工作？参见[【排版软件】](/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c#.7k9w6v9ok)中的[【缩小】](/javascript-scene/reduce-composing-software-fe22f0c39a1d#.8d8kw0l40)。

关键是**更新函数**:

```
(prevState, props) => ({
  count: prevState.count + props.increment
})
```

这基本上是一个缩减器，其中`prevState`充当累加器，`props`充当新更新数据的来源。像 Redux 的 reducer 一样，您可以使用任何标准的 reduce 实用程序(包括`Array.prototype.reduce()`)通过这个函数进行 reduce。也和 Redux 一样，减速器应该是一个[纯函数](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)。

> 注意:试图直接变异`prevState`是新用户常见的困惑来源。

API 文档中没有提到 updater 函数的这些属性和期望，所以偶然发现函数式的`setState()`形式做了一些对象字面形式不支持的有用的事情的少数幸运的新手可能仍然会感到困惑。

# 只是新手问题？

当我处理表单或 DOM 元素坐标时，我仍然会不时碰到粗糙的边缘，因为当您使用`setState()`时，您必须直接处理组件生命周期。当您使用容器组件或存储并通过 props 传递状态时，React 会为您处理计时问题。

共享的可变状态和状态锁可能很难导航 [*不管你的经验水平*](/@mweststrate/3-reasons-why-i-stopped-using-react-setstate-ab73fc67a42e#.saj7jn6wh) *。有经验的用户更善于快速识别问题，并跳到一个方便的解决方法。*

由于新手以前没有见过这个问题，也不知道解决方法，它只是碰巧对他们打击最大。

当事情发生时，你可以与 React 斗争，或者你可以让 React 做它自己的事，顺其自然。这就是我说 Redux 有时比`setState()`、*更容易*的意思，即使对初学者来说也是如此。**

在并发系统中，状态更新通常以以下两种方式之一进行处理:

*   当其他事物正在使用状态时，锁定或限制对状态更新的访问(例如，`setState()`)或…
*   使用不变性来消除共享的可变状态，这允许不受限制地访问状态，并在任何时间点创建新的状态。(例如，Redux)

在我看来(在向许多学生教授了这两种技术之后)，第一种方法比第二种方法更容易出错，也更令人困惑。当状态更新被简单地阻塞(或者在`setState()`的情况下，批处理或延迟)时，问题的正确解决方案并不立即清晰。

当我遇到`setState()`计时问题时，我的默认反应很简单:将我的状态管理沿着树向上移动，或者移动到 Redux(或 MobX ),或者移动到容器组件。我通常出于很多原因使用和推荐 Redux，但是很明显，这不是对每个人都正确的建议。

Redux 有它自己的*巨大的学习曲线，*但是回避了共享的可变状态和状态更新时间复杂性，所以我发现一旦我教学生避免突变，它就相当顺利，*没有太多的陷阱或障碍。*

一个没有任何函数式编程经验而试图学习 Redux 的新手可能会比使用`setState()`遇到更多的麻烦——但至少有一个关于这个主题的[免费](https://egghead.io/courses/getting-started-with-redux) [课程](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)，作者是 Redux。

React 应该向 Redux book 学习一页:一个关于常见 React 模式和`setState()` gotchas 的很好的视频教程将会给 React 主页带来惊人的增加。

## 渲染前决定状态

将状态管理转移到容器组件(或 Redux)会迫使你以不同的方式思考你的组件状态，明确在你可以呈现组件之前，它的状态必须已经确定(因为你必须将它作为道具传递)。

值得重复的是:

> 渲染之前，先决定状态！

一个显而易见的推论是，试图在您的`render()`方法中使用`setState()`是一种反模式。

在你的渲染方法中计算依赖状态是很好的(例如，如果你有`firstName`和`lastName`并且你想计算`fullName`，在`render()`中这样做也是可以的)，但是我更喜欢在一个容器中计算依赖状态并把它作为道具传递给表示组件。

# setState()如何修复？

我倾向于反对`setState()`的对象字面形式。我知道这(表面上)更容易理解，也更方便，但这也是许多新用户陷入困境的原因，我认为有人这样做是不言而喻的:

```
state.count; // 0
this.setState({count: state.count + 1});
this.setState({count: state.count + 1});
this.setState({count: state.count + 1});
```

希望之后能见到`{count: 3}`。我还没有见过在同一个属性上批量对象合并是预期行为的情况。我认为，如果存在这样的情况，它们与 React 的实现细节耦合得太紧密，不适合作为有效的用例。

我还希望看到`setState()`文档的 API 部分链接到深入的[“状态和生命周期”](https://facebook.github.io/react/docs/state-and-lifecycle.html)指南，为那些试图了解`setState()`来龙去脉的用户提供更多关于这个主题的细节。因为它不同步操作或返回任何有意义的东西，简单地描述它的函数签名而不更彻底地讨论它的效果和行为是不能成功地接纳新用户的。

他们不得不求助于数小时的故障排除、谷歌搜索、StackOverflow 和 GitHub 问题。

# setState()为什么这么严格？

setState()的古怪行为不是一个错误。这是一个特点。事实上，你可能会说*这是 React 首先存在的全部原因。*

React 的驱动力之一是确保确定性呈现:给定一些应用程序状态，呈现一些特定的输出。理想情况下，给定相同的状态，总是呈现相同的输出。

为了实现这一点，React 必须通过限制变异发生的时间来管理变异。我们不只是抓住 DOM，然后在适当的地方改变它。相反，React 呈现 DOM，当一些状态改变时，React 决定如何再次呈现。*我们不渲染 DOM。React 有。*

但是为了在更新周期中不重新触发渲染，React 引入了一个规则:

在 DOM 渲染过程中，React 用于渲染的状态不能发生变化。*我们不决定组件状态何时更新。React 有。*

因此产生了困惑。当你调用`setState()`时，你认为你在设置状态。你不是。

![](img/d759aaca676b94fdba28626f0f112185.png)

“You keep using that word. I don’t think it means what you think it means.”

# 什么时候应该使用 setState()？

我几乎专门将`setState()`用于不需要保持状态的自包含功能单元。换句话说，像可重用的表单验证组件、自定义日期或时间段选择小部件、允许您自定义视图状态的数据可视化小部件等等…

我将这样的组件称为“小部件”，它们实际上由两个或更多组件组成:一个用于内部状态管理的容器，以及一个或多个处理实际 DOM 和表示方面的子组件。

以下是一些简单的试金石:

*   其他组件是否依赖于状态？
*   需要持久化状态吗？(保存到本地存储还是发送到服务器？)

如果这两个问题的答案都是“不”，也许使用`setState()`就可以了。否则，你可能要考虑别的事情。

据我所知，在脸书，他们使用由[中继容器](https://facebook.github.io/relay/)管理的`setState()`来封装脸书 UI 的不同部分，就像在更大的脸书应用程序中的迷你应用程序一样。对他们来说，这是将许多复杂的数据依赖项与实际使用它们的组件放在一起的好方法。

对于非常大的(企业级)应用程序，我推荐类似的策略。如果你的应用程序有很多代码(几十万个 LOC+)，这对你来说可能也是一个好策略——但是这种方法没有理由不能缩小规模。

也没有理由不使用类似的方法，而是将这些不同的部分分解成实际上独立的小应用程序，然后组合成更大的应用程序。我已经用 Redux for enterprise 软件做到了这一点。例如，我经常将分析仪表板、消息传递、管理、团队/用户角色管理和计费管理分离到完全独立的应用程序中，这些应用程序拥有自己的 Redux 商店。这种应用程序可以共享一个域，以及使用 API 令牌和 OAuth 的通用登录/会话管理，这样这些应用程序就像一个连接的应用程序。

对于大多数 app，我推荐**默认为 Redux** 。值得注意的是，丹·阿布拉莫夫(Redux 的创造者)不同意我的观点。他理所当然地支持让应用程序尽可能简单，直到不能再简单为止。传统的社区智慧说“不要使用 Redux，直到你感到痛苦。”

我的回答是这样的:

![](img/10b29a51ab8a5bd2e6ee2d1401d57c39.png)

“Those who are unaware they are walking in darkness will never seek the light”.

正如我已经提到的，*在某些情况下，* Redux 比`setState()`更简单。Redux 通过消除与共享可变状态和时序依赖相关的所有类型的错误，简化了状态管理。

一定要学习`setState()`，但是即使你决定不想在你的应用中使用 Redux，**你还是应该学习 Redux。**它将教你思考应用程序状态的新方法，并且可能帮助你简化你的应用程序状态，不管你为你的应用程序选择什么其他的解决方案。

对于有大量派生状态的应用程序， [MobX](https://github.com/mobxjs/mobx) 可能是比`setState()`或 Redux 更好的解决方案，因为它非常擅长有效地管理和组织计算状态。

由于其细粒度的可观察订阅模型，它也非常擅长高效地呈现大量动态 DOM 元素(成千上万)。因此，如果您正在构建一个图形游戏，或者一个监控企业微服务所有实例的控制台，它可能是实时可视化显示所有复杂信息的绝佳选择。

# 后续步骤

想了解更多关于用 React 和 Redux 构建软件的知识吗？

[跟随 Eric Elliott](http://ericelliottjs.com/product/lifetime-access-pass/) 学习 JavaScript。如果你不是会员，你就错过了！

[![](img/ebd7dfc9ae8d8938e30bdbdbe428fd4c.png)](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他曾为****Adobe Systems*******尊巴健身*******华尔街日报*******ESPN*******BBC****等顶级录音师贡献过软件经验*****

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**