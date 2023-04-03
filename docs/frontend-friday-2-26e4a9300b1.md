# 前端星期五 2

> 原文：<https://medium.com/compendium/frontend-friday-2-26e4a9300b1?source=collection_archive---------6----------------------->

![](img/97f4c6f56685e5183a7ce695fa208067.png)

是时候发表你最喜欢的系列文章中的另一篇文章了，在这篇文章中，我们将深入探讨上个月与前端开发相关的一些激动人心的新闻。这次我们将深入 Angular 8，Ivy，看看什么将取代 node.js 和 Edge 的下一个渲染引擎。我们还将了解什么是 Myjson，以及为什么需要它。

**TLDR**

*   Angular 8 将 Ivy 的尺寸缩减了 93%,在移动设备上的渲染速度加快了 45%
*   Node.js 的后续版本正在开发中；德诺
*   Edge 切换到闪烁(Chrome 渲染引擎)
*   Myjson，一个为你的网络或移动应用提供的简单的 json 商店

# 角 8 和常春藤

Angular 正在通过一个全新的渲染引擎进行彻底检查，这将彻底改变 Angular，甚至可能使它感觉像一个新的框架，而没有任何突破性的变化！新引擎的主要目标是使 web 应用程序更小，运行更快，更容易调试。完成 Ivy 已经成为 Angular 的首要任务，因为他们暂时拉近了与整个 Angular Material 团队的距离，以帮助完成 Ivy。他们演示了一个 hello world 应用程序，它的大小减少了 93%,在移动设备上的渲染速度提高了 45%!Ivy 将从 Angular 8 中选择加入，因为它还没有完全完成。今年年底，Angular 9 可能会发布完整版本。

如果你想了解更多关于 ivy 的信息，以及 Angular 8 中的其他新闻，请查看以下链接:

[https://blog . angular . io/a-plan-for-version-8-0-and-ivy-b 3318 DFC 19 f 7](https://blog.angular.io/a-plan-for-version-8-0-and-ivy-b3318dfc19f7)

https://www.youtube.com/watch?v=jnp_ny4SOQE&feature = youtu . be&t = 1320

# node.js 的未来，以及它的继任者 Deno

正如我们所知，Node.js 是网络的基础，包括前端和后端。然而，Node.js 有几个源于其概念的问题。最初的创建者(已经离开了这个项目)最近有一次谈话，描述了 Node.js 的缺点和他对它的继任者 Deno([https://www.youtube.com/watch?v=M3BM9TB-8yA](https://www.youtube.com/watch?v=M3BM9TB-8yA&feature=youtu.be))的计划。该项目仍处于早期阶段，还远远没有准备好，但与 Node.js 相比，它已经在一些功能上有所改进。一些例子包括切换到 typescript，更多地使用 promises(和 async await)和默认沙盒程序(无磁盘或网络)。Node.js 社区出现了一些管理不善的负面新闻，包括被临时分成两个不同的项目，一天之内三分之一的 it TCS 董事会成员辞职([https://www . zdnet . com/article/after-governance-break down-node-js-leaders-fight-for-its-survival/](https://www.zdnet.com/article/after-governance-breakdown-node-js-leaders-fight-for-its-survival/))。对于 Node.js 的继任者来说，这似乎是一个完美的时机。

你可以在这里找到德诺:【https://deno.land/】T4

# Edges 新渲染引擎

微软 Edge 团队已经选择将他们的渲染引擎替换为 Chrome 正在使用的 Blink(这是 Safari，WebKit 中渲染引擎的一个分支)。这可能是个好消息，因为这将使支持多种浏览器的 web 应用程序变得更加容易。然而，这可能是 2002 年的回归，一个浏览器家族拥有超过 90-95%的市场份额([http://www.onestat.com/html/aboutus_pressbox4.html](http://www.onestat.com/html/aboutus_pressbox4.html))。IE 受到开发者的喜爱，导致了对市场的完全控制，Chrome 现在也处于同样的位置。Chrome 和 Google 已经开始通过杀死背景标签和强迫我们使用 AMP(https://medium . com/@ ui Stephen/why-not-Google-AMP-cf 1 aeb 974463)来利用他们的统治地位。你可能会赞成，甚至喜欢这些变化，但随着他们的市场份额不断攀升，情况可能会变得更糟。微软退出渲染引擎领域意味着 chrome 将面临更少的竞争和更多的优势，这是他们可以利用的。这可能是一个回归火狐的好时机，在过去的一年里，凭借惊人的新功能和惊人的速度，女巫实际上已经成为一个很好的选择。

如果你想知道为什么 Edge 要转向 Blink，那么你应该看看这篇描述谷歌如何利用他们在网络上的中心地位让 Edge 团队别无选择只能转向的文章:[https://news.ycombinator.com/item?id=18697824](https://news.ycombinator.com/item?id=18697824)

# 本月最酷的东西:Myjson

如果你需要一种快速简单的方法来存储和检索 web 或移动应用程序的 JSON，你应该试试 myjson(【http://myjson.com/】T2)。Myjson 是一个简单干净的 web 应用程序，它允许你粘贴 json 并获得一个 URL 作为回报。您可以使用此 URL 在您的 web 或移动应用程序中检索粘贴的 JSON。例如，在后台准备就绪之前开发一个网站时，这很有用。该 API 支持创建、获取和更新 JSON 数据。