# 单元测试反应组件

> 原文：<https://medium.com/javascript-scene/unit-testing-react-components-aeda9a44aae2?source=collection_archive---------0----------------------->

![](img/4654bb4dd135baf31fcb83a7c02921b2.png)

Photo of a first attempt to test a React component by clement127 (CC BY-NC-ND 2.0)

单元测试是一门伟大的学科，它可以导致错误密度降低 40%-80%。单元测试还有其他几个重要的好处:

*   改进您的应用程序架构和可维护性。
*   通过让开发人员在实现细节之前关注开发人员体验(API ),带来更好的 API 和可组合性。
*   提供关于文件保存的快速反馈，告诉您更改是否有效。这可以代替`console.log()`和在 UI 中点击来测试变化。单元测试的新手可能会在测试驱动开发(TDD)过程上花费额外的 15% - 30%，因为他们知道如何测试各种组件，但是有经验的 TDD 实践者可能会体验到使用 TDD 节省实现时间。
*   提供了一个很好的安全网，可以增强您在添加功能或重构现有功能时的信心。

但是有些东西比其他东西更容易进行单元测试。具体来说，单元测试对于[纯函数](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)非常有效:给定相同输入的函数，总是返回相同的输出，并且没有副作用。

通常，UI 组件不属于易于单元测试的那一类，这使得坚持 TDD 的原则更加困难:首先编写测试。

首先编写测试对于实现我列出的一些好处是必要的:架构改进，更好的开发者体验设计，以及开发应用时更快的反馈。训练自己使用 TDD 需要纪律和实践。许多开发人员喜欢在编写测试之前进行修补，但是如果你不首先编写测试，你就剥夺了单元测试的许多最佳特性。

不过，这值得练习和训练。带有单元测试的 TDD 可以训练你编写更简单、更容易维护、更容易与其他组件组合和重用的 UI 组件。

我的测试领域最近的一项创新是开发了 [RITEway 单元测试框架](/javascript-scene/rethinking-unit-test-assertions-55f59358253f)，它是一个包裹在[带](https://github.com/substack/tape)周围的小包装，帮助你编写更简单、更易维护的测试。

无论你使用什么样的框架，下面的技巧将帮助你编写更好的、更易测试的、更易阅读的、更易组合的 UI 组件:

*   **UI 代码偏向纯组件:**给定相同的道具，总是渲染相同的组件。如果您需要来自应用程序的状态，您可以用管理状态和副作用的容器组件包装这些纯组件。
*   **将应用逻辑/业务规则**隔离在纯粹的 reducer 功能中。
*   **使用容器组件隔离副作用**。

# 偏爱纯成分

纯组件是这样一种组件，给定相同的属性，它总是呈现相同的 UI，并且没有副作用。例如，

```
import React from 'react';const Hello = ({ userName }) => (
  <div className="greeting">Hello, {userName}!</div>
);export default Hello;
```

这些类型的组件通常很容易测试。您需要一种选择组件的方法(在本例中，我们通过`greeting` `className`进行选择)，并且您需要知道预期的输出。为了编写纯组件测试，我使用来自`RITEway`的`render-component`。

要开始，请安装 RITEway:

```
npm install --save-dev riteway
```

在内部，RITEway 使用`react-dom/server`T5，并将输出包装在一个 [Cheerio](https://github.com/cheeriojs/cheerio) 对象中，以便于选择。如果没有使用 RITEway，可以手动创建自己的函数，将 React 组件呈现为可以用 Cheerio 查询的静态标记。

一旦有了从标记中生成 Cheerio 对象的呈现函数，就可以编写这样的组件测试:

```
import React from 'react';
import { describe } from 'riteway';
import render from 'riteway/render-component';
import match from 'riteway/match';import Hello from '../hello';describe('Hello component', async assert => {
  const userName = 'Spiderman';
  const $ = render(<Hello userName={userName} />);
  const contains = match($('.greeting').html()); assert({
    given: 'a username',
    should: 'Render a greeting to the correct username.',
    actual: contains(userName),
    expected: `Hello, ${userName}!`
  });
});
```

但那不是很有趣。如果你需要测试一个有状态的组件，或者一个有副作用的组件呢？这就是 TDD 对 React 组件真正感兴趣的地方，因为这个问题的答案与另一个重要问题的答案是一样的:“我如何使我的 React 组件更易于维护和调试？”

答案是:将您的状态和副作用从您的表示组件中分离出来。您可以通过将您的状态和副作用管理封装在一个容器组件中，然后通过 props 将状态传递到一个纯组件中来实现。

但是 hooks API 不就是为了让我们拥有扁平的组件层次结构，并且忘记所有的组件嵌套吗？不完全是。将您的代码放在三个不同的存储桶中，并使这些存储桶相互隔离，这仍然是一个好主意:

*   **显示/用户界面组件**
*   **程序逻辑/业务规则**——处理你正在为用户解决的问题的东西。
*   **副作用** (I/O、网络、磁盘等。)

根据我的经验，如果你将显示/UI 问题与程序逻辑和副作用分开，你的生活会轻松很多。这条经验法则一直适用于我，在我使用过的每一种语言和每一个框架中，包括用钩子做出反应。

让我们通过构建一个点击计数器来演示有状态组件。首先，我们将构建 UI 组件。它应该显示类似“Clicks: 13”的信息，告诉你一个按钮被点击了多少次。按钮只会显示“点击”。

显示组件的单元测试非常简单。我们实际上只需要测试按钮是否被渲染(我们不关心标签上写了什么——它可能用不同的语言写了不同的东西，这取决于用户的区域设置)。我们*确实*想要确保显示正确的点击次数。让我们编写两个测试:一个用于按钮显示，一个用于正确呈现的点击次数。

当使用 TDD 时，我经常使用两种不同的断言来确保我已经编写了组件，以便从 props 中提取正确的值。可以编写一个测试，这样就可以硬编码函数中的值。为了防止这种情况，您可以编写两个测试，每个测试一个不同的值。

在这种情况下，我们将创建一个名为`<ClickCounter>`的组件，该组件将有一个用于点击计数的道具，名为`clicks`。要使用它，只需渲染组件并将`clicks`属性设置为您希望它显示的点击次数。

让我们来看两个单元测试，它们可以确保我们从 props 中获取点击量。让我们创建一个新文件，`click-counter/click-counter-component.test.js`:

```
import { describe } from 'riteway';
import render from 'riteway/render-component';
import React from 'react';import ClickCounter from '../click-counter/click-counter-component';describe('ClickCounter component', async assert => {
  const createCounter = clickCount =>
    render(<ClickCounter clicks={ clickCount } />)
  ; {
    const count = 3;
    const $ = createCounter(count); assert({
      given: 'a click count',
      should: 'render the correct number of clicks.',
      actual: parseInt($('.clicks-count').html().trim(), 10),
      expected: count
    });
  } {
    const count = 5;
    const $ = createCounter(count); assert({
      given: 'a click count',
      should: 'render the correct number of clicks.',
      actual: parseInt($('.clicks-count').html().trim(), 10),
      expected: count
    });
  }
});
```

我喜欢创建小的工厂函数，使编写测试更容易。在这种情况下，`createCounter`将进行多次点击来注入，并使用该点击次数返回一个呈现的组件:

```
const createCounter = clickCount =>
  render(<ClickCounter clicks={ clickCount } />)
;
```

写完测试之后，是时候创建我们的`ClickCounter`显示组件了。我将我的文件和我的测试文件放在同一个文件夹中，文件名为`click-counter-component.js`。首先，让我们编写一个组件片段，看着我们的测试失败:

```
import React, { Fragment } from 'react';export default () =>
  <Fragment>
  </Fragment>
;
```

如果我们保存并运行我们的测试，我们将得到一个`TypeError`，它当前触发了 Node 的`UnhandledPromiseRejectionWarning`——最终，Node 将停止发出带有额外段落`DeprecationWarning`的恼人警告，而只是抛出一个`UnhandledPromiseRejectionError`。我们得到了`TypeError`,因为我们的选择返回了`null`,我们试图对它运行`.trim()`。让我们通过呈现预期的选择器来解决这个问题:

```
import React, { Fragment } from 'react';export default () =>
  <Fragment>
    <span className="clicks-count">3</span>
  </Fragment>
;
```

太好了。现在我们应该有一个通过的测试和一个失败的测试:

```
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
not ok 3 Given a click count: should render the correct number of clicks.
  ---
    operator: deepEqual
    expected: 5
    actual:   3
    at: assert (/home/eric/dev/react-pure-component-starter/node_modules/riteway/source/riteway.js:15:10)
...
```

要修复它，请将计数作为一个道具，并使用 JSX 中的活动道具值:

```
import React, { Fragment } from 'react';export default ({ clicks }) =>
  <Fragment>
    <span className="clicks-count">{ clicks }</span>
  </Fragment>
;
```

现在我们的整个测试套件都通过了:

```
TAP version 13
# Hello component
ok 1 Given a username: should Render a greeting to the correct username.
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
ok 3 Given a click count: should render the correct number of clicks.1..3
# tests 3
# pass  3# ok
```

测试按钮的时间到了。首先，添加测试并看着它失败(TDD 风格):

```
{
  const $ = createCounter(0); assert({
    given: 'expected props',
    should: 'render the click button.',
    actual: $('.click-button').length,
    expected: 1
  });
}
```

这会产生一个失败的测试:

```
not ok 4 Given expected props: should render the click button
  ---
    operator: deepEqual
    expected: 1
    actual:   0
...
```

现在我们将实现点击按钮:

```
export default ({ clicks }) =>
  <Fragment>
    <span className="clicks-count">{ clicks }</span>
    <button className="click-button">Click</button>
  </Fragment>
;
```

并且测试通过:

```
TAP version 13
# Hello component
ok 1 Given a username: should Render a greeting to the correct username.
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
ok 3 Given a click count: should render the correct number of clicks.
ok 4 Given expected props: should render the click button.1..4
# tests 4
# pass  4# ok
```

现在我们只需要实现状态逻辑并连接事件处理程序。

# 单元测试有状态组件

我将要向你展示的方法对于点击计数器来说可能有点过了，但是大多数应用程序都比点击计数器复杂得多。状态通常保存在数据库中或在组件之间共享。React 社区中流行的说法是从本地组件状态开始，然后根据需要提升到父组件或全局应用程序状态。

事实证明，如果您用纯函数开始本地组件状态管理，那么这个过程以后更容易管理。出于这个和其他原因(比如 React 生命周期混乱、状态一致性、避免常见错误)，我喜欢使用纯 reducer 函数来实现我的状态管理。对于本地组件状态，您可以导入它们并应用`useReducer` React 钩子。

如果您需要将状态提升到由 Redux 这样的状态管理器来管理，那么在开始之前，您已经完成了一半的工作:单元测试等等。

首先，我将为状态缩减器创建一个新的测试文件。我将把它放在同一个文件夹中，但使用不同的文件。我把这个叫做`click-counter/click-counter-reducer.test.js`:

```
import { describe } from 'riteway';import { reducer, click } from '../click-counter/click-counter-reducer';describe('click counter reducer', async assert => {
  assert({
    given: 'no arguments',
    should: 'return the valid initial state',
    actual: reducer(),
    expected: 0
  });
});
```

我总是从断言开始，以确保缩减器将产生有效的初始状态。如果您后来决定使用 Redux，它将调用每个没有状态的 reducer，以便为存储产生初始状态。这也使得创建一个有效的初始状态变得非常容易，只要你需要一个初始状态来进行单元测试，或者初始化你的组件状态。

当然，我们需要创建一个相应的 reducer 文件。我把它叫做`click-counter/click-counter-reducer.js`:

```
const click = () => {};const reducer = () => {};export { reducer, click };
```

我从简单地导出一个空的 reducer 和 action creator 开始。要了解更多关于动作创建器和选择器的重要作用，请阅读[“10 个更好的 Redux 架构技巧”](/javascript-scene/10-tips-for-better-redux-architecture-69250425af44)。我们现在不打算深入 React/Redux 架构模式，但是对这个主题的理解将有助于理解我们在这里做什么，即使您不打算使用 Redux 库。

首先，我们将看到测试失败:

```
# click counter reducer
not ok 5 Given no arguments: should return the valid initial state
  ---
    operator: deepEqual
    expected: 0
    actual:   undefined
```

现在让我们通过测试:

```
const reducer = () => 0;
```

初始值测试现在将通过，但是是时候添加更有意义的测试了:

```
 assert({
    given: 'initial state and a click action',
    should: 'add a click to the count',
    actual: reducer(undefined, click()),
    expected: 1
  }); assert({
    given: 'a click count and a click action',
    should: 'add a click to the count',
    actual: reducer(3, click()),
    expected: 4
  });
```

观察测试失败(当它们应该分别返回`1`和`4`时，它们都返回`0`)。然后实施修复。

注意，我使用了`click()` action creator 作为 reducer 的公共 API。在我看来，您应该把 reducer 看作是您的应用程序不直接与之交互的东西。相反，它使用动作创建器和选择器作为缩减器的公共 API。

我也不为动作创建者和选择器编写单独的单元测试。我总是结合减速器来测试它们。测试 reducer 就是测试动作创建者和选择器，反之亦然。如果您遵循这个经验法则，您将需要更少的测试，但是仍然可以获得与您独立测试时相同的测试和案例覆盖率。

```
const click = () => ({
  type: 'click-counter/click',
});const reducer = (state = 0, { type } = {}) => {
  switch (type) {
    case click().type: return state + 1;
    default: return state;
  }
};export { reducer, click };
```

现在所有的单元测试都将通过:

```
TAP version 13
# Hello component
ok 1 Given a username: should Render a greeting to the correct username.
# ClickCounter component
ok 2 Given a click count: should render the correct number of clicks.
ok 3 Given a click count: should render the correct number of clicks.
ok 4 Given expected props: should render the click button.
# click counter reducer
ok 5 Given no arguments: should return the valid initial state
ok 6 Given initial state and a click action: should add a click to the count
ok 7 Given a click count and a click action: should add a click to the count1..7
# tests 7
# pass  7# ok
```

只差一步:将我们的行为连接到我们的组件。我们可以用容器组件来实现。我称之为`index.js`,并将它与其他文件放在一起。它应该是这样的:

```
import React, { useReducer } from 'react';import Counter from './click-counter-component';
import { reducer, click } from './click-counter-reducer';export default () => {
  const [clicks, dispatch] = useReducer(reducer, reducer());
  return <Counter
    clicks={ clicks }
    onClick={() => dispatch(click())}
  />;
};
```

就是这样。该组件唯一的工作是连接我们的状态管理，并将状态作为道具传递给我们经过单元测试的 pure 组件。要测试它，请在浏览器中加载应用程序，然后单击“点击”按钮。

到目前为止，我们还没有看到浏览器中的组件，也没有做任何样式。为了澄清我们正在计数的内容，我将为`ClickCounter`组件添加一个标签和一些空间。我还会挂上`onClick`功能。现在代码看起来像这样:

```
import React, { Fragment } from 'react';export default ({ clicks, onClick }) =>
  <Fragment>
    Clicks: <span className="clicks-count">{ clicks }</span>&nbsp;
    <button className="click-button" onClick={onClick}>Click</button>
  </Fragment>
;
```

并且所有的单元测试仍然通过。

容器组件的测试呢？我不对容器组件进行单元测试。相反，我使用功能测试，在浏览器中运行，模拟用户与实际 UI 的交互，端到端运行。在您的应用程序中，您需要两种类型的测试(单元测试和功能测试)，对您的容器组件(主要是连接/连接组件，就像上面连接我们的 reducer 的组件)进行单元测试对我来说是多余的，并且不太容易正确地进行单元测试。通常，您必须模拟各种容器组件依赖关系才能让它们工作。

与此同时，我们已经对所有不依赖于副作用的重要单元进行了单元测试:我们正在测试数据是否得到了正确的呈现，状态是否得到了正确的管理。您还应该在浏览器中加载组件，并亲自查看按钮是否工作以及 UI 是否响应。

为 React 实现功能/e2e 测试与为任何其他框架实现它们是一样的。查看[“行为驱动开发(BDD)和功能测试”](/javascript-scene/behavior-driven-development-bdd-and-functional-testing-62084ad7f1f2)了解详情。

# 后续步骤

TDD 日是一个 5 小时的视频记录，带有关于测试驱动开发的所有方面的交互式课程。它被设计成一个很棒的全天速成课程，来提升你的整个团队的 TDD 技能。不管你目前的 TDD 经验如何，你都会学到很多。适用于[EricElliottJS.com](https://ericelliottjs.com/premium-content)的成员。

***Eric Elliott*** *是分布式系统专家，著有书籍* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](http://pjabook.com) *。作为*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，他向开发人员传授远程工作和实现工作/生活平衡所需的技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *以及包括* ***亚瑟、弗兰克·奥申、金属乐队在内的顶级录音艺术家提供软件体验******

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**