# 关于并发性，我希望我不知道的一切

> 原文：<https://medium.com/square-corner-blog/everything-i-wish-i-didnt-know-about-concurrency-53e9a34b449a?source=collection_archive---------1----------------------->

## 从概念和习语到战争故事和轶事

*由* [撰写*曼尼克*](https://medium.com/u/cb858ba411a5?source=post_page-----53e9a34b449a--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

作为工程师，我们所做的一切都涉及到并发性。由于[散热技术](http://www.wired.com/2008/09/battling-the-co/)、[的限制，摩尔定律](http://en.wikipedia.org/wiki/Moore%27s_law)在十多年前就达到了实际极限，计算取得进步的唯一方式就是将更多的内核塞进一个芯片，和/或使用更多的 CPU。并且[阿姆达尔定律](http://en.wikipedia.org/wiki/Amdahl%27s_law)一直统治至今。

然而，并发性无处不在，存在一大类我们传统上不认为是“并发”的系统，例如客户端和服务器之间的交互、多用户系统上运行的单线程应用程序、操作系统虚拟化、共享数据存储等等。考虑并发性对于编写正确的、可伸缩的、高性能的软件至关重要。然而，并发的成功应用是理论和实践的独特结合。

在 Square，我们最近组织了一个名为*“我希望我不知道的关于并发的一切”*的研讨会，在那里我们开始了对一些并发概念和习惯用法的简短复习。然后，我们向 Square 工程师小组提出了一系列问题，他们分享了战争故事、轶事和谜题，并谈论了他们遇到的有趣的并发应用。

您将对设计、构建和调试并发系统时应用的思维启发有所了解。无论您是在 1977 年的 Apple II 上触发了您的第一个竞赛条件，还是在本月早些时候的一个任务关键型生产系统上触发了您的第一个竞赛条件，都应该有与您相关的、有趣的内容！

# 发言者和小组成员

*   [Manik Surtani](https://twitter.com/maniksurtani) 是 Square 支付平台的工程师，主要研究可扩展性和高可用性问题。他有分布式计算的背景，是数据网格平台 [Infinispan](http://www.infinispan.org/) 的创始人。
*   Gian Perrone 是 Square 销售分析&报告团队的工程师，拥有哥本哈根 IT 大学并发理论博士学位。
*   [兰迪·威金顿对计算机的痴迷始于 1974 年，当时他加入了位于硅谷的家酿计算机俱乐部。1977 年，兰迪加入苹果电脑公司，成为他们的第一名软件工程师和第六名员工。Randy 的职业生涯跨越了许多硅谷最著名的公司，如 E*TRADE、Ebay 和 Google。Randy 是早期采用者，他于 2011 年加入 Square，并在今天为该公司的支付合作伙伴建立了支付网关。](https://twitter.com/squarenerd)
*   [约亨·贝克曼](https://twitter.com/__jochen__)是 Square Cash 的一名工程师。此前，Jochen 共同创立了基于云的电子邮件客户端 Fluent.io，并在谷歌工作。
*   塔米尔·杜伯斯坦是 Square 公司的多面手工程师。

[](/@maniksurtani) [## Manik Surtani - Profile

### SSL 和虚假的安全感我最近和一位同事谈到了他在网上预订酒店时遇到的一个问题…

medium.com](/@maniksurtani)