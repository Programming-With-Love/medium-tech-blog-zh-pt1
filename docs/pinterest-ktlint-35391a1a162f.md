# Pinterest + ktlint = ❤

> 原文：<https://medium.com/pinterest-engineering/pinterest-ktlint-35391a1a162f?source=collection_archive---------3----------------------->

沙沙初|安卓核心体验

![](img/46d2186e06b4338d452e532d1facad7f.png)

Pinterest 的 Android 代码库成为 Kotlin-first 已经将近一年了，我们为 Kotlin 林挺和格式化采用 [ktlint](https://ktlint.github.io/) 也已经有大约两年了。今天，我们分享 Pinterest 已经正式接管了这个项目。

最初，我们选择 ktlint 是因为它的简单性、活跃的社区、可扩展性和响应速度极快的所有者。它还可以很容易地与我们现有的基于 [Phabricator](https://www.phacility.com/phabricator/) 的工作流集成，并且，在我们的 [Arcanist](https://secure.phabricator.com/book/phabricator/article/arcanist/) 库中添加了大约 65 行 PHP 代码后，我们能够在不同的基础上对我们的 Kotlin 文件应用林挺和格式化。

几个月前，当 Stanley shy iko(kt lint 的开发者)呼吁为这个项目寻找新的所有者时，我们立刻产生了兴趣。这不仅是我们在 Pinterest 每天使用的工具，也是我们回馈 Kotlin 社区的切实方式。在与斯坦利的几次会面后，很明显这是一个很好的选择，我们很高兴作为所有者向前迈进。

在短期内，项目运行和维护的方式不会有任何改变。我们仍然欢迎并鼓励外部以[问题](https://github.com/pinterest/ktlint/issues/new)和[拉动请求](https://github.com/pinterest/ktlint/pulls/new)的形式对项目做出贡献。从中长期来看，我们计划遵循 Stanley 提出的路线图，实现一种全局禁用规则的方法(ktlint 的[最受欢迎的功能](https://github.com/pinterest/ktlint/issues/208))，集成一个官方的 Gradle 插件，并更新 ktlint-core 中的一些 API 以支持更清晰的规则编写。如果您对这些项目中的任何一个感兴趣，请联系我们或打开一个拉动式请求。最后，如果您或您的公司使用 ktlint，请打开一个 Pull 请求，将您自己添加到[采纳者列表](https://github.com/pinterest/ktlint/blob/master/ADOPTERS.md)。

我们致力于继续 Stanley 开始的伟大工作，并在不断发展的 Kotlin 社区中与其他开发人员合作。

*感谢 Garrett Moon 和 Jon Parise 协助将 ktlint 引入 Pinterest，感谢 Google 的 Kevin Bierhoff、Beth Cutler 和 James Lau 提供的技术支持。*