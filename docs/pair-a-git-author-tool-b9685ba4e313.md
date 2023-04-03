# pair，一个 git 创作工具

> 原文：<https://medium.com/square-corner-blog/pair-a-git-author-tool-b9685ba4e313?source=collection_archive---------3----------------------->

## 简化结对编程中的作者身份。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

*由* [撰写*布莱恩多诺万*](https://medium.com/u/c9cfa64b9d05?source=post_page-----b9685ba4e313--------------------------------) *。*

Square 的软件工程师有时候自己写代码，有时候和别人一起写。当我们在同一台计算机上与其他人一起编写代码时，我们称之为“[结对编程](https://en.wikipedia.org/wiki/Pair_programming)或“结对”。我们在采访中[使用这种技巧](https://corner.squareup.com/2015/12/ace-the-square-pairing-interview.html)，并在适当的时候在工作中使用。当我们准备在我们的版本控制系统 git 中提交我们的工作时，我们希望确保为后代列出所有的作者。

当我开始的时候，我们有几种不同的方法来解决这个问题。有些人使用 ruby gem，有些人为此编写了自己的工具，有些人每次都只是手工编辑他们的 git author 配置。我想要一种方法来快速扩展名称，并为提交的所有作者创建一个联合电子邮件别名，并且我不希望它只在我当前的 ruby 和 rvm 配置下工作。

我决定在 Go 中编写 [pair](https://github.com/square/pair) 作为一种自学语言的方式，以确保它快速可靠。它默认安装在 Square 的所有机器上，包括我们的面试机器。这对于任何有相当稳定的人员列表的团队来说都是非常好的，比如公司的工程团队或者做结对编程的开源项目。你可以这样使用它:

```
$ pair mb lb
Lindsay Bluth and Michael Bluth <git+lb+mb@example.com>
```

对任意数量的作者使用它:

```
$ pair l c m
Curly and Larry and Moe <git+c+l+m@example.com>
```

您可以使用它将 git 作者配置恢复到正常状态:

```
$ pair lb
Lindsay Bluth <lb@example.com>
```

位于~/的 YAML 文件。pairs 将用户名映射到全名:

```
c: Curly
l: Larry
lb: Lindsay Bluth
m: Moe
mb: Michael Bluth
```

这个简单的工具在 Square 上经常使用，我希望它对你也有用。欲了解更多信息或将其安装在您的机器上，请查看 GitHub 上的[。](https://github.com/square/pair)

[](/@eventualbuddha) [## 布莱恩·多诺万-简介

### 我为@Square 制作数字产品，主要是网站。

medium.com](/@eventualbuddha)