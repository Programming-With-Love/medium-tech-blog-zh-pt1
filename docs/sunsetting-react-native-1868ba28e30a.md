# 日落反应自然

> 原文：<https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a?source=collection_archive---------1----------------------->

## 由于各种各样的技术和组织问题，我们将关闭 React Native，并尽全力制作 native amazing。

![](img/4a7ba10e4affd494b36930062bc1855a.png)

*这是* [*系列博文*](/airbnb-engineering/react-native-at-airbnb-f95aa460be1c) *中的第四篇，在这篇博文中，我们概述了 React Native 的使用体验以及 Airbnb 的下一步移动应用。我们今天在哪里？*

尽管许多团队依赖 React Native，并计划在可预见的未来使用它，但我们最终无法达到我们最初的目标。此外，还有许多我们无法克服的[技术](/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)和[组织](/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)挑战，这将使继续投资 React Native 成为一项挑战。

因此，向前看，我们正在 Airbnb**淘汰 React Native**并将我们所有的努力重新投入到 Native。

# 未能达到我们的目标

## 动作快点

当 React Native 按预期工作时，工程师能够以无与伦比的速度前进。然而，我们在本系列中概述的众多技术问题和组织问题给许多项目增加了挫折和意外的延迟。

## 维护质量栏

最近，随着 React Native 的成熟和我们积累了更多的专业知识，我们能够完成许多我们不确定是否可能的事情。我们构建了共享元素过渡、视差，并能够显著提高一些经常丢帧的屏幕的性能。然而，一些[技术挑战](/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)比如初始化和异步优先渲染使得满足某些目标变得很有挑战性。内部和外部资源的缺乏使这变得更加困难。

## 写一次代码而不是两次

尽管 React 原生特性中的代码几乎完全跨平台共享，但我们的应用程序中只有一小部分是 React 原生的。此外，需要大量的桥接基础设施来使产品工程师有效地工作。结果，我们最终在三个平台上支持代码，而不是两个。我们看到了移动和网络之间代码共享的潜力，并能够共享一些 npm 包，但除此之外，它从未以有意义的方式实现。

## 改善开发人员体验

React Native 的开发者体验是一个大杂烩。在某些方面，比如构建时间，情况明显好了很多。然而，在其他方面，比如调试，情况要糟糕得多。详细内容在本系列的第 2 部分中列举。

# 日落计划

因为我们无法实现我们的具体目标，我们已经决定 React Native 不再适合我们。我们目前正在与团队合作制定一个健康的过渡计划。我们已经停止了所有新的 React 原生功能，并计划在年底前将大多数流量最高的屏幕转移到原生功能。这得益于一些无论如何都要发生的预定的重新设计。我们的原生基础架构团队将在 2018 年全年支持 React Native。2019 年，我们将开始减少支持，并减少一些 React 本机开销，如启动时初始化运行时。

在 Airbnb，我们是开源的坚定信仰者。我们积极使用和贡献于世界各地的许多开源项目，并且也开源了我们的一些 React 原生工作。由于我们已经远离 React Native，我们无法像社区应得的那样维护我们的 React Native repos。为了做对社区最有利的事情，我们将把我们的一些 React 原生开源工作迁移到 [react-native-community](https://github.com/react-native-community) ，我们已经开始用 [react-native-maps](https://github.com/react-community/react-native-maps) 和 [native-navigation](https://github.com/airbnb/native-navigation) 和 [lottie-react-native](https://github.com/airbnb/lottie-react-native/) 做这些工作。

# 这并不全是坏事

虽然我们无法用 React Native 实现我们的目标，但使用 React Native 的工程师通常都有积极的体验。这些工程师中:

*   60%的人将他们的经历描述为惊人的。
*   20%略呈阳性。
*   15%略呈阴性。
*   5%的人强烈反对。

如果有机会，63%的工程师会再次选择 React Native，74%的工程师会考虑在新项目中使用 React Native。值得注意的是，这些结果中存在固有的选择偏差，因为它只调查了选择使用 React Native 的人。

这些工程师在 220 个屏幕上编写了 80，000 行产品代码，以及 40，000 行 javascript 基础架构。作为参考，在每个本机平台上，我们有大约 10 倍的代码量和 4 倍的屏幕数量。

# 反应本土正在走向成熟

这一系列帖子反映了我们迄今为止对 React Native 的体验。然而，脸书和更广泛的 React Native 社区致力于让 React Native 大规模应用于混合应用。React Native 的进展比以往任何时候都快。去年有超过 2500 次提交，脸书[刚刚宣布](https://facebook.github.io/react-native/blog/2018/06/14/state-of-react-native-2018)他们正在解决我们正面面对的一些技术挑战**。**即使我们将不再投资 React native，我们也很高兴能继续关注这些发展，因为 React Native 的技术胜利将转化为世界各地使用我们产品的人们的现实胜利。

# 外卖食品

我们将 React Native 集成到大型现有应用中，这些应用继续以非常快的速度发展。我们遇到的许多困难是由于我们采用了混合模型方法。然而，我们的规模使我们能够承担并解决一些小公司可能没有时间解决的难题。让 React Native 与 Native 无缝地工作是可能的，但具有挑战性。每个使用 React Native 的公司都会有一种体验，这种体验是他们的团队组成、现有应用程序、产品要求和 React Native 成熟度的独特功能。

当所有的东西都在一起时，许多特性都是这样，迭代速度、质量和开发人员体验都达到或超过了我们所有的目标和期望。有时，真的感觉我们正处于改变移动开发游戏的边缘。尽管这些经历非常鼓舞人心，但当我们权衡利弊以及工程组织的当前需求和资源时，我们决定不再适合我们。

决定是否使用一个新的平台是一个重大的决定，完全取决于你的团队特有的因素。我们的经历和离开的原因可能不适用于你的团队。事实上，[许多](/@Pinterest_Engineering/supporting-react-native-at-pinterest-f8c2233f90e6) [公司](https://instagram-engineering.com/react-native-at-instagram-dd828a9a90c7)今天仍在成功地使用它，它可能仍然是许多其他公司的最佳选择。

虽然我们从未停止对 native 的投资，但 sunsetting React Native 释放了更多资源，使 Native 比以往任何时候都更好。跟随本系列的下一部分[来了解 native 中的新特性。](/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)

这是一系列博客文章的第四部分，重点介绍了我们在 React Native 的体验以及 Airbnb 的下一步移动应用。

*   [第 1 部分:在 Airbnb 上反应原生](/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)
*   [第二部分:技术](/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)
*   [第 3 部分:建立跨平台移动团队](/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)
*   [*第四部分:对 React Native 做出决定*](/airbnb-engineering/sunsetting-react-native-1868ba28e30a)
*   [第五部分:手机的下一步是什么](/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)