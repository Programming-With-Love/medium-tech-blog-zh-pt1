# 模板如何帮助留住用户

> 原文：<https://medium.com/capital-one-tech/how-templates-can-help-onboard-and-keep-your-users-c1636f48ddc3?source=collection_archive---------5----------------------->

## 好的文档和快乐的开发人员就像花生酱和果冻一样

![](img/b9a3bf55a64cf85ef5326393f613139f.png)

我四年级的时候，老师让我告诉他如何做花生酱和果冻三明治。

我说，“*这么容易！你只要把花生酱和果冻涂在面包上！”*

他拿起罐子，把它们放在面包上面。*“不！”我惊恐地说。*“不是那样的！”**

为了吃到三明治(我妈妈绝不会买那种含糖的花生酱和果冻)，我必须精确地描述制作三明治的细节——需要什么工具，每片面包应该放在哪个方向——直到最后一个“普通的”花生酱和果冻三明治被制作出来。

# 花生酱和文件

![](img/4ae4be30c5d5704b0299b3601e8f81ff.png)

许多(如果不是大多数)技术文档停留在*的水平上，“就这么做吧！”关于术语的含义、已经采取的步骤、如何建立本地环境以及读者的技术水平，有一层又一层的假设。结果往往是混乱或不完整的指令，导致额外的支持时间和浪费开发人员的时间。*

现实情况是，技术用户来自完全不同的背景和知识水平，为了使文档有效， ***任何*** 用户都应该能够跟踪您的入职流程。即使是严格为公司内部使用的文档，也常常无法满足这一要求，因为这些文档可能采用了一些共享的术语或设置。

当你的文档说*“把花生酱涂在面包上”*你的用户可能不知道他们需要一把黄油刀、一个盘子和拧开盖子的能力。这些用户可能试图用他们的手指涂抹花生酱，或者用链锯打开盖子。他们可能会感到恼火，不再尝试做三明治，或者他们可能会在您的支持频道上发布简单、重复或不必要的问题:

“我怎么才能把花生酱从有盖子的罐子里取出来呢？”

*“两种浇头都需要用一把刀，还是每种浇头都需要一把刀？”*

“你支持果酱和果冻吗？”

“我怎样才能把花生酱从我的电锯刀片上弄下来？”

作为一名软件工程师，我既要和其他工程师一起为我服务，也要和无数其他服务一起工作，我有一个非常简单的解决方案来节省我们所有人的时间和精力。它是免费的，与语言无关，并且您可能已经在代码库的基础上埋下了一个。

这个神奇的解决方法是什么？

**让你的用户成为你服务的所有界面的模板。**模板显示而不是告诉你如何使用你的服务，并且更有可能向你的用户传达未被认可的假设。

# 什么是模板？

[模板](https://en.wikipedia.org/wiki/Template)这个词可以指代任何数量的物体，从缝纫图案到预先设计好但空无一物的幻灯片，它们甚至存在于自然界的 [DNA](https://www.nature.com/scitable/topicpage/dna-transcription-426/) 中。在所有情况下，模板传达一种预设格式，以消除不必要的重复。在这种情况下，模板是文档中的代码块，用户可以复制、粘贴和定制这些代码块，以用作加入您的服务的清晰模型。

最简单的形式是，软件模板是一个明确的协议。如果我给你一个我如何期待你的三明治请求的模板，你应该能够填写它并返回给我以得到你想要的三明治。

假设我给你一个 *sandwich.yml* 文件，看起来像这样:

```
# Ariel's Sandwich Maker Service: Hot Dogs Accepted 
# Supported bread and filling types can be found [here] 
name: my_example_pbj_sandwich 
bread_type: white 
fillings: 
-	peanut_butter 
-	strawberry_jelly 
crust: on # optional, default: `on`, options: `off` 
cut: diagonal # optional, default: `parallel`, options: `diagonal` served_on: white_plate # optional, default: `white_plate`, options: `napkin`
```

这确切地告诉了我的用户我所期望的: [YAML](https://yaml.org/) 格式的[蛇案](https://en.wikipedia.org/wiki/Snake_case)字段，以特定的顺序，包括可选的参数和支持的定制。它告诉你`bread_type`接受一个参数，而`fillings`接受多个参数。它为您提供了关于三明治选择的附加文档。通过省略拧开罐子和刀具类型的选项，它告诉用户该服务将处理打开罐子和传播填充物。用户可以很容易地复制、粘贴和修改这个文件，以便在我的服务中使用。

# 保持模板的新鲜

模板和所有书面文档的一个缺点是它需要维护。如果我决定不再把热狗算作一个三明治，而是把它分拆成一个独立的服务，我需要更新我的文档来说明这一点。但是说实话:文档一写出来就开始变得陈旧了。

代码库维护者有两种策略:

1.  利用 Github 问题、来自用户或人员支持热线的反馈，或者手工测试，定期(比如每季度)留出时间来确认文档符合当前的需求；和/或
2.  使用自动文档方法，如 [Swagger](https://swagger.io/) 、[Read Docs](https://docs.readthedocs.io/en/stable/)或 [Sphinx](https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html) 。或者[自己造](https://towardsdatascience.com/auto-docs-for-python-b545ce372e2d)！

我建议两者同时使用。

Swagger 可以检测 API 的需求，并创建一个易于使用的 UI 来定义如何使用每个端点——它期望什么，它应该返回什么。Swagger 不仅让用户可以轻松地将他们的 API 装载到 Swagger 上，还让他们的用户的用户可以轻松地理解他们的 API 需求。Swagger 基本上是一个元模板。这篇[文章](https://www.capitalone.com/tech/software-engineering/how-to-make-swagger-codegen-work-for-your-team/)带您了解如何设置 [Swagger CodeGen](https://swagger.io/docs/open-source-tools/swagger-codegen/) ，它可以自动生成文档。作为 Python [Flask](https://flask.palletsprojects.com/en/1.1.x/) 的经常用户，我已经开始使用 [Flask-RESTX](https://flask-restx.readthedocs.io/en/latest/swagger.html) ，它可以自动生成自动生成，因为最好的工作量就是不做任何工作。

# 三个模板的故事

在使用了一个非常好的模板和一个非常差的模板之后，我有了写这篇文章的灵感——还有一个模板我必须自己编写和维护。

## 好模板

作为一名负责我的产品的后端工程师，与企业和外部服务集成是我日常生活的一部分。其中一个产品，我称之为 [GrilledCheese](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) ，允许用户通过简单的 JSON 请求以编程方式管理他们的云资产。成千上万的工程师每天使用它来完成重要的工作。你可以想象，如果他们的文档缺失，他们的支持热线将会被紧急消息和沮丧的客户堵塞。幸运的是，GrilledCheese 有很好的文档，有清晰的需求描述和可复制粘贴的模板。结果是一个广泛使用的产品，节省了不可估量的开发时间。虽然开发人员肯定还有未解决的问题，但由于他们的文档全面且易于访问，基本问题被最小化了。

GrilledCheese 是一个很好的模板和文档模型，它展示了好的文档是如何让用户更满意和被更广泛地采用的。

## 错误的模板

我最近使用的另一个产品，姑且称之为 Hoagie，提供了我最喜欢的一种模板:完整的 GitHub 回购。理想情况下，我可以克隆这个回购，填补空白，并在几分钟内启动和运行。不是这样的！虽然父应用程序正在积极开发，但模板回购从未更新，并且已经落后了几个版本。大多数库都过时了，有些不再工作了，ATDD 测试失败了，代码本身也不符合服务的实际需求。

结果，有几十个问题开放，愤怒的用户一次又一次地请求回应和更新。他们的支持渠道充斥着重复的问题，回复很慢。现有用户不再推荐这项服务，现在引导新用户不要开始登机。用户更新模板的尝试受到阻碍，因为需要进行多层更新，而很少有所有者可以批准更改。

读者，不要像三明治一样。

## 正在进行的模板

当我刚开始在我目前的团队工作时，我所在部门的大多数 API 都是用 Java 编写的。(嘘！开玩笑，有人爱 Java！)我有一个非常适合 Python 的绿地项目，最后我做了肉丸子，这是近年来第一个 Python API。

当我在构建肉丸子的时候，一个同事建议为我的项目创建一个[模板 repo](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-template-repository) ，这样其他人就可以很容易地创建新的 Python APIs。对我来说，通过复制肉丸子并删除所有细节——如果你愿意，可以说是肉丸子——创建一个模板的速度快得惊人。剩下的是一个通用 Python Flask 应用程序 Sub，它已经有了一个健康检查、测试和一个虚拟端点来演示用法。我打包了所有特定于 Capital One 的需求和最佳实践，并记录了如何让它运行。在过去的一年里，我已经更新了几次，只是为了确保它仍然工作，或者澄清或简化一个用户难以理解的元素。我必须记得时不时地检查它，但是我花在维护它上的时间是最少的。

我制作 Sub 的主要收获是，为了让人们使用它，他们必须知道它的存在以及在哪里可以找到它。我需要在相关的展示中展示它，在相关的渠道中链接到它，并在我遇到一个好的用例时推荐它。如果一个模板存在但没人用，有人在乎吗？不。GitHub 博客最近有一篇关于这个可发现性问题的很棒的文章。确保你社交和宣传你的文档和模板。如果这不是你喜欢做的，看看你是否能让别人为你推销它们。

# 增加影响的文件指南

对于用户来说，模板是理解你的服务是做什么的以及如何开始使用它的最简单、最快和最有效的方法之一。与过于冗长的文档(或者更糟，没有文档)不同，模板向用户展示了服务是如何工作的，并传达了一些看起来微不足道的细节。(有多少次集成因为案例错误而在第一次尝试中没有成功？)浏览一下就可以快速展示需求和过程，并允许用户独立地确定您的产品是否适合他们的用例。

所有的软件都受制于[自然选择](https://en.wikipedia.org/wiki/Natural_selection)；一些服务找到了生存、进化和传播的方法，而另一些服务逐渐消亡并被取代。好消息是——一种进化到只吃三明治的蜥蜴只能在一个狭窄的窗口内生存或繁衍；实际上，您可以控制代码的生存或发展。

如果您希望您的代码向前发展并成倍增长，请遵循以下准则:

1.  **写文档！这是一个低门槛，但我们必须从某个地方开始。**
2.  编写团队以外的人能够理解的文档！知道你做到了的唯一方法是和你的圈子之外的人交谈。所以，去请一个陌生人阅读你的文件，告诉你它们哪里不好。
3.  **创建用户可以克隆或复制粘贴的模板！**
4.  [**使用**](https://www.process.st/software-documentation/) [**汽车**](https://wiki.python.org/moin/DocumentationTools) **-** [**文档**](/technical-writing-is-easy/tools-for-code-documentation-4fd9e8e39eed) [**工具**](https://en.wikipedia.org/wiki/Comparison_of_documentation_generators) **！**
5.  确保所有的文档和模板都链接到人们可以找到它们的地方。谈论它们，展示它们，链接它们，锁定它们，营销它们。
6.  像对待代码一样对待你的文档！在你的验收标准和 [PR 模板](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)中包含文档更新，并养成在每次相关提交时更新文档的习惯。
7.  确保您的文档是最新的！每三个月，去找一个陌生人来阅读你的文档，确保它们仍然有意义，或者倾听你的用户，并根据需要进行编辑。让新的团队成员使用您的文档，并授权他们进行更新和澄清。
8.  承认没有可理解的文档，你的代码将会灭绝，因为没有人会使用它！不要忽视它。

我一直很想听听您保持文档新鲜和低维护的解决方案——除了我在这里讨论的之外，如果您有适合您的策略，请告诉我。

*披露声明:2021 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*原载于*[*https://www.capitalone.com*](https://www.capitalone.com/tech/software-engineering/how-to-use-templates-for-better-documentation/)*。*