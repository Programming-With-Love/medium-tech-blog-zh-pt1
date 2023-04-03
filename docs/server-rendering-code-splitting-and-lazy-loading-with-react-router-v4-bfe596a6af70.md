# React 路由器 v4 的服务器渲染、代码分割和延迟加载

> 原文：<https://medium.com/airbnb-engineering/server-rendering-code-splitting-and-lazy-loading-with-react-router-v4-bfe596a6af70?source=collection_archive---------0----------------------->

> "祝那些尝试服务器渲染、代码分割应用的人好运."
> — [瑞安·弗洛伦斯](https://medium.com/u/162352c45b6e?source=post_page-----bfe596a6af70--------------------------------)，React 路由器的联合创始人

接受挑战。

![](img/12d03057e4b690930cef280283ff59fc.png)

Nicole, one of our hosts, taking some guests on a [sunrise walk and picnic above Edinburgh](https://www.airbnb.com/experiences/184926)

# Airbnb 服务器渲染的一些背景

从历史上看，Airbnb 一直是一款 Rails 应用。几年前，这种情况开始改变，我们开始将 Rails 简单地用作数据层，所有渲染逻辑开始以 React 的形式迁移到 JavaScript 中。为了维护服务器渲染，我们创建并开源了 [Hypernova](https://github.com/airbnb/hypernova) ，一个 JavaScript 渲染服务。

更进一步，我们在 React Router v3 中引入了客户端路由和基于路由的代码分割，作为我们[架构改造](/airbnb-engineering/rearchitecting-airbnbs-frontend-5e213efc24d2)的一部分。这是实现平滑页面转换和较小初始页面加载的原因。

## 输入 React 路由器 v4

服务器渲染+代码拆分归结为一个需求。为了让它们一起工作，你需要在渲染之前匹配你当前的路线。

*(这篇文章的其余部分代码相当多，如果你还不熟悉 RRv4，请花些时间阅读优秀的* [*React 路由器文档*](https://reacttraining.com/react-router/web/guides/philosophy) *。另外，看看* [*webpack 代码分解文档*](https://webpack.js.org/guides/code-splitting/) *也无妨。)*

那么问题是反应路由器 v4 从集中式路由配置(具有用于异步加载的`getComponent`功能)切换到分散式版本。路线现在被内联定义，如下所示:

以这种方式定义路由意味着在实际呈现之前，您不会知道需要哪些路由/组件来呈现您的页面。为了演示为什么这是一个问题，假设我们正在异步加载`About`组件。

当我们进行服务器呈现时，我们希望呈现所有的内容，因此会生成用于`About`组件的 html 并插入到 DOM 树中。但是在客户端，我们不知道匹配`/about`路径，也不知道加载`About`组件，直到我们已经进入渲染周期。这将导致客户端/服务器不匹配错误，因为没有加载`About`组件，客户端不会创建与服务器相同的 html。这也可能意味着内容的闪烁和渲染的浪费，意味着用户体验的恶化。

# 重新集中路线

为了解决内联路由的独特问题，他们创建了`[react-router-config](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config)`。这让你可以继续在一个集中的位置定义你的路线，并在触发你的初始渲染之前匹配它们。使用该库，我们的路由定义可能如下所示:

This is the basic route structure we’ll be using in the rest of the post.

# 分散我们重新集中的路线

(我知道你在想什么……)

使用`react-router-config`很棒，但是还有一点工作要做。`react-router-config`似乎不支持异步加载组件，要求子路由过于明确。请注意，所有路径值都被定义为完整路径。

这会变得有点笨拙，并且限制了重用。为了解决这个问题，我们实现了一个映射功能，允许组件定义子路由。

这样做在开发过程中给了我们更多的自由。孙管线不再需要知道其完整路径，并且可以在任何位置插入到中心配置中，以创建深度嵌套的管线。

这是非常强大的，尤其是在大型代码库中。路由可以在多个位置重用，并且组件逻辑可以是路由不可知的，因此它们的可重用性也增加了。这使得我们目前向大型单页应用(SPA)的过渡成为可能，因为我们可以在不增加核心流程的情况下增加额外的路线。随着时间的推移，我们的 SPA 会包含更多的产品页面，因此我们可以放心，任何单个页面都只包含所需的内容。

# 定义异步路由

我们需要确保所有的组件，无论用户点击哪个路径，在我们调用渲染之前都被加载。

首先，我们将创建一个异步组件定义，然后我们将更改我们的孙路由以使用新组件。**注意静态加载函数**，稍后将使用它来确保我们准备好渲染。

此外，让我们更新我们的孙子路线，以利用新的助手函数。

## 确保路线准备就绪

现在我们导出一个路由配置，它包含一个定义静态`load`函数的路由组件。在渲染之前剩下要做的就是确保所有的东西都被加载了。

# 把所有的放在一起

现在一切就绪，我们终于准备好呈现我们的应用程序了。这是所有东西联系在一起时的样子(减去助手函数`ensureReady`、`generateAsyncComponent`和`convertCustomRouteConfig`的定义)

## 演示！

这篇文章代码有点多，我想看到所有这些都在运行会有所帮助。查看[演示库](https://github.com/gdborton/rrv4-ssr-and-code-splitting)来查看所有的东西，并随意探索代码！

## *下一步是什么*

我们一直在寻找改进的方法，包括我们的核心产品和我们所依赖的开源库。

*   向`react-router-config`提出问题/请求，希望这一过程对其他人来说更加简单。
*   随着越来越多的页面被拉进我们的 [SPA](/airbnb-engineering/rearchitecting-airbnbs-frontend-5e213efc24d2) ，研究如何最小化水合大小。

*我们一直在寻找有才华、有好奇心的人来* [*加入团队*](https://www.airbnb.com/careers/departments/engineering) *。或者，如果你只是想谈谈工作，可以随时在 twitter 上联系我*[*@ Gary borton*](https://twitter.com/garyborton)*。*

## * *关于导入的说明()

您在上面代码中看到的`import()`是相对较新的[动态导入语法](https://github.com/tc39/proposal-dynamic-import)(目前是阶段 3)。这个新语法意味着支持 ES 模块的异步加载。我们正在利用它来替换我们源代码中的 webpack 的`require.ensure`调用。

我们已经发布了一些特定于 webpack 的 babel 转换来帮助解决这个问题。[动态导入 webpack](https://github.com/airbnb/babel-plugin-dynamic-import-webpack) 和[动态导入节点](https://github.com/airbnb/babel-plugin-dynamic-import-node)。

他们可以将您的大额`require.ensure`电话转换成:

对此: