# React 钩子会取代 Redux 吗？

> 原文：<https://medium.com/javascript-scene/do-react-hooks-replace-redux-210bab340672?source=collection_archive---------0----------------------->

## TL；大卫:钩子很棒，但是不行。

![](img/830a23d13cf90c974aedfaf4128cd16f.png)

Mandarin Duck — Malcolm Carlaw (CC-BY-2.0)

自从引入 React hooks API 以来，出现了许多关于 React hooks 是否会取代 Redux 的问题。

在我看来，hooks 和 Redux 之间几乎没有重叠。钩子没有给我们神奇的新状态能力。相反，它为我们已经可以用 React 做的事情增强了 API。然而，hooks API 使得原生的 React state API 变得更加可用，并且因为它比它所取代的`class`模型更容易，所以在适当的时候，我比以前使用*更多地使用组件状态。*

为了理解我的意思，我们首先需要更好地理解为什么我们会考虑 Redux。

## Redux 是什么？

Redux 是一个可预测的状态管理库*和架构*，很容易与 React 集成。

Redux 的主要卖点是:

*   **确定性状态解析**(结合纯组件时启用确定性视图渲染)。
*   **事务性状态。**
*   **将状态管理**与 I/O 和副作用隔离开来。
*   **应用状态的单一真实来源**。
*   **不同组件之间轻松共享状态** *。*
*   **交易遥测**(自动记录动作对象)。
*   **时间旅行调试。**

换句话说，Redux 给了你代码组织和调试的超能力。它使得构建更易维护的代码变得更容易，并且在出错时更容易找到根本原因。

## 什么是 React 钩子？

React 挂钩允许您使用状态和 React 生命周期特性，而无需使用`class`和 React 组件生命周期方法。它们是在 React 16.8 中引入的。

React 挂钩的主要卖点是:

*   **使用状态并挂钩到组件生命周期**而不使用`class`。
*   把相关的逻辑放在你的组件中的一个地方，而不是把它分割在不同的生命周期方法中。
*   **共享独立于组件实现的可重用行为**(如[渲染道具模式](https://reactjs.org/docs/render-props.html))。

请注意，这些奇妙的好处并没有真正与 Redux 的好处重叠。您可以并且应该使用 React 钩子来获得确定性状态更新，但是这一直是 React 的一个特性，Redux 的确定性状态模型很好地插入了它。这就是 React 提供确定性视图渲染的方式，也是 React 的创建动机之一。

有了像 [react-redux hooks API](https://react-redux.js.org/next/api/hooks) 和 [React 的 useReducer hook](https://reactjs.org/docs/hooks-reference.html#usereducer) 这样的工具，就没有必要二选一了。两者都用。混搭。

## 挂钩替代什么？

自从 hooks API 推出以来，我已经停止使用:

*   `**class**` **组件。**
*   **[**渲染道具**](https://reactjs.org/docs/render-props.html) **图案。****

## **挂钩不替代什么？**

**我仍然经常使用:**

*   ****基于上面列出的所有原因，Redux** 。**
*   ****更高阶的组件**组成我的所有或大部分应用程序视图共享的横切关注点，比如 Redux 提供者、公共布局提供者、配置提供者、认证/授权、i18n 等等。**
*   ****容器和显示组件之间的分离**为了更好的模块化、可测试性，以及效果和纯逻辑之间更容易的分离。**

## **何时使用钩子**

**你不需要总是为每个应用程序或每个组件使用 Redux。如果你的应用程序由单一视图组成，不保存或加载状态，没有异步 I/O，我想不出一个好的理由来增加 Redux 的复杂性。**

**同样，如果您的组件:**

*   ****不使用网络。****
*   ****不保存或加载状态。****
*   ****不与其他非子组件共享状态**。**
*   ****确实需要一些短暂的局部组件状态。****

**对于 React 的内置组件状态模型，您可能有一个很好的用例。在这些情况下，React hooks 将为您提供很好的服务。例如，下面的表单使用 React 中的本地组件状态`useState`钩子。**

```
import React, { useState } from 'react';
import t from 'prop-types';
import TextField, { Input } from '[@material/react-text-field](http://twitter.com/material/react-text-field)';const noop = () => {};const Holder = ({
  itemPrice = 175,
  name = '',
  email = '',
  id = '',
  removeHolder = noop,
  showRemoveButton = false,
}) => {
  const [nameInput, setName] = useState(name);
  const [emailInput, setEmail] = useState(email);const setter = set => e => {
    const { target } = e;
    const { value } = target;
    set(value);
  };return (
    <div className="row">
      <div className="holder">
        <div className="holder-name">
          <TextField label="Name">
            <Input value={nameInput} onChange={setter(setName)} required />
          </TextField>
        </div>
        <div className="holder-email">
          <TextField label="Email">
            <Input
              value={emailInput}
              onChange={setter(setEmail)}
              type="email"
              required
            />
          </TextField>
        </div>
        {showRemoveButton && (
          <button
            className="remove-holder"
            aria-label="Remove membership"
            onClick={e => {
              e.preventDefault();
              removeHolder(id);
            }}
          >
            &times;
          </button>
        )}
      </div>
      <div className="line-item-price">${itemPrice}</div>
      <style jsx>{cssHere}</style>
    </div>
  );
};
Holder.propTypes = {
  name: t.string,
  email: t.string,
  itemPrice: t.number,
  id: t.string,
  removeHolder: t.func,
  showRemoveButton: t.bool,
};export default Holder;
```

**这段代码使用`useState`来跟踪姓名和电子邮件的短暂表单输入状态:**

```
const [nameInput, setName] = useState(name);
const [emailInput, setEmail] = useState(email);
```

**你可能会注意到 Redux 的道具中也加入了一个`removeHolder`动作创建器。混搭就可以了。**

**像这样使用本地组件状态总是很好的，但是在 React hooks 之前，我可能会想把它塞进 Redux 并从 props 中取出状态。**

**使用组件状态意味着使用一个`class`组件，用`class`实例属性语法(或者一个`constructor`函数)设置初始状态，等等——仅仅为了避免重复就增加了太多的复杂性。有一些即插即用的工具可以用 Redux 管理表单状态，这很有帮助，所以我不必担心短暂的表单状态会渗入到我的业务逻辑中。**

**因为我已经在所有重要的应用程序中使用 Redux，所以选择很简单:Redux(几乎)所有的东西！**

**现在选择仍然很简单:**

> **组件状态为组件状态，Redux 为应用程序状态。**

## **何时使用 Redux**

**另一个常见的问题是*“应该把所有东西都放在 Redux 里吗？不这样不会破时间旅行调试吗？”***

**不，因为应用程序中有许多状态是*短暂的*和*太细粒度的*，无法为日志记录遥测或时间旅行调试之类的事情提供非常有用的信息。除非您正在构建一个实时协作编辑器，否则您可能不需要将每个用户按键或鼠标移动都置于 Redux 状态。当您向 Redux state 添加内容时，您添加了一个抽象层以及随之而来的复杂性。**

**换句话说，你应该可以随意使用 Redux，但是当你这样做的时候，你应该有一个理由。**

**如果您的组件:**

*   ****使用类似网络或设备 API 的 I/O。****
*   ****保存或加载状态。****
*   ****与非子组件共享其状态。****
*   ****处理与应用程序其他部分共享的任何业务逻辑或数据处理。****

**这是另一个来自[TDD day 应用](https://tddday.com/)的例子:**

```
import React from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { compose } from 'ramda';import page from '../../hocs/page.js';
import Purchase from './purchase-component.js';
import { addHolder, removeHolder, getHolders } from './purchase-reducer.js';const PurchasePage = () => {
  // You can use these instead of
  // mapStateToProps and mapDispatchToProps
  const dispatch = useDispatch();
  const holders = useSelector(getHolders);const props = {
    // Use function composition to compose action creators
    // with dispatch. See ["Composing Software"](/javascript-scene/composing-software-the-book-f31c77fc3ddc) for details.
    addHolder: compose(
      dispatch,
      addHolder
    ),
    removeHolder: compose(
      dispatch,
      removeHolder
    ),
    holders,
  };return <Purchase {...props} />;
};// `page` is a Higher Order Component composed of many
// other higher order components using function composition.
export default page(PurchasePage);
```

**这个组件不处理任何 DOM。它是一个容器组件，将 DOM 问题委托给一个导入的表示组件。它使用 [React-Redux 钩子 API](https://react-redux.js.org/next/api/hooks) 连接到 Redux。**

**它使用 Redux 是因为我们需要这个表单在 UI 的其他部分关心的数据，当我们完成购买流程时，我们需要将数据保存到数据库中。**

**它所关心的状态是在组件之间共享的，而不是局限于单个组件，它是持久的而不是短暂的，并且它可能跨越多个页面视图或会话。这些都是本地组件状态无法解决的问题，除非您在 React API 上构建自己的状态容器库——这比仅仅使用 Redux 要复杂得多。**

**未来，React 的[悬念 API](https://reactjs.org/docs/react-api.html#suspense) 可能会帮助保存和加载状态。我们需要等到悬念 API 登陆，看看它是否能取代我在 Redux 中的保存/加载模式。Redux 允许我们干净地将副作用与组件逻辑的其余部分分开，而不需要我们模仿 I/O 服务。(隔离效果是我更喜欢《T4》的原因。为了与这个用例竞争 Redux，React 的 API 需要提供效果隔离。**

## **Redux 是建筑**

**Redux 比状态管理库要多得多(通常也少得多)。它本质上也是 [Flux 架构](https://facebook.github.io/flux/)的一个子集，后者对于状态变化是如何发生的更加固执己见。我有另一篇博文，更深入地详述了 Redux 架构。**

**当我需要复杂的组件状态但不需要 redux 库时，我经常使用 Redux 风格的 Redux。我还使用 redux 风格的动作(甚至 Redux 工具，如 [Autodux](https://github.com/ericelliott/autodux) 和 [redux-saga](https://github.com/redux-saga/redux-saga) )来调度节点应用程序中的动作，而无需导入 Redux 库。**

**Redux 一直比 library 更有架构和不强制的约定。事实上，Redux 的基本实现可以在几十行代码中重现。**

**如果你想开始在 hooks API 中使用更多的本地组件状态，而不是重复所有的事情，这是一个好消息。**

**React 提供了一个`useReducer`钩子，它将与 Redux 风格的减速器接口。这对于非平凡的状态逻辑、依赖状态等非常有用。如果您有一个用例，您认为您可以将短暂的状态包含到单个组件中，那么您可以使用 Redux 架构，但是使用`useReducer`钩子而不是 Redux 来管理状态。**

**如果您稍后需要保持或共享状态，那么您已经完成了 90%。剩下的工作就是连接组件，并将缩减器添加到 Redux 存储中。**

## **更多问答**

> **"如果所有东西都不在 Redux 中，决定论会被打破吗？"**

**不。事实上，Redux 也不强制执行决定论。惯例会。如果你希望你的 Redux 状态是确定的，[使用纯函数](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)。如果你希望你的临时组件状态是确定的，使用纯函数。**

> **"难道你不需要 Redux 作为真理的单一来源吗？"**

**真理的单一来源原则并没有说你需要你所有的状态都来自单一的来源。相反，它意味着对于每一个国家，应该有一个单一的真理来源。你可以有许多不同的状态，每一个都有自己唯一的真相来源。**

**这意味着你可以选择什么进入 Redux，什么进入组件状态。您还可以从其他来源获取状态，比如当前位置的浏览器 href。**

**Redux 是维护应用程序状态的单一真实来源的好方法，但是如果您的组件状态局限于单个组件，并且只在一个地方使用，根据定义，它已经有了该状态的单一真实来源:React 组件状态。**

**如果你把一些东西放入 Redux 状态，你应该总是从 Redux 状态读取它。对于 Redux 中所有状态，Redux 应该是该状态的唯一真实来源。**

**如果需要的话把所有东西都放在 Redux 里也没问题。对于需要频繁更新的状态，或者具有大量依赖状态的组件，可能会有性能影响。您不应该担心性能，除非它成为一个问题，但当您这样做时，尝试两种方法，看看它是否有影响。简档，并记住铁路性能模型。**

> **"我应该使用 react-redux connect，还是 hooks API？"**

**那得看情况。创建一个可重用的高阶组件，而 hooks API 针对与单个组件的集成进行了优化。是否需要将相同的商店道具连接到其他组件？跟`connect`走。否则，我更喜欢 hooks API 的读法。例如，假设您有一个为用户操作处理许可授权的组件:**

```
import { connect } from 'react-redux';
import RequiresPermission from './requires-permission-component';
import { userHasPermission } from '../../features/user-profile/user-profile-reducer';
import curry from 'lodash/fp/curry';

const requiresPermission = curry(
  (NotPermittedComponent, { permission }, PermittedComponent) => {
    const mapStateToProps = state => ({
      NotPermittedComponent,
      PermittedComponent,
      isPermitted: userHasPermission(state, permission),
    });

    return connect(mapStateToProps)(RequiresPermission);
  },
);

export default requiresPermission;
```

**现在，如果您有一堆管理视图，它们都需要管理权限，那么您可以创建一个更高级的组件，将所有这些视图的权限要求与所有其他横切关注点结合起来:**

```
import NextError from 'next/error';
import compose from 'lodash/fp/compose';
import React from 'react';
import requiresPermission from '../requires-permission';
import withFeatures from '../with-features';
import withAuth from '../with-auth';
import withEnv from '../with-env';
import withLoader from '../with-loader';
import withLayout from '../with-layout';

export default compose(
  withEnv,
  withAuth,
  withLoader,
  withLayout(),
  withFeatures,
  requiresPermission(() => <NextError statusCode={404} />, {
    permission: 'admin',
  }),
);
```

**要使用它:**

```
import compose from 'lodash/fp/compose';
import adminPage from '../HOCs/admin-page';
import AdminIndex from '../features/admin-index/admin-index-component.js';

export default adminPage(AdminIndex);
```

**高阶组件 API 对于这个用例来说很方便，它实际上比钩子 API 更简洁(它需要更少的代码)，但是为了阅读`connect` API，你必须记住它把`mapStateToProps`作为第一个参数，把`mapDispatchToProps`作为第二个参数，你应该知道它可以接受函数或对象文字，你应该知道这些行为的区别。你还需要记住，这是咖喱，但不是自动咖喱。**

**换句话说，我发现`connect` API 用简洁的代码完成了这项工作，但它不是特别易读或符合人体工程学。如果我不需要为其他组件重用该连接，我更喜欢可读性更好的 hooks API，尽管它涉及的输入稍微多一点。**

> **"如果单例是反模式，Redux 是单例，那么 Redux 不是反模式吗？"**

**不，单例是一种代码气味，它可能表明*共享的可变状态，*才是真正的反模式。Redux 通过封装(您不应该直接在 reducers 之外改变应用程序状态，相反，Redux 处理状态更新)和消息传递(只有调度的动作对象可以触发状态更新)来防止共享的可变状态。**

## **后续步骤**

**在[EricElliottJS.com](https://ericelliottjs.com/)上了解更多关于 React 和 Redux 的信息。本文代码示例中使用的函数模式(如函数组合和部分应用程序)通过大量示例和视频演练进行了深入讨论。**

**学习[如何单元测试 React 组件](/javascript-scene/unit-testing-react-components-aeda9a44aae2)，说到测试，学习[TDDDay.com](https://tddday.com/)上的测试驱动开发(TDD)。**

*****埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com)*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】**********

***他和世界上最美丽的女人享受着与世隔绝的生活方式。***