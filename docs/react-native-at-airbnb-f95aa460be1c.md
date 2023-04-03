# 在 Airbnb 本地反应

> 原文：<https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c?source=collection_archive---------2----------------------->

## 2016 年，我们在 React Native 上下了大赌注。两年后，我们准备好与世界分享我们的经验，并展示接下来的事情。

![](img/468964a9aad03fb2d06e019503ee1fa0.png)

Years later, it’s still possible to book a meeting in our Airstream

这是一系列博客文章中的第一篇，我们在文章中概述了我们使用 React Native 的体验以及 Airbnb 的下一步移动应用。

10 年前 Airbnb 推出时，智能手机还处于起步阶段。从那时起，智能手机已经成为我们日常生活中的一个重要工具，特别是随着越来越多的人在全球旅行。作为一个为数百万人提供新的旅行方式的社区，拥有一个世界级的应用程序至关重要。移动设备通常是他们出门在外时的主要或唯一通信方式。

自 2008 年我们的第一批三位客人入住劳施街酒店以来，手机使用量已经从零增长到每年数百万的预订量。我们的应用程序让主人能够在旅途中管理他们的列表，并为旅行者提供灵感，让他们触手可及地发现新的地方和体验。

为了跟上移动使用的加速步伐，我们的团队已经发展到 100 多名移动工程师，以实现新的体验并改善现有的体验。

# 在 React Native 上下赌注

我们不断评估新技术，以使我们能够改善客人和主人使用 Airbnb 的体验，快速行动，并保持良好的开发人员体验。2016 年，其中一项技术是 React Native。当时，我们意识到移动对我们的业务有多么重要，但我们没有足够的移动工程师来实现我们的目标。因此，我们开始探索替代方案。我们的网站主要是用 React 构建的。在 Airbnb 中，它是一个非常有效且广受欢迎的网络框架。正因为如此，我们将 React Native 视为向更多工程师开放移动开发的机会，并通过利用其跨平台的特性更快地发布代码。

当我们开始投资 React Native 时，我们就知道存在风险。我们正在向我们的代码库添加一个新的、快速发展的、未经验证的平台，这个平台有可能使代码分散，而不是统一。我们也知道，如果我们要投资 React Native，我们希望做得正确。React Native 的目标是:

1.  让我们**作为一个组织行动更快**。
2.  保持本土设定的**品质条**。
3.  为移动设备编写一次产品代码**而不是两次**。****
4.  **改进**开发者体验**。**

# **我们的经历**

**在过去的两年里，这项实验变成了一项严肃的努力。我们已经在我们的应用中建立了一个非常强大的集成，以支持复杂的本机功能，如共享元素转换、视差和地理围栏，以及到我们现有本机基础架构的桥梁，如网络、实验和国际化。**

**我们已经使用 React Native 为 Airbnb 推出了许多关键产品。React Native 使我们能够推出 Airbnb 的全新业务 [Experiences](https://www.airbnb.com/s/experiences) ，以及从评论到礼品卡的数十种其他功能。这些特性中有许多是在我们根本没有足够的本地工程师来实现我们的目标的时候构建的。**

**不同的团队对 React Native 有着广泛的经验。React Native 有时被证明是一个不可思议的工具，同时也给其他人带来了技术和组织方面的挑战。在这个系列中，我们将详细介绍我们的经验以及我们下一步要做的事情。**

**[在第二部分](/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)中，我们列举了 React Native 作为一项技术的成功之处和失败之处。**

**[在第三部分](/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)，我们列举了一些与建立跨平台移动团队相关的组织挑战。**

**[在第四部分](/airbnb-engineering/sunsetting-react-native-1868ba28e30a)，我们强调了 React Native 的现状以及它在 Airbnb 的未来。**

**[在第五部分](/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)中，我们从 React Native 中吸取了最重要的经验，并利用这些经验让 Native 变得更好。**