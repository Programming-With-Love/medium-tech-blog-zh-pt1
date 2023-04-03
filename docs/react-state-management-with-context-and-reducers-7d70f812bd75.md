# 用上下文和缩减器反应状态管理

> 原文：<https://medium.com/version-1/react-state-management-with-context-and-reducers-7d70f812bd75?source=collection_archive---------0----------------------->

今天我想开始一系列关于 React 使用上下文的状态管理的文章。

在这第一篇文章中，我想给你一个状态管理的概述，什么是 React 上下文以及它与外部状态管理器的比较。

![](img/7e30025765fa314e3cec7d1b155b53d4.png)

# 概观

我很久以前就开始开发 web 应用程序了。一开始，一切都是服务器端的:PHP、ASP.NET 等等。前端很少或没有 JavaScript。然后我不得不添加越来越多的 JavaScript，但是当应用变得复杂，我不得不处理跨不同浏览器和浏览器版本的 DOM 时…“哦，这真是一场噩梦！”😱

![](img/4053bdf3c84480abc350dc331b4c76fe.png)

Photo by [Yogendra Singh](https://unsplash.com/@yogendras31?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/screaming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

然后 jQuery 出现了，一切都变了…“OMG 这就是未来！”😍直到我不得不开发一个复杂的应用程序…😱

然后我从 Angular.js 开始，再一次，“耶！这是游戏规则的改变！未来就是现在！!"😍直到我不得不开发另一个复杂的应用程序…😱

我仍然在这个循环中，我怀疑它是无限的。当我在 2017 年开始使用 React 时，我又有了一个😍感觉是因为与我以前使用的相比，这在我的开发体验中是一个很大的改进。它被设计成一个用于构建 ui 的 JavaScript 库，使用基于状态的组件呈现的声明式风格。它非常擅长处理本地组件状态。它允许您使用普通 JavaScript(或 TypeScript)并导入任何 JavaScript 库。它没有规定一种方法来处理 web 应用程序的任何不同方面，比如全局状态。这个方面，**状态管理**，是前端开发中最难的一个。

我们必须在浏览器中管理应用程序的状态。这个应用程序状态的一部分是纯 UI 状态，但其余部分通常只是服务器状态的缓存(可能来自数据库)。我们使用无状态协议，比如 HTTP 来获取服务器状态。所以，我们必须设法处理软件开发的另一个最困难的方面:**缓存失效**。

这可能解释了为什么 React 有这么多的状态管理库:Redux、MobX、Apollo、React Query、SWR、反冲、Zustand 等等。

我过去使用过 Redux，我认为让它工作所需的样板文件和复杂性(我称之为“管道系统”)太多了，尤其是对新手来说。

![](img/f0d2e1a152cf838af0f517f34d9ead26.png)

Photo by [Crystal Kwok](https://unsplash.com/@spacexuan?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

一旦管道到位，Redux 开始工作，事情就简单多了。但随着应用程序的增长，你必须创建更多的 reducers、actions、dispatch calls 等。在你的编辑器的标签森林里有很多打开的文件。我认为我们应该简化这一点，就像我们通过使用像 *create-react-app* 、 *NextJS* 等应用构建器来简化全局配置和其他复杂方面一样。

在本文中，我想解释如何在没有任何外部库的情况下以简单的方式处理状态管理，使用内置的 React 上下文 API 和 *useContext* 和 *useReducer* 钩子。特别是，我将展示如何实现一个非常简单的有限状态机(FSM)来处理一部分全局状态。

# 目标

我的目标是回答以下问题:

1.  什么是反应上下文？
2.  何时使用外部状态管理器？
3.  如何用 React 上下文管理全局状态？
4.  如何用上下文和 *useReducer* 创建一个简单的有限状态机？

# 什么是反应上下文

React Context“提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性”。

基本上，它是一个可以从上下文提供者的子树中的任何组件与之交互的对象。因此，它需要一个具有默认上下文对象的提供程序，并且该提供程序的所有子级都可以使用该对象。由于这个对象允许我们在应用程序级别处理状态，我们可以说 React 有一个内置的状态管理机制；不需要安装外部库。

适用于:

*   缓慢移动的全局数据:主题、认证用户、偏好等。
*   当数据改变时，许多 UI 元素需要更新
*   微前端:数据占用空间小

# 何时使用外部状态管理器

有时使用许多状态管理库中的一个会更好。由于它们不是 React 的一部分，我们必须做一些研究。然后，我们应该尝试并评估其中的几个，并最终选择一个，我们从那一刻起就依赖于它。因此，重要的是所选择的库有一个广泛的社区来支持和维护它。

适用于:

*   快速移动的数据。
*   更大的应用，更复杂的状态
*   标准很重要时的团队合作

# 如何用 React 上下文管理全局状态

我们应该将全局状态分成更小的部分，并尽可能靠近使用它的组件来使用这些部分。根据依赖于这些子状态的特性，从逻辑上将全局状态划分为子状态。然后，将上下文放在尽可能靠近使用它的组件的地方。

使用 TypeScript 对定义和维护状态也很有帮助。当您重构状态时，例如，通过重命名、更改函数签名、添加或移除元素，如果您犯了任何错误，TypeScript 都会立即通知您。

# 一个不那么简单的例子

由于我已经厌倦了基本的例子，我想向您展示一个中等复杂度的状态管理例子。假设我们想要使用 React 上下文和 TypeScript 管理当前登录用户的状态。该状态将包含:

*   用户资料:名/姓，电子邮件，设置等。在登录前和注销后将为空。
*   用户功能:
    `login
    logout
    acceptTermsAndConditions
    updateUserProfile`

这些函数将处理用户配置文件，因此我们不必在使用该配置文件的组件中担心它。我们不必直接修改用户配置文件。所以，我们来定义一下:

现在让我们定义上下文本身，它包括用户配置文件和功能:

例如，如果我们想要改变一个用户设置，我们用新的设置调用 *updateUserProfile* ，并且 *userProfile* 将被更新(并且可能我们将调用 API 在服务器端更新它)。那么所有使用上下文提供者中概要文件的组件都将被呈现。这一点很重要，因为如果受影响的组件树非常大，将会发生大量的组件渲染，可能会降低应用程序的速度。稍后我们将看到如何通过记忆上下文对象来部分缓解这个问题。

现在让我们使用上面的接口创建上下文对象。我们需要传递这个对象的初始值，所以我们传递 null 作为 *userProfile* 来表示用户没有登录:

我们将创建一个定制的钩子来帮助组件使用这个上下文。它将检查它是从上下文提供程序内部调用的(从相应的提供程序外部使用上下文是一个常见的错误):

然后，假设我们需要一个组件来显示用户的电子邮件，如果用户已经登录，需要一个按钮来注销，否则需要一个到登录页面的链接；我们只需导入这个 *useCurrentUser* 钩子，并分解用户配置文件和*注销*函数:

当然，这个组件应该在上下文提供者内部，否则会抛出一个错误，并且组件不会被呈现。

让我们看看如何为提供者创建一个包装器，它应该尽可能地放在需要的地方:

如你所见，我们正在记忆 *UserContext* 以避免在提供者级别不必要的渲染。我们稍后将添加“TODO”逻辑。

现在，我们将把这个提供者放在离需要它的地方尽可能近的地方。假设我们只在用户页面需要它。我们可以像这样使用这个上下文提供程序:

然后，在组件 *UserContactInfo* 、 *UserSettings* 和 *UserAccount* 中，我们可以使用带有 *useCurrentUser* 的上下文，如下所述(参见*componentsusingusercontext*):

```
const { /* objects and functions you need */ } = useCurrentUser();
```

最后，让我们看看如何在提供者中实现剩余的逻辑(在 *CurrentUserProvider* 中的“TODO”部分):

如你所见，我们可以通过实现 *useAnyUserAPI* 定制钩子来使用任何 API 调用机制(fetch，Axios…)。然后，我们必须处理使用效果中的响应:

*   在第一个 useEffect 中，当从 API 成功接收时，我们设置用户配置文件。
*   在第二个 useEffect 中，在成功的 API 调用注销后，我们将用户配置文件设置为 null。
*   在第三个 useEffect 中，当 API 调用 OK 时，我们只设置 *termsAccepted* 的值；其余的用户资料道具保持不变。

在这些情况下，以及当调用 updateUserProfile 时，所有依赖于 UserProfile 的组件都将使用更新后的配置文件重新呈现。这些 useEffects 通过更新配置文件来处理对 API 端点的成功调用(*登录、注销、acceptermsandconditions*)。在生产应用程序中，我们也应该处理错误响应。为此，我们可以在上下文中添加错误属性，这样，当组件进行 API 调用时，它可以检查错误，并可能显示一条消息或 toast 通知。此外，我们可能会添加一些功能，以便在通知用户后清除错误。

如下所述，API 调用逻辑是在自定义钩子 *useAnyUserAPI* 中实现的，在这里你可以使用 fetch、Axios 或任何其他库，所以我们的上下文不依赖于此。您可以在补充本文的代码中找到使用 Axios 的实现(在本系列的最后一篇文章中)。

![](img/579815964f5cd909f47b623450b193a7.png)

Photo by [Artem Sapegin](https://unsplash.com/@sapegin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/react-context?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

现在我们有了所有的用户状态和逻辑，可以在应用程序的任何地方使用。在下一篇文章中，我们将看到如何测试它！

# 关于作者:

路易斯·卡纳斯是 Version 1 的高级软件工程师。