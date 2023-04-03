# 我的 Python 世界之旅

> 原文：<https://medium.com/capital-one-tech/my-journey-into-the-world-of-python-f1b9af36ee09?source=collection_archive---------0----------------------->

## *Python 如何帮助我自动化 Capital One 开源办公室的任务*

![](img/8521686c19dd008d593d73aee8685578.png)

大约三年前，我有机会成为第一资本公司开源办公室(OSO)的首批创始成员之一。开源办公室推动 Capital One 的开源战略，定义安全采用和贡献于开源项目的政策和程序。自从我加入这个小组以来，在我们帮助 Capital One 拥抱开源的努力中，一直不乏令人兴奋的挑战！让我告诉你一个故事。

加入开源办公室后，我开始以一种更广泛、更有目的的方式为我的工作寻找开源解决方案。虽然我以前接触过 Python，但随着我进入开源之旅，我对这种语言和社区的欣赏与日俱增。

![](img/8d845457521cc799cf882f95a930dffc.png)

# 早期

在我作为开发人员的早期，在使用 PowerBuilder & Visual Basic 进行客户机服务器开发之前，我用 Fortran 卡打孔，用 C 语言写了一页又一页的代码。我特别喜欢从事数据库方面的工作，并有机会使用几个在 20 世纪 90 年代和 21 世纪初流行的 RDBMSs。

![](img/76ab94d171c1d17bf517307a8bb0d4aa.png)

# 2013

在 2013 年，我有机会在使用 Splunk 创建监控仪表板的过程中使用 Python SDK。总之，我花了一个为期三天的周末来学习基本的 Python 语言结构和 SDK 方法，以制作一份精心制作的 web 报告。Python 的易用性(在 Unix 操作系统系统调用的帮助下)让我感觉自己已经从使用 dll 和注册表设置的湿热赤道来到了加勒比海的凉爽微风中。直到今天，我还在继续利用 Python 来自动化我的团队手工完成的服务。

![](img/0a66aa9d3d225ce2129628cee32e3c39.png)

# 2015

这让我们来到了 2015 年。2015 年，我在开源办公室的第一个任务是开发一个执行仪表板，提供关于[卫生站](https://github.com/capitalone/Hygieia)成功的每日报告。Hygieia 是 Capital One 的第一个主要开源项目，我们很高兴能够跟踪它在开源和 DevOps 社区中的进展。(这是一个 DevOps 仪表板工具，你应该在[我们的网站](https://www.capitalone.com/tech/solutions/hygieia)上查看一下。)

为此，我必须从 GitHub 获取与项目使用相关的指标。整理这份报告对我来说是扩展 Python 技能和利用 AWS 的绝佳机会。我最近完成了一个项目，让我等了几天才让 6 台 VMWare 机器运行起来，迁移到 AWS 并在几秒钟内运行一个 EC2 实例和一个 PostgreSQL 数据库的乐趣是无法估量的。

我用 Python 设计了一个简单的解决方案来收集关于 issues、PRs、forks 等的 Github repo 级别的细节。该程序使用请求包来调用 Github APIs。然后，数据被推入 AWS 中的 PostgreSQL 数据库。使用自动化程序获取数据使得每天或者有时甚至一天多次生成执行仪表板变得容易。

以下是一些相关的决定:

*   Capital One 已经接受了公共云。所以，我决定使用 RedHat Linux EC2，因为我使用的是 Mac，操作系统与 Linux 相似。安装所需的 Python 包既快速又简单。
*   我搜索一个数据库驱动来与 PostgreSQL 对话相对容易，因为 Python 有一个流行的库，即[pypi.org](https://urldefense.proofpoint.com/v2/url?u=http-3A__pypi.org&d=DwMFaQ&c=pLULRYW__RtkwsQUPxJVDGboCTdgji3AcHNJU0BpTJE&r=c1NXQ1oSIoFCnptKvDsux6CJkC3eQ6GcmtN29CL4bQ0&m=WduhHssH2pZYN_4w8w2oWsVQxgjHBrflRtoOHDoZIq0&s=SAAXeAOWN2Hbbd65Fjhe9OYyqGomdW1RaUQz4FpFNR4&e=)。虽然 psycopg2 是一个流行的选择，但由于 psycopg2 使用的许可证，我选择了 pg8000。在我加入开源办公室之前，我一直在使用开源软件，几乎不知道不同类型的[开源许可](https://developer.capitalone.com/blog-post/dr-strangecode-or-how-i-learned-to-stop-worrying-and-love-the-license/)。但是在开源办公室帮助我理解许可和左版权之间的区别，以及我应该在这个项目中使用哪一个。
*   PostgreSQL 对我来说是新的，但是除了稍微有点混乱的用户角色和组设置之外，它不需要太多时间就可以开始使用数据库。我对 JSON 包的维护者感激不尽。浏览 GitHub API 的结果集并只获得我需要的信息是轻而易举的事情。
*   在选择不同组件时，我面临的最大挑战是为请求包建立连接，以便调用 GitHub API。我努力理解跳过代理和 SSL 验证的方法。再一次，请求包上的[文档，以及社区发布的问题和解决方案，帮助我选择方法并编写调用代码。](http://docs.python-requests.org/en/master/)

创建这份报告是我拥抱 Python 之旅中的一个重要里程碑。我意识到我可以使用 [pyGithub](https://github.com/PyGithub/PyGithub) ，但是我的代码提供了获取数据所需的灵活性和速度。此外，当我在 Capital One 开发仪表板来跟踪一些内部项目的成功时，我重用了这些包。

![](img/de5d94b89663e4cbee311ab1b581817d.png)

# 2019

当我回顾 2015 年的那四个星期时，我从一个在笔记本电脑上运行几行代码的 Python 程序员新手变成了一个在 AWS 上运行的完整的小程序，我觉得这是我作为一名使用 Python 的开发人员的旅程中重要的一步。

我喜欢 Python 的地方在于它的简单性和开始交付解决方案的短学习曲线。借助丰富的帮助资料和开放源码包的深度，Python 对于寻求自动化任务的新手和熟练程序员来说都是一个分水岭。Python 社区的禅宗传统鼓励简单性和可读性，这让我很容易理解其他开源代码，并鼓励我更多地使用它。

Python 有一些用例，它的轻量级提供了巨大的 ROI。除了复杂性之外，Python 与其他几种语言的集成能力使得它对新手和专家程序员同样具有吸引力。

在过去的四年里，我非常高兴地讲述了我进入 Python 世界的故事，以及为什么 Python 社区是做好开源的一个很好的例子。Python 让我觉得我可以通过阅读和研究达到我的目标，而不需要昂贵的培训。社区的支持是如此巨大，以至于我总觉得有成百上千的人在那里提供帮助。我很高兴用 Python 写作！我期待着在未来的许多年里用它来写作！

## 有关系的

*   [在监管环境下开源](/capital-one-tech/open-source-in-a-regulated-environment-dc4b4d9af3f8)
*   [采用企业开源思维](/capital-one-tech/adopting-an-enterprise-open-source-mindset-at-capital-one-b2a7624e27da)
*   [启动开源项目的具体细节](/capital-one-tech/nuts-bolts-of-launching-an-open-source-project-4da584c053f7)

*披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2019 首都一。*