# 用 Sitka 减少 Redux 样板文件

> 原文：<https://medium.com/quick-code/reduce-redux-boilerplate-with-sitka-a8c4bc6fe8f9?source=collection_archive---------0----------------------->

## 介绍 React/Redux/Redux Saga 的新框架

作者阿伦·拉乔和弗兰克·梅萨——最初发布于 2019 年 2 月 15 日

![](img/39fc90e694c335d43509238ed13e946e.png)

Evergreen trees in an open field. Photo: Diverse Graphics via [Pexels](https://medium.com/u/5c7773c5a002?source=post_page-----a8c4bc6fe8f9--------------------------------)

Sitka 是一个小而强大的国家管理框架。它将应用程序组织成模块，每个模块管理一个逻辑状态区域。它建立在 Redux 和 Redux Saga 之上，为您提供这些工具的所有好处，而没有样板文件。

让我们检查一个简单的计数器应用程序，对比传统的 [Redux](https://redux.js.org/) / [Redux Saga](https://redux-saga.js.org/) 实现和 Sitka 实现之间的差异。

# 简单的计数器应用程序

为了便于说明，我们将使用一个使用 [TypeScript](https://www.typescriptlang.org/) 构建的简单计数器应用程序，其状态存在于 Redux 存储中，并使用 Redux Saga 来协调其逻辑。

在简单的应用程序中，通过调用 Redux reducer 直接使用的动作来改变状态可能就足够了。然而，在较大的应用程序中，Sagas 提供了一个有用的机制来触发围绕单个逻辑动作的复合操作，例如“增量计数器”。例如，在最终调用缩减器来更改状态之前，increment 操作可能需要权限检查、日志记录，甚至是对辅助数据存储的异步访问。

这里讨论的计数器应用程序是一个更大的应用程序的简化模型，类似于我们在 Olio Apps 创建的其他应用程序。

# 传统的方式

使用 TypeScript、Redux 和 Redux Saga 构建计数器应用程序需要:

1.  Redux 中的计数器状态
2.  增量动作创建者的界面
3.  使用界面的动作创建者
4.  Sagas 索引中此动作的监听器，路由到一个 Saga
5.  从 Redux 获取当前计数器状态的选择器
6.  一个 Saga，它处理动作的有效负载以及任何其他应用程序副作用，并使用第二个 Redux 动作创建器通过 Redux 状态设置一个新值
7.  这个面向 reducer 的动作创建器的接口
8.  面向减速器的动作创建者的动作
9.  倾听价值设定行动创建者的缩减器
10.  渐缩管与根部渐缩管的配准

下面是这些组件的完全实现的实现:

TypeScript、Redux 和 Redux-Saga 一起使用很棒。您将获得强类型代码库、Redux 状态管理以及同步和异步操作的简单控制流的所有优势。

但是正如您从上面看到的，这个堆栈的一个明显的缺点是典型的用法需要大量的样板文件！这就是 Sitka 派上用场的地方。

# 使用 Sitka 实现应用程序

Sitka 大幅减少了样板文件的数量。完成上述相同计数器应用程序所需的所有代码都可以使用 Sitka 编写，如下所示:

完整的反申请可在 Github 上找到[。](https://github.com/frankmeza/sitka-counter-ts)

类内设置了`moduleName`和`defaultState`，并定义了单个生成器函数`*handleCounter`，它只是将计数器加 1。

`public`和`private`关键字用于显示哪些类方法可以从类本身之外调用。有关 TypeScript 这个特性的更多信息，请参见他们关于[类](https://www.typescriptlang.org/docs/handbook/classes.html)的手册。在这种情况下，`getCounter`被标记为`private`，因此只有`CounterModule`可以进入它自己的状态。

这比第一个例子要少得多。这使得它更容易维护，也更容易推理。总的来说，这给你留下了一个更干净的代码库。

在你的表现部分，你可以这样称呼`*handleIncrement`:

您的模块中定义的`handleIncrement`生成器封装在一个在引擎盖下创建的 Action 中，因为它的名称以`handle`开头。

# 引擎盖下发生了什么

Sitka 通过自动生成代码使您不必编写样板文件。它生成:

*   包装每个生成器函数的动作
*   支持 setState 方法的缩减器
*   redux 状态树的一部分，由 sitka 类属性`moduleName` -在本例中为`counter`-定义。

# 你什么时候应该使用 Sitka

如果您打算使用 typescript 和 redux 构建一个更大的应用程序，我们建议您使用 Sitka。Sitka 保持您的代码的强类型化和组织化，同时减少重复和类型化脚本的样板负担。更少的打字=更快的开发时间。

# 升级现有应用程序

虽然您可以使用 Sitka 从头开始构建您的应用程序，但它也被设计为增量添加到现有的 Redux 应用程序中。查看将 Sitka 添加到项目的[示例，以及使用 React-Redux 的`connect`函数将您的组件连接到 Sitka 支持的 Redux `store`的](https://github.com/olioapps/sitka/wiki/Adding-Sitka-to-a-Redux-store)[示例。](https://github.com/olioapps/sitka/wiki/React-web-usage)

# Olio Apps 正在使用 Sitka

我们在实际项目中使用 Sitka，和它一起工作很愉快。我们可以利用强类型代码库的所有优势，同时也享受在使用 Sitka 之前编写一小部分所需代码的乐趣。我们 Olio Apps 希望你在下一个项目中试用 Sitka 来管理状态，甚至在现有项目中实现一个新功能。

Olio Apps 为网络、手机和其他平台开发软件。要阅读更多类似的文章，请订阅我们的时事通讯。

*原载于*[*www.olioapps.com*](https://www.olioapps.com/blog/sitka-module-manager/)*。*