# 了解流数据、微服务和开发运维

> 原文：<https://medium.com/capital-one-tech/catch-up-on-streaming-data-microservices-and-devops-42388f48ca61?source=collection_archive---------0----------------------->

为了庆祝春天的开始，首都一号交易所认为我们应该分享一些我们今年读到的、听到的和学到的东西。

# 阅读

![](img/c87c87d9f4ab52ba78fdcce496b01879.png)

很难选择一个博客来代表我们出版物的长度和广度。所以，我们选择了四个:

[**思考数据流的三种方式**](/capital-one-developers/three-ways-to-think-about-streaming-6cc39b99a56e)**——**数据流是一个非常强大的概念。但它更像一条温暖的围巾，一堆小桌子，还是一条鱼满的河？这三个参考框架有助于制定方法。

[**面向微服务的函数式编程类比**](/capital-one-developers/toward-a-functional-programming-analogy-for-microservices-ba6f49b94ad) —在拒绝或盲目实施微服务架构之前，请考虑您的需求，以及 Kafka 支持的反应式、不可变的函数式风格是否更合适。

[**在 Android 上发起活动的更好方式**](/capital-one-developers/a-better-way-to-launch-activities-on-android-8a1045181b16) —单一责任+ Kotlin！启动带有意图参数的活动对开发人员来说不是一种友好的体验。主要的难点是:如果有更好的方法呢？

[**没有测试策略，就没有开发任务**](/capital-one-developers/no-testing-strategy-no-devops-915287e1b4fd)——Paul Bruce 的客座博文。在 DevOps 圈子里，人们倾向于狭隘地关注有助于自动化的工具；但是现在，关于 QA 和测试如何适应 DevOps 产品生命周期，有一个迫在眉睫的危机。

# 听

![](img/318be6adf5e53fd4d464493a95bd9c36.png)

为什么大公司要开源？在这个独家网络研讨会上，我们邀请了来自思科、谷歌、微软和 Capital One 的专家。如果你错过了它最初播放的时候，[我们在网上提供了一段录音](https://na-sj16.marketo.com/lp/284-ESC-480/0-Open-Source-for-Enterprise_Open-Source-for-Enterprise-Panel.html?utm_source=Dev-Ex)供你欣赏。*注意——虽然录像是免费的，但您需要注册才能观看。*

# 学习

![](img/b98cf32c9f1f2a8116e3052b664124a9.png)

今年 3 月，我们发布了新的 API。不仅仅是一个新的 API，而是一个*新类型*的 API。请欢迎[金钱运动](https://developer.capitalone.com/api-products/money-movement/)——我们的第一个实验 API。

***什么是实验 API？*** —实验 API 是使用模拟数据模仿 API 功能的模拟服务。它们的设计目标是获得关于 API 可取性、功能和设计的早期反馈。这些 API 本质上是敏捷和迭代的，允许我们与我们的开发人员社区共同创建有价值的 API 解决方案。

***什么是金钱运动实验 API？*** —资金移动 API 模拟在 Capital One 账户之间以及在 Capital One 账户和其他金融机构持有的账户之间转移资金的能力。在当前迭代中，实验 API 可以检索符合条件的账户列表**，**请求转账，检索转账信息，更新转账请求。

***披露声明:以上观点为作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权均为其各自所有者所有。本文为 2018 首都一。***