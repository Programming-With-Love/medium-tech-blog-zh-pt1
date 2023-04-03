# 测试驱动开发中 React 组件的单元测试行为

> 原文：<https://medium.com/capital-one-tech/unit-testing-behavior-of-react-components-with-test-driven-development-ae15b03a3689?source=collection_archive---------0----------------------->

![](img/7d3cd2e2ab4a1bce936639127abb9ece.png)

互联网上充满了如何对纯粹关注表示的组件进行单元测试[反应](https://reactjs.org/)的优秀例子。使用 [Jest 的快照测试](https://facebook.github.io/jest/docs/en/snapshot-testing.html)很容易做到这一点。但是如何测试利用生命周期方法的 React 组件呢？维持内部状态？有副作用，比如叫`setTimeout()`或者`setInterval()`？

测试不是纯功能性的并且负责**行为**的组件并不困难，但是网上并没有太多描述如何做的资源。本文将展示如何对这些更复杂的 React 组件进行单元测试。我们将使用测试驱动开发(TDD)方法，首先编写我们的测试。

# 示例使用案例

假设我们有一个装载指示器组件，我们希望在请求数据时显示它。如果数据加载非常快，我们希望避免加载指示器短暂闪烁。我们的产品经理告诉我们，如果用户在数据加载前的那一瞬间什么也看不到，将会提供更流畅的用户体验。因此，我们希望装载指示器组件在实际呈现装载指示器之前等待 200 毫秒。如果过去的时间少于 200 毫秒，我们不想渲染任何东西。

# 工具

对于本文，我们将使用 [Jest](https://facebook.github.io/jest/) 测试平台。 [Enzyme](http://airbnb.io/enzyme/) 是 React 的一个 JavaScript 测试工具，它提供了一种在我们的单元测试中呈现 React 组件并断言其输出和行为的方法。在 React 中使用 Enzyme 时，我们需要一个与我们正在运行的 React 版本相对应的适配器。在我们的例子中，我们将使用[酶-接头-反应-16](https://github.com/airbnb/enzyme/tree/master/packages/enzyme-adapter-react-16)

# 设置

本文的示例代码可在[本报告](https://github.com/bruceharris/react-unit-testing-example)中找到。repo 中的每个提交都映射到本文中的一个步骤。repo 使用的 [create-react-app](https://github.com/facebookincubator/create-react-app/) 已经配备了 Jest 测试运行程序。您可以复制示例 repo 并遵循这里的步骤。

我们的第一步是让一个测试运行并失败。然后我们将实现代码来通过测试。

如果您的项目还没有使用这些包，请使用 [npm](https://www.npmjs.com/get-npm) 或 [Yarn](https://yarnpkg.com/en/) 来安装它们。

```
npm install — save-dev jest enzyme enzyme-adapter-react-16
```

按照[这些步骤](https://github.com/airbnb/enzyme/tree/master/packages/enzyme-adapter-react-16#installation)来配置 Enzyme，以便在您的项目中使用相关的 React 适配器。该步骤在示例 repo 中的 [this commit](https://github.com/bruceharris/react-unit-testing-example/commit/c58ddd4e96ffe8327c793bafcf39914c72914620) 中实现。

要在示例 repo 中以交互监视模式运行测试，您需要安装 [Watchman](https://facebook.github.io/watchman/)

运行测试观察器:

```
npm test
```

如果您喜欢在没有监视的情况下手动运行测试，那么 Watchman 就没有必要了。

```
CI**=**true npm test
```

我们还没有添加任何测试，但是 create-react-app 提供了一个基本的测试。此时，当您运行测试时，您应该会看到类似这样的内容:

```
PASS  src/App.test.js
  ✓ renders without crashing (24ms)Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.612s
Ran all test suites.
```

# 要求

我们的组件将接受一个布尔属性:`isLoading`。

当`isLoading`为假时，我们将渲染组件的`children`。

当`isLoading`为真时…

*   如果已经过了 200 毫秒，我们将显示文本，表明我们正在“加载”。
*   如果没有超过 200 毫秒，我们将不会显示任何内容。

# 实施步骤

让我们开始我们的 TDD 循环。

## 创建组件和初始失败单元测试

我们的下一步是创建一个文件，导出一个组件和它的单元测试。一旦我们有一个失败的单元测试，我们可以将目标行为添加到我们的组件中，并使它通过。该步骤在示例 repo 中的 [this commit](https://github.com/bruceharris/react-unit-testing-example/commit/46ff6b4b417d91583dda3f1b0c565ba80732ff17) 中实现。

让我们从当`isLoading`为假时呈现`children`的简单例子开始。我们将为组件创建一个文件，从一个不呈现任何内容的基于类的组件开始:

让我们为单元测试创建一个文件。我们将添加一个测试来验证当`isLoading`为假时`children`被渲染。

当我们不需要关心生命周期方法或孩子时，Enzyme 的`[shallow](http://airbnb.io/enzyme/docs/api/shallow.html)()`函数允许我们隔离想要测试的组件，确保孩子不会出现在我们的单元测试中。因为我们想验证组件的子组件是否被渲染，所以我们使用 Enzyme 的`[mount()](http://airbnb.io/enzyme/docs/api/mount.html)`函数在测试运行环境中的 DOM 中挂载组件，这样我们就可以对它进行断言。

我们不想让我们的测试组件安装在测试环境的 DOM 中，所以我们调用`[unmount()](http://airbnb.io/enzyme/docs/api/ShallowWrapper/unmount.html)`来清理我们的断言。

我们的测试运行程序现在应该显示一个失败的测试，如下所示:

```
PASS  src/components/LoadingIndicator.test.js
FAIL  src/components/LoadingIndicator.test.js
  ● LoadingIndicator › when isLoading is false › should render childrenexpect(received).toEqual(expected)

    Expected value to equal:
      "<div>ahoy!</div>"
    Received:
      null

    Difference:

      Comparing two different types of values. Expected string but received null.

      at Object.it (src/components/LoadingIndicator.test.js:13:30)
          at Promise (<anonymous>)
      at Promise.resolve.then.el (node_modules/p-map/index.js:46:16)
          at <anonymous>Test Suites: 1 failed, 1 passed, 2 total
Tests:       1 failed, 1 passed, 2 total
Snapshots:   0 total
Time:        0.556s, estimated 1s
Ran all test suites.
```

如果你在你的项目中看到一个看起来不相似的测试失败，确保你已经[正确配置了酶](https://github.com/airbnb/enzyme/tree/master/packages/enzyme-adapter-react-16#installation)，并且你的 [Jest](http://facebook.github.io/jest/docs/en/configuration.html#testenvironment-string) `[testEnvironment](http://facebook.github.io/jest/docs/en/configuration.html#testenvironment-string)` [是默认的](http://facebook.github.io/jest/docs/en/configuration.html#testenvironment-string) `[jsdom](http://facebook.github.io/jest/docs/en/configuration.html#testenvironment-string)` [。](http://facebook.github.io/jest/docs/en/configuration.html#testenvironment-string)

# 让孩子通过失败的测试

*这一步是在* [*这个例子中的*](https://github.com/bruceharris/react-unit-testing-example/commit/f673f0527ebd5e228cfb6926cb8b9a22daf5638f) *提交回购。*

让这个测试通过是超级容易的；我们简单地改变我们的渲染方法来返回`this.props.children`而不是`null`。根据我们的其他需求，这显然不是正确的实现，但是本着 TDD 的精神，我们编写最少的代码来通过测试。

我们还将在示例应用程序中向`App.js`添加代码，这样我们就可以在浏览器中看到我们的组件。

使用`npm start`运行示例应用程序——这将在端口 3000 上运行一个本地 web 服务器，并打开一个指向它的浏览器窗口。

## 增加 200 毫秒后不显示任何内容的测试

*本步骤在* [*本例回购中的*](https://github.com/bruceharris/react-unit-testing-example/commit/dad3332ab55413223977b6eb286f2c6bcb59c608) *提交中实现。*

让我们转到规范中下一个最简单的部分。当`isLoading`为真时，如果 200 毫秒还没有过去，我们应该什么也不显示。

让我们添加以下测试:

我们现在应该看到测试失败了:

```
FAIL  src/components/LoadingIndicator.test.js
  ● LoadingIndicator › when isLoading is true › given 200ms have not yet elapsed › should render nothingexpect(received).toBe(expected)

    Expected value to be (using ===):
      null
    Received:
      "<div>ahoy!</div>"

    Difference:

      Comparing two different types of values. Expected null but received string.

      at Object.it (src/components/LoadingIndicator.test.js:26:32)
          at Promise (<anonymous>)
      at Promise.resolve.then.el (node_modules/p-map/index.js:46:16)
          at <anonymous>
      at process._tickCallback (internal/process/next_tick.js:188:7)PASS  src/App.test.jsTest Suites: 1 failed, 1 passed, 2 total
Tests:       1 failed, 2 passed, 3 total
Snapshots:   0 total
Time:        1.495s
Ran all test suites.
```

## 呈现 Null 以通过测试

*这一步是在* [*这个例子中的*](https://github.com/bruceharris/react-unit-testing-example/commit/15c12daab65202d86d3ffe2e79a2f59fad801534) *提交回购。*

为了通过这个测试，当`isLoading`为真时，我们返回 null。

我们的`render()`方法现在看起来是这样的:

我们的测试应该会再次通过。

# 如果 isLoading 为真并且已经过了 200 毫秒，增加显示负载指示器的测试

*这一步是在* [*这个例子中的*](https://github.com/bruceharris/react-unit-testing-example/commit/706f34fbf0a10262eea6a5452ccc9fd7dcb63a69) *提交回购中实现的。*

这是事情变得更有趣的地方。我们需要跟踪组件内部状态的延迟时间是否已经过去。我们将使用`setTimeout()`来安排状态更新，以表明我们的延迟时间已经过去。我们需要使用组件生命周期方法来触发何时设置和清除超时。

因为我们需要完整的组件生命周期和环境运行时方法`setTimeout()`和`clearTimeout()`，我们将使用 Enzyme 的`[mount()](http://airbnb.io/enzyme/docs/api/mount.html)` 函数，就像我们到目前为止所做的那样。我们不想在测试中实际等待 200 毫秒，所以我们将使用 Jest 的[定时器模拟](https://facebook.github.io/jest/docs/en/timer-mocks.html)来模拟`setTimeout()`和`clearTimeout()`。

让我们添加以下测试:

注意 Jest 的[定时器模拟](https://facebook.github.io/jest/docs/en/timer-mocks.html)方法的用法。

*   我们调用 `jest.useFakeTimers()`来告诉 Jest 模拟时间函数。
*   我们称`jest.runAllTimers()`为`setTimeout()`期过去后的快进时间。

在我们的简化示例中，我们的加载指示器标记很简单:

```
<div>loading…</div>
```

我们现在应该看到这个测试失败了:

```
FAIL  src/components/LoadingIndicator.test.js
  ● LoadingIndicator › when isLoading is true › given 200ms have elapsed › should render loading indicatorexpect(received).toBe(expected)

    Expected value to be (using ===):
      <div>loading...</div>
    Received:
      null

    Difference:

      Comparing two different types of values. Expected object but received null.
```

# 实现调度状态更新以使测试通过

*本步骤在* [*本例回购中的*](https://github.com/bruceharris/react-unit-testing-example/commit/8a75ba599a6145735de4f5a8a4ca5046124c7956) *提交中实现。*

我们将使用类属性向组件添加默认状态。当组件第一次挂载时，我们没有超过延迟时间，所以默认值为 false。

让我们添加一个 `componentDidMount()`生命周期方法，这样我们就可以在组件挂载时安排状态更新。200 毫秒的延迟时间过去后，我们更新状态以表明 `isPastDelay`为真。我们保留由`this._delayTimer`中的`setTimeout()`返回的值，以便我们稍后能够清除超时。

我们还需要用逻辑更新我们的`render()`方法，以便:

*   仅当我们没有超过延迟时间时才显示任何内容。
*   如果我们过了延迟期，显示装载指示器。

完整的组件类如下所示，我们的测试都再次通过。

## 添加测试以验证延迟时间

*这一步是在* [*这个例子中实现了*](https://github.com/bruceharris/react-unit-testing-example/commit/f689a897b8778a78bddcf059f34d3d71ba5306b7) *的提交回购。*

在我们目前的实施中，我们不能确保我们有正确的延迟时间。为了说明这一点，如果我们将超时值改为 100，我们的测试仍然通过。

我们可以使用 [Jest 的模拟函数](https://facebook.github.io/jest/docs/en/mock-function-api.html#content)来确保用正确的值调用`setTimeout()`。让我们更新我们的测试，添加关于通过`setTimeout.mock.calls`调用`setTimeout()`的断言。

我们的测试现在应该失败了。

```
FAIL  src/components/LoadingIndicator.test.js
  ● LoadingIndicator › when isLoading is true › given 200ms have elapsed › should render loading indicatorexpect(received).toEqual(expected)

    Expected value to equal:
      200
    Received:
      100
```

# 修正延迟时间以使测试通过

*此步骤在示例 repo 中的*[*This commit*](https://github.com/bruceharris/react-unit-testing-example/commit/3b44540a5af5c86a6501f80da98fbac0ee866178)*中实现。*

现在，将超时时间更新回 200 会使我们的测试通过。

# 添加测试以确保我们正在清除超时

*本步骤在* [*本例回购中的*](https://github.com/bruceharris/react-unit-testing-example/commit/47dfd911483716f64e17de006970f383fb19b9ff) *提交中实现。*

我们的组件现在实现了我们定义的所有功能需求。有了`setTimeout()`，一旦定时器超时，我们的处理函数以及它关闭的所有东西都将被垃圾收集。如果我们的组件使用了`setInterval()`，那么在我们清除超时之前，处理函数及其闭包不会被垃圾收集。在这种情况下，如果我们不自己清理，就会导致内存泄漏。虽然这在我们的例子中并不是绝对必要的，但是确保我们清理我们设置的任何计时器是一个很好的实践。

让我们添加一个测试来确保我们正在清除超时:

我们的测试现在应该失败了:

```
FAIL  src/components/LoadingIndicator.test.js
  ● LoadingIndicator › on unmount › should clear timeoutexpect(received).toEqual(expected)

    Expected value to equal:
      1
    Received:
      0
```

# 清除超时以使测试通过

*这一步是在* [*这个例子中实现了*](https://github.com/bruceharris/react-unit-testing-example/commit/da278c6d6b6958e27df25d14c5823379c269214d) *的提交回购。*

我们添加一个`componentWillUnmount()`生命周期方法并清除那里的超时。

我们的测试又通过了。

# 添加测试以确保我们清除了正确的超时

*这个步骤是在* [*这个例子中的*](https://github.com/bruceharris/react-unit-testing-example/commit/e8e4dcaa3334924c8a77a63c476da9247aa0a6e3) *这个提交回购中实现的。*

我们当前的测试没有验证我们是否将正确的参数传递给了`clearTimeout()`。为了说明这一点，去掉传递给`componentWillUnmount()`的参数。我们的测试仍然错误地通过，即使我们实际上没有清除超时。

我们希望断言传递给`clearTimeout()`的参数与我们在`componentDidMount()`方法中调用`setTimeout()`返回的值相同。为了做到这一点，我们需要知道`setTimeout()`返回的值。由于我们在测试中使用了 Jest 的假计时器，`setTimeout()` 实际上是一个模拟函数。我们可以使用 Jest 的`[mockFn.mockReturnValue()](https://facebook.github.io/jest/docs/en/mock-function-api.html#mockfnmockreturnvaluevalue)`指令 mock `setTimeout()`函数返回一个预定值。现在我们知道了期望传递给`clearTimeout()`的值。然后我们可以断言这个值— `clearTimeout.mock.calls[0][0]`是第一次调用`clearTimeout()`的第一个参数。

我们的测试现在应该失败了:

```
FAIL  src/components/LoadingIndicator.test.js
  ● LoadingIndicator › on unmount › should clear timeoutexpect(received).toEqual(expected)

    Expected value to equal:
      2
    Received:
      undefined

    Difference:

      Comparing two different types of values. Expected number but received undefined.
```

## 修正`clearTimeout`参数以使测试通过

*这一步是在* [*这个例子中的*](https://github.com/bruceharris/react-unit-testing-example/commit/113608db51e40a0da6c9ccd8cb08eca920f91260) *提交回购。*

将我们的`componentWillUnmount()`方法改回传递计时器参数会使我们的测试再次通过。

## 在浏览器行为中

我们的组件现在已经过很好的测试，但是我们仍然想探索它在浏览器中的行为。

在[提交](https://github.com/bruceharris/react-unit-testing-example/commit/cb524caca0e51823b67a27a26613a5262e5fefee)中，我们更新`App.js`来演示我们的组件在浏览器中的行为。如果运行示例应用程序，请注意当您刷新页面时，`isLoading`被设置为`true`，并且加载指示器直到一秒钟后才显示。

# 摘要

我们使用 [Jest](https://facebook.github.io/jest/) 测试平台和 [Enzyme](http://airbnb.io/enzyme/) 在我们的单元测试中呈现 React 组件，并断言它们的输出和行为。

当我们不需要关心生命周期方法或孩子时，Enzyme 的`[shallow](http://airbnb.io/enzyme/docs/api/shallow.html)()`函数允许我们隔离想要测试的组件，确保孩子不会出现在我们的单元测试中。

当我们想要验证组件的子组件是否被渲染，或者如果我们需要完整的组件生命周期和环境运行时方法，我们使用 Enzyme 的`[mount()](http://airbnb.io/enzyme/docs/api/mount.html)`函数在我们的测试运行环境中的 DOM 中挂载组件，这样我们就可以对它进行断言。

当使用`mount()`时，一定要在断言之后调用`[unmount()](http://airbnb.io/enzyme/docs/api/ShallowWrapper/unmount.html)`来清理我们的测试环境的 DOM。

我们使用 Jest 的[定时器模拟](https://facebook.github.io/jest/docs/en/timer-mocks.html)方法来模拟`setTimeout()`和`clearTimeout()`，并在我们的测试中模拟时间的变化。

我们使用 [Jest 的模拟函数](https://facebook.github.io/jest/docs/en/mock-function-api.html#content)来确保使用正确的值来调用`setTimeout()`，并使用模拟函数`setTimeout()`来返回预定的值。

# 感谢

感谢 Drew Bourne 在这篇文章的同行评论中提出的优秀建议。

***披露声明:以上观点为作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。***