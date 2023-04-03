# 直播重装浏览器预告:猎枪第 4 集

> 原文：<https://medium.com/javascript-scene/live-reloading-browser-previews-shotgun-episode-4-bab7a0508d71?source=collection_archive---------4----------------------->

![](img/991d0b9dbb03bab579fb88c41f2f5453.png)

Midnight Cowboy — Brandon Bailey (CC-BY-2.0)

> Shotgun 是一个新的视频截屏节目，当我为真正的应用程序和库解决真正的编程挑战时，你可以和我一起乘坐 shotgun。这个节目只对 EricElliottJS.com 的成员开放，但是我在这里记录了这次冒险。

## 赶上:

*   [第一集](/javascript-scene/shotgun-javascript-video-experience-c8b6a7771d49)
*   [第二集](/javascript-scene/shotgun-episode-2-writing-tests-that-don-t-break-6fbce334c0c8)
*   [第三集](/javascript-scene/looping-over-child-components-in-react-shotgun-episode-3-396de16289cc)

在这一集里，是时候看看我们的组件如何在真实的浏览器中呈现了。

## 为什么选择 Browsersync？

有一段时间，由于 Dan Abramov 的出色工作，我很喜欢 React 的热模块更换，但当纯组件在工作中抛出扳手时，这种情况就土崩瓦解了。

丹和他的朋友们正在努力寻找解决方案，他们已经一起破解了 React Hot Loader 3.0 (RHL)的一个 alpha 版和一个 beta 版，但在撰写本文时，仍有[个问题需要解决](https://github.com/gaearon/react-hot-boilerplate/pull/61)。当它有一段时间变得稳定时，我想把这个工具和 Browsersync 一起重新组合起来。

在此期间， [Browsersync](https://www.browsersync.io/) 只是工作。它不会在您重新加载时保留您的应用程序状态，但会在您保存文件时重新加载。Browsersync 还有一些 RHL 没有提供的好处:

1.  它不是 React 特定的——不管您选择什么样的框架，它都能工作。
2.  它可以同时将预览状态同步到多个浏览器，包括外部设备。

让我们更深入地讨论第二点:它提供了一个外部 URL，允许您使用其他设备(如手机和平板电脑)浏览预览。您在任何设备上执行的每个操作都会反映在所有设备上。键入文本，点击按钮，在任何设备上滚动，你将能够同时看到它在每个设备上的外观。

## 今天的目标

在浏览器中查看预览的目的是捕捉我们在单元测试中可能错过的可视化内容。当你只进行单元测试时，很容易忽略视觉上的小问题。在浏览器中查看您的作品也非常重要。

当我们识别出实际组件代码的问题时，我们将在做出更改之前添加单元测试。

值得注意的是，我没有对 CSS 进行单元测试。视觉设计的变化与代码的其他部分完全不同。产品团队可能会不断地运行 A/B 测试，微调用户界面的视觉外观，我们不希望单元测试阻碍这一进程。对视觉设计进行单元测试也有点棘手。我们的单元测试将关注技术细节，而不是担心输出的精确像素尺寸和颜色。

给定一些数据，正确的数据会被渲染吗？按钮有正确的类吗？组件是否呈现了正确的文本？你明白了。让设计和 QA 团队去担心视觉回归测试。

您的团队可能决定您也想要自动化那些，并且那可能成为您的责任的一部分，但是您可能会用一种与单元测试完全不同的工具来做这件事(有基于图像的可视化回归测试工具)。

在我们开始之前，让我们先列出一个待办事项清单:

1.  安装 Browsersync。
2.  在 *`package.json`.* 中新建一个 *`sync`* 脚本
3.  确保所有东西的样式都是正确的。

我很确定我们没有彻底测试所有的按钮状态，我们可能也想修改现有的样式。在静态模型中，您一次只能看到一个状态。Browsersync 将让我们轻松地探索和检查所有的状态。

## 入门指南

如果您还没有克隆应用程序，请先克隆:

```
git clone [git@github.com](mailto:git@github.com):jshomes/course-player.git
```

确保您的本地副本是最新的，然后查看这一集的起点:

```
git fetch — all && git merge origin/master
git checkout ep4-start
```

我喜欢在本地安装浏览器同步，这样它就可以和*【NPM 安装】:*一起安装

```
npm install --save-dev browser-sync
```

接下来，将上面的 *`sync`* 脚本添加到您的 *`package.json`* 文件的 *`scripts`* 部分:

您需要同时运行 *`watch`* 脚本和 *`sync`* 脚本。我在两个独立的终端选项卡中运行它们。如果您的终端不支持标签，您可以在单独的终端窗口中运行它们。

```
npm run sync
```

记下外部 URL，这样您就可以在移动设备上浏览它，体验 Browsersync 到底有多酷。我强烈建议你这么做。在另一个终端中:

```
npm run watch
```

我们的监视脚本监视更改并自动重建，这将触发 Browsersync 刷新。

您现在应该已经做好了一切准备，可以了解这些变化如何同时影响浏览器和单元测试。通常，我会在不同的屏幕上运行浏览器和开发控制台，这样在我编码的时候，我可以很容易地一次看到所有的东西。在您的工作流程中尝试一下。

如果想在剧集结束后浏览代码，可以查看 *`ep4-finished`* 标签。

猎枪视频系列是会员的专属福利。立即加入并赶上前几集:

# [跟随 Eric Elliott 学习 JavaScript】](https://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他曾为****Adobe Systems*******Zumba Fitness*******【华尔街日报】*******BBC****等顶级录音师****

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**