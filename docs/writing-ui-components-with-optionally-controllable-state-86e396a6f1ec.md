# 如何编写具有可选受控状态的 UI 组件

> 原文：<https://medium.com/quick-code/writing-ui-components-with-optionally-controllable-state-86e396a6f1ec?source=collection_archive---------2----------------------->

![](img/db385e95a3c263738bab5bb91d85f514.png)

[image source](http://time.com/4494496/tesla-mobileye-autopilot-safety/)

广泛的用户界面元素恰好是以下抽象的一个例子:

> *屏幕的一部分，根据某个* ***值*** *(状态)以不同方式显示，并为* ***改变*** *该值提供一组交互。*

例如:

*   一个普通的旧 input 元素:它显示输入中的值，并提供一些改变该值的方法(键入、粘贴、拖动、撤销/重做等)。
*   zippy/accordion(一个标题和一个可折叠的内容):它显示基于**打开**状态展开或折叠的内容，并提供一些更改(切换)的方法(单击标题)。
*   可调整大小的窗口:它显示一个矩形表面，其位置和大小基于表单 **{x，y，width，height}** 的值，并且它提供了一些更改该值的方法(拖动窗口标题来移动它，或者拖动其角上的调整大小手柄来调整它的大小)。

因此，是**值**和提供的**变化机制**定义了这些 UI 元素中的每一个。在本文的其余部分，我们将深入探讨实现这种“状态和变更机制”的方法。虽然这几乎是一个框架不可知的主题，但我选择了 React，它让我们用 [**钩子**](https://reactjs.org/docs/hooks-intro.html) **提出了一个非常简洁和通用的解决方案。**

让我们继续使用 zippy 组件作为一个简单但真实的例子。这是 zippy 的样子。您可以单击标题来切换内容。就是这样:

Example of a Zippy component

与您选择的框架无关，zippy 组件的实现主要由这两部分逻辑组成:

*   基于`open`值显示或隐藏细节。
*   处理点击标题，切换`open`值。

将该值的状态保存在组件中，并通过处理用户在标题上的点击来更新它，这可能很有吸引力。
下面是一个非常简单的 zippy 组件的实现，它将自己的`open`状态保存为本地状态值:

Zippy1.tsx

从 UX 的角度来看，它完全符合活泼的定义。它的用法也相当简单。你只需要这样指定头和内容:`<Zippy header="...">content</Zippy>`。仅此而已。你有一个工作的活力。但是如果你想在 zippy 打开的时候做点什么呢？zippy 组件上应该有一个 API (prop ),让您知道它何时被切换(值被更改)。
我们可以简单地在现有实现中添加一个`onToggle`道具:

Zippy2.tsx

父组件现在可以得到 zippy 状态变化的通知，但它仍然无法控制该状态。这种控制有时是必需的。例如，您的页面中可能有一堆这样的文件，您希望根据当前路线来控制打开哪一个。
为了控制组件的状态，我们需要[提升状态](https://reactjs.org/docs/lifting-state-up.html)并让父组件通过`open`状态作为输入(prop)。由于我们不再在组件中保存`open`状态，所以当用户点击标题时，我们所能做的就是让父组件知道用户的意图。由父组件来处理。
这种模式对于拦截用户的动作(例如验证)特别有用。
这是完全由其父组件控制的 zippy 组件的第三个版本:

Zippy3.tsx

这种无状态的实现也是非常简单的，从某种意义上说，我们可以在它的基础上创建有状态的版本(`Zippy1`)，可能是通过几个更高阶的组件，如 [withState](https://github.com/acdlite/recompose/blob/master/docs/API.md#withstate) 和 [withHandlers](https://github.com/acdlite/recompose/blob/master/docs/API.md#withhandlers) 。换句话说，有状态版本就像无状态版本加上内置的状态管理。

虽然这个无状态版本让我们可以完全控制它的行为，但是它将状态管理样板文件推到了组件层次结构的更高层。你不再能够通过这样的代码片段获得 zippy 的工作版本:`<Zippy header="...">content</Zippy>`
你需要这样使用它:`<Zippy header="..." open="someSource" onToggle="someHandler">content</Zippy>`。

如果我们能有一个灵活的组件，它足够聪明，能判断出我们是否想控制它的状态，那该多好。当我们不想控制它时，它可以表现得像`Zippy1`一样，而当`open` prop 通过时，它仍然支持对其状态的外部控制。您可能已经注意到，这种行为非常类似于 react-dom 为原生表单输入提供的[。您可以选择在](https://reactjs.org/docs/forms.html)[受控](https://reactjs.org/docs/forms.html#controlled-components)或[非受控](https://reactjs.org/docs/uncontrolled-components.html)模式下使用。事实上，在野外有很多组件已经采用了这种模式来支持对状态的可选控制。

所以让我们实现另一个版本的 zippy 组件，它支持对状态的可选控制。为此，我们接受一个可选的`open`道具。我们还对不受控制的使用保持开放状态。当`open`道具为`undefined`时，我们处于非受控模式。为了响应用户的点击，根据是否处于受控模式，我们或者调用`onToggle`或者设置我们的内部`open`状态。

Zippy4.tsx

这是我们的 zippy 组件的最高级版本。对其状态的控制是一个可选的特性。如果不受控制地使用 zippy，您仍然可以得到切换事件的通知。

使用这种方法，我们可以将“依赖于状态并提供改变状态方法的 UI 元素”视为接受两个可选属性的组件:**值**和 **changeHandler** (在 Angular 中，`value` 是输入，`changeHandler` 是输出)。
根据**值**和**变化处理程序**是否通过，我们有以下 4 种使用组合，每种组合在特定情况下都有效。

*   **值**⠀⠀⠀**change handler**
    ***受控*** *。*值的状态被提升到父组件，并作为属性传递。变更请求也在父组件中处理。 *示例:*只接受数字的输入。选项卡视图，其中选定的选项卡是基于当前路线确定的。
*   **值**⠀⠀⠀**change handler***✗* ***受控*** 。值是受控的(强制的),它独立于组件提供的正常更改机制。 *示例:*只读输入。不能移动或调整大小的固定窗口。
*   **值**t32】✗⠀⠀⠀**change handler
    t36】不受控制。**值作为本地状态保存在组件本身中。将通过`changeHandler`通知父组件值的变化。
    *示例*:一个可拖动的可调整大小的窗口，它由正常的交互方式控制，但是你想在它被拖动到特定边界之外时显示一个警告。
*   **值***✗*⠀⠀⠀**change handler***✗* ***不受控制。*** 值作为一个局部状态保存在组件本身中，你不关心它的变化。示例 **:** 标签视图、zippy、窗口等。当您只想要该组件的默认功能，而不关心它在任何时候的状态时。

> “第一次做对一点都不重要。最后一次做对非常重要。”
> —编程的艺术，安德鲁·亨特和戴维·托马斯

我们最终实现的 zippy 组件(`Zippy4`)支持受控和非受控模式。但是它的大部分代码(第 4-32 行)都是针对它的，而不是与 zippy 本身相关的逻辑(第 34-40 行)。换句话说，在我们的实现中混合了两种不同的关注。当我们决定实现另一个组件(比如一个时间选择器)时，这一点就变得更加明显了，它具有完全相同的行为，既支持受控使用，也支持不受控使用。

一种排除这种逻辑的方法是[一个特设的](https://reactjs.org/docs/higher-order-components.html)。它可以接受关于哪个属性可选地成为有状态的以及哪个属性被认为是改变处理器的配置。事实上，你可以找到一些这样的 HOC 的现有实现，比如带有不可控道具的[或者不可控道具的](https://github.com/klarna/higher-order-components#withuncontrolledprop-config-component)。不久前，我还试图用 typescript 编写一个(我不记得为什么，但可能是因为现有的没有一个是我想要的)，但我很快就陷入了类型定义。过了一段时间，我了解了 react 的新特性:[钩子](https://reactjs.org/docs/hooks-overview.html)。事实证明，很容易“使用”钩子来为**可选控制状态**提出一个简单、可读和直观的解决方案，而不需要额外的努力来使它对类型脚本友好。

## UseControllableState

使用 React 内置的`useState`钩子，我们可以在组件中创建一个状态变量。它返回一个有状态的值和一个更新它的函数。它也接受初始值。用`useState`创建我们的 Zippy 组件(类似于`Zippy1`)的有状态(不可控)版本非常简单:

Zippy1WithHook.tsx

我们现在想要的是用 props 可选地控制状态值(及其设置器)的能力。我们实际上需要一个新版本的`useState` ，它除了`initialValue`之外，还接受`value`和`changeHandler`。我们称之为`useControllableState`。如果`value`未定义，我们处于非受控模式，并且`useControllableState`像正常的`useState`一样运行，并且在状态改变时也调用`changeHandler`(如果通过)。否则，我们处于受控模式。

在编写`useControllableState`之前，让我们编写一些测试，确保它涵盖我们之前讨论过的所有 [4 种用法。](#aef6)

这是我们希望在组件中使用它的方式:

这与我们使用`useState` hook ( `Zippy1withHook`)的 Zippy 的有状态版本非常相似。粗略来说，唯一的区别是`useState`被换成了`useControllableState`。

我们还在测试中使用了一个小工具，它装载了我们的组件，并返回一个带有 [enzyme](https://airbnb.io/enzyme/) 包装器和几个实用函数的对象:

[最后一种用法类型](#8c2e)(当`value`和`changeHandler`都没有通过时)，是最容易测试的一种:

现在我们为 `[value](#49da)` [未通过而](#49da) `[changeHandler](#49da)` [为](#49da)时的情况[添加一个测试:](#49da)

接下来的两个测试案例用于受控模式。首先，我们测试如果[值被传递但是没有改变处理器](#7b0a)，点击按钮不会改变值:

最后，我们用变更处理器测试[受控模式:](#aef6)

以下是我们测试的完整版本:

useControllableState.test.tsx

值得注意的是，我们可以在不渲染任何东西的情况下测试`useControllableState`hook easy[。](/@se.mo.moosavi/testing-react-hooks-without-pain-7bc2c26146ed)

编写`useControllableState`的实现比它看起来要简单:

useControllableState.ts

它在内部使用状态挂钩来保存状态值。像`useState`一样，它返回一个值和一个设置器。为了确定该值，我们应该检查我们是否处于受控模式。如果`value`参数不是`undefined`，我们处于受控模式，我们返回那个值。否则，我们处于非受控模式，我们返回当前状态值。
对于 setter，我们返回一个调用`changeHandler`的函数，如果通过，还将`newValue`存储到我们的本地状态(使用我们从`useState`获得的 setter)。如果我们处于受控模式，我们可以跳过设置状态，这在某些情况下会导致较小的性能改进。

## 进一步的改进

*   **使用相同的 setter 函数**:为了与`useState`钩子更加一致，我们可以使用[回调钩子](https://reactjs.org/docs/hooks-reference.html#usecallback)返回同一个 setter，除非`changeHandler`被改变。我们还需要避免 setter 中的[闭包陷阱](https://github.com/facebook/react/issues/14010)。
*   **控制模式改变时的警告**:从受控模式改变到非受控模式(反之亦然)通常是一个无意的错误。在这种情况下，React 本身会显示一个警告。我们也可以处理并显示一个警告。

y̵o̵u̵̵c̵a̵n̵̵f̵i̵n̵d̵̵a̵̵b̵e̵t̵t̵e̵r̵̵v̵e̵r̵s̵i̵o̵n̵̵o̵f̵̵`u̵s̵e̵C̵o̵n̵t̵r̵o̵l̵l̵a̵b̵l̵e̵S̵t̵a̵t̵e̵`̵w̵i̵t̵h̵̵t̵h̵e̵s̵e̵̵e̵n̵h̵a̵n̵c̵e̵m̵e̵n̵t̵s̵̵i̵n̵̵[u̵s̵e̵c̵o̵n̵t̵r̵o̵l̵l̵a̵b̵l̵e̵s̵t̵a̵t̵e̵](https://github.com/alirezamirian/react-use-controllable-state/)̵n̵p̵m̵̵p̵a̵c̵k̵a̵g̵e̵.

**更新(2021–04–24)**:我最近在[**@ react-estorary/utils**](https://www.npmjs.com/package/@react-stately/utils)中发现了[**usecontrolled state**](https://github.com/adobe/react-spectrum/blob/main/packages/%40react-stately/utils/src/useControlledState.ts)类似的钩子。[react-庄严](https://react-spectrum.adobe.com/react-stately/index.html)是 [react 光谱库](https://react-spectrum.adobe.com/index.html)的一部分。我推荐用那个代替[usecontrolablestate](https://github.com/alirezamirian/react-use-controllable-state/)。签名有一点不同，它不支持懒惰默认值。

# 结论

大量的 UI 组件都是某种状态的函数，它们也提供了一些改变这种状态的方法。这些组件的灵活实现允许有状态和无状态的使用。在 React 中，我们可以通过`useState`钩子在组件中维护本地状态。有了`useControllableState`，我们可以维持一个由道具随意控制的本地状态。