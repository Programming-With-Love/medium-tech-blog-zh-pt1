# Pinterest + ktlint =❤

> 原文：<https://medium.com/pinterest-engineering/pinterest-ktlint-4b8543aa8d12?source=collection_archive---------2----------------------->

作者 Sha Sha Chu | Android Core 体验

这篇文章最初发表在 英语;Read the English version [here](/pinterest-engineering/pinterest-ktlint-35391a1a162f)

![](img/83249aeea72aacf4ce2ad97289a72872.png)

自从 Pinterest 的 Android 代码库成为 Kotlin 的优先事项以来,已经过去了将近一年,自从我们采用了 Kotlin 的格式和 lint 的[ktlint](https://ktlint.github.io/)以来,已经过去了将近两年。今天,我们宣布 Pinterest 已正式接管该项目。

最初,我们选择 ktlint 是因为它的简单性,活跃的社区,可扩展性和非常敏感的所有者。它还可以轻松地与我们当前基于[Phabricator](https://www.phacility.com/phabricator/)的工作流程集成,并且在我们的[Arcanist](https://secure.phabricator.com/book/phabricator/article/arcanist/)库中添加了大约 65 行 PHP 之后,我们能够将 linting 和格式应用于我们的 Kotlin 文件。

当 [Stanley Shyiko](https://github.com/shyiko) (ktlint 的开发者)几个月前打电话给我们寻找新的项目所有者时,我们立即感兴趣。它不仅是我们每天在 Pinterest 上使用的工具,也是我们回馈 Kotlin 社区的有形方式。经过与斯坦利的一些会议,很明显他完全适合,我们很高兴继续作为业主。

在短期内,项目的执行和维护方式不会发生任何变化。我们仍然欢迎并鼓励外部以[问题](https://github.com/pinterest/ktlint/issues/new)和[验证请求](https://github.com/pinterest/ktlint/pulls/new)的形式对项目做出贡献。从中长期来看,我们计划遵循 Stanley 提出的路线图,实施一种全局禁用规则的方法(ktlint 的 [最受欢迎的功能](https://github.com/pinterest/ktlint/issues/208) ),整合官方的 Gradle 插件,并更新一些 ktlint 核心 API 以允许更清晰的规则编写。如果您觉得这些项目中的任何一个感兴趣,请联系或打开验证请求。最后,如果您或您的公司使用 ktlint,请打开一个验证请求,将您添加到[采用者列表](https://github.com/pinterest/ktlint/blob/master/ADOPTERS.md)。

我们致力于继续斯坦利开始的伟大工作,并与不断增长的 Kotlin 社区中的其他开发人员合作。

*感谢 Garrett Moon 和 Jon Parise 协助将 ktlint 带到 Pinterest,以及来自 Google 的 Kevin Bierhoff、Beth Cutler 和 James Lau 提供技术意见。(T9 )*

我们正在建造世界上第一个视觉发现引擎。全球有超过 4.75 亿人使用 Pinterest 来梦想,计划和准备他们想要做的事情。[*加入我们的团队!*](https://careers.pinterest.com/careers)