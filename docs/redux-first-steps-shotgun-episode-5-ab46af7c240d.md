# Redux 第一步:猎枪第 5 集

> 原文：<https://medium.com/javascript-scene/redux-first-steps-shotgun-episode-5-ab46af7c240d?source=collection_archive---------6----------------------->

![](img/991d0b9dbb03bab579fb88c41f2f5453.png)

Midnight Cowboy — Brandon Bailey (CC-BY-2.0)

> Shotgun 是一个视频截屏节目，当我为真正的应用程序和库解决真正的编程挑战时，你可以和我一起乘坐 shotgun。这个节目只对 EricElliottJS.com 的成员开放，但是我在这里记录了这次冒险。

## 赶上:

*   [第一集](/javascript-scene/shotgun-javascript-video-experience-c8b6a7771d49)
*   [第二集](/javascript-scene/shotgun-episode-2-writing-tests-that-don-t-break-6fbce334c0c8)
*   [第三集](/javascript-scene/looping-over-child-components-in-react-shotgun-episode-3-396de16289cc)
*   [第四集](/javascript-scene/live-reloading-browser-previews-shotgun-episode-4-bab7a0508d71)

# 入门指南

如果您还没有克隆应用程序，请先克隆:

```
git clone git@github.com:jshomes/course-player.git
```

确保您的本地副本是最新的，然后查看这一集的起点:

```
git fetch — all && git merge origin/master
git checkout episode-5-start
npm install
```

然后运行测试控制台:

```
npm run watch
```

您现在应该已经做好了一切准备，可以了解这些变化如何同时影响浏览器和单元测试。

# 当前状态

我们已经构建了基础课程播放器 UI，它引导用户浏览一个信息*卡片*的播放列表，称为*卡片组*。现在是时候添加我们的状态管理特性了，为此，我们使用了 Redux。要了解更多关于 Redux 架构的信息，请查看[“10 个更好的 Redux 架构技巧”](/javascript-scene/10-tips-for-better-redux-architecture-69250425af44)。

# 减少最初的步骤

Redux 的核心构建模块是**减速器功能**。reducer 函数是应用于集合或流中的每个元素的函数，目的是将数据缩减为单个值，在本例中是应用程序的状态。

Reducers 接受两个参数，当前状态和要应用的数据，并返回下一个状态:

```
reducer(state, action) => nextState
```

在 Redux 中，reducer 是状态的唯一来源，它负责确定自己的初始状态，因此构建 reducer 的第一步是确定存储形状，如果 reducer 没有收到参数，则返回默认状态。

我总是通过单元测试开始构建 reducer，以确保 reducer 返回正确的默认状态，但是我没有对该状态进行硬编码，而是创建了一个可以从任何单元测试中调用的工厂:

这样，我可以重用工厂来创建手头测试所需的任何状态。工厂的另一个好处是，如果状态形状改变，我只需要在一个地方改变它，而不是在潜在的许多单元测试中。

# 不可变状态

Reducers 必须是纯函数(参见[“掌握 JavaScript 面试:什么是纯函数？”](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976))，意思是:

1.  给定相同的参数，总是返回相同的值。
2.  没有副作用。

我一直喜欢为应用程序状态使用不可变数据结构的想法，但是在团队中使用 reducers 之后，很明显意外的状态突变是相当常见的，甚至对于聪明的开发人员群体也是如此。

我最近开始在 reducers 中更多地使用 [Immutable.js](https://facebook.github.io/immutable-js/) 。我真的不喜欢这个 API，但是我做了一些糖叫做 [dpath](https://github.com/ericelliott/dpath) 来平滑粗糙的边缘。

即使是不可变状态，您仍然应该小心不要改变 action 对象或 reducer 之外的任何共享可变状态。

# 概述

记住，减速器应该遵循一些简单的规则:

1.  如果没有状态，reducers 应该总是返回有效的初始状态。
2.  减速器应该是纯函数。您可以使用像 Immutable.js 这样的不可变数据结构来帮助您避免意外的状态突变。

成员们，[看视频](https://ericelliottjs.com/premium-content/shotgun-episode-5-redux/)。

非会员，现在注册:

# [跟随 Eric Elliott 学习 JavaScript】](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******Zumba Fitness*******【华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**