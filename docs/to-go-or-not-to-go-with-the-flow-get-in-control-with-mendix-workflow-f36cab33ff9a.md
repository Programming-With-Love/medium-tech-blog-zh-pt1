# 随波逐流还是随波逐流？掌控 Mendix 工作流程！

> 原文：<https://medium.com/mendix/to-go-or-not-to-go-with-the-flow-get-in-control-with-mendix-workflow-f36cab33ff9a?source=collection_archive---------6----------------------->

![](img/e30484ac0dffa2322053954f876100f4.png)

# 我们生活的所有领域都在经历转变:企业正在走向数字化，客户正在习惯于通过互联网、使用应用程序和网站安排许多事情。一切都变得相互关联。如果你已经可以从一个应用程序管理不同的银行账户，那么整合其他服务的需求也会增加，不仅仅是金融服务。与其分别进入各种应用程序，您可能希望使用一个应用程序或一个门户网站来无缝访问您生活的各个方面。通过一个入口点访问数十或数百个应用程序:听起来很神奇？！mendix——低代码领域的领导者——已经迈出了重要的一步，通过工作流实现组织间和组织内的流程自动化。对工作流和你能用它做什么感兴趣？继续读下去！

## **什么是工作流？**

工作流这个术语并不新鲜。这是一个你在任何商业或 It 相关教育中学到的概念。我也是，也许我比别人钻研得更深一点，成为了这方面的专家。我是在流程采矿教父威尔·范德阿尔斯特教授的指导下完成博士学位的。(我将在以后的文章中写关于流程挖掘的内容。)在这篇文章中，我想分享一些基本概念，这些概念将有助于您将工作流置于其他术语之中，以及为什么我认为 Mendix 在采取这一步骤方面表现出色！

**过程模型和过程实施之间有什么区别？**

有许多信息系统使用选定的过程建模符号来指定业务过程。其中一些系统还允许制定业务流程。简单地说，你可以画一个图表，首先 Alice 发送一个请求，然后 Bob 处理这个请求。这是一个非常简单的业务流程，只包含两个任务，在这种情况下，工作从 Alice 流向 Bob。

![](img/f3093dd34d681936c8d8e1d39a5493d2.png)

Example of a simple business process

假设你有一个页面上有两个按钮的应用程序。通过点击 Create Request 按钮，您将得到一个用于填写请求的表单。通过点击 other Process Request 按钮，您将得到一个包含请求信息和一些附加字段的表单。在一个页面上有这两个按钮允许你以任意的顺序按下它们。但这不是我们想要的，对吗？您希望强制执行这些任务的特定顺序:首先需要创建一个请求，然后才应该处理它。当然，您可以考虑使用变量、微流、条件可见性等方法来实现这种因果关系。事实上，你应该以一种可见的方式，但是在一个更高的抽象层次上，来捕捉这个逻辑。

您绘制该图的方式，或者用业务术语来说，您对该业务流程建模的方式，在很大程度上取决于为可视化目的而选择的流程建模符号。这样的流程图为在主要或次要流程中执行的任务提供了参考。主要流程仅包括旨在交付价值的任务(如食品、车辆或设备生产)，次要流程以所有可能的方式支持主要流程以实现这一目标(想想:IT、人力资源、财务、设施等)。).流程模型的目的是提供指导，说明需要哪些任务，以及为了交付特定的产品或服务，必须以何种顺序执行这些任务。

此外，流程图为您提供了有关任务执行中所涉及的资源的信息。在所考虑的例子中，Alice 和 Bob 是人力资源。通常，有人负责任务的执行，也有人对此负责。Alice 和 Bob 分别负责执行他们的任务:创建请求和处理请求。这些任务的流程实际上是组织中的工作流程，这就是我们所说的工作流。

![](img/6dce5a2e86ec59d3ed0aa6627062ee68.png)

[https://bit.ly/MXW21](https://bit.ly/MXW21)

**自动化还是不自动化**

当一个工作流程没有制定出来时，人们会试图遵循规定的流程。但是在现实生活中，它们会偏离、走捷径，并以不同的方式解释每一步，从而导致一个不一致的过程，在不同的情况下表现不同。您不希望以不同的方式对待客户，每个客户都是国王，因此确保业务流程和自动化的一致性是必由之路。

**跨组织的工作流，具有共同而独特的 IT 环境**

因此，如果一个过程模型仅仅是一个允许所有这些偏差的参考，那么您如何能够加强控制并确保任务按照预想的特定顺序执行呢？提供这种业务流程自动化的信息系统有一个自动化引擎，它制定工作并将工作项目从一个人发送到另一个人。当使用这样的工作流系统时，流程中涉及的每个人都需要使用它。

![](img/66102e3d0353a433e59a52fa10e4cc5b.png)

Collaboration is easy when interorganizational data is exchanged via a common workflow system

如果**公司 A** 需要与**公司 B** 合作怎么办？你能预料到哪些含义？要么两家公司需要使用相同的应用程序，要么需要在不同的工作流系统之间实现一个接口，以确保所有需要的输入和输出都以正确的格式进行交换。这种界面针对这些特定的工作流系统进行了微调。然而，它增加了工艺链的刚性，降低了更换的灵活性。如果**公司 A** 决定更换他们目前的**工作流程系统 A** ，是否会中断与**公司 B** 的合作并影响信息流动？十之八九，答案是肯定的——除非您确保每个公司的业务逻辑都以灵活、可伸缩的方式实现，并且它们之间存在某种形式的治理。

![](img/a03af0251f01ea801bb102d5f0e3754d.png)

Interaction between distinct workflow systems is only possible via an intermediate interface

## **如何启用应用间监管**

这就是 **Mendix** 出现的地方！任何公司都可以通过在 Mendix 应用程序中创建工作流来明确规定哪些任务需要执行，由谁执行，以及以什么顺序执行。如果一个给定的应用程序需要来自另一个应用程序的一些数据，可以从 **Mendix 数据中心提供的数据目录中使用。**

![](img/d7a87c50dac54f82aea3ee6e0f5489ef.png)

Example of intra-app governance made available using Mendix Workflow and Mendix Data Hub

当提供服务时，数据需要向另一家公司公开( **Mendix Data Hub** 也将用于此目的)。 **Mendix 工作流**反过来允许您指定总体流程，规定各种应用程序相互通信的顺序。 **Mendix Workflow** ，结合 **Mendix Data Hub** ，使您能够安排流程驱动和以数据为中心的治理，确保灵活性、透明性和可扩展性。这完全符合**面向服务的架构**，在那里你可以轻松地插入、拔出和替换应用程序，只要它们提供工作流所需的数据。**这实现了你做梦也想不到的应用间管理！**

## 阅读更多

[](https://bit.ly/MXW21) [## Mendix World 2021 |召集您的应用开发团队 2021 年 9 月 7 日至 9 日

### 好像你需要说服…在一个全球制造商社区，他们想通过探索什么来相互学习…

bit.ly](https://bit.ly/MXW21) [](https://www.mendix.com/mendix-world/tracks/) [## 曲目|门迪克斯世界 2021

### 在今年 Mendix World 开幕之前，手工制作您的议程。浏览专为您量身定制的 8 个专题讲座中的 85 个以上专题讲座…

www.mendix.com](https://www.mendix.com/mendix-world/tracks/) 

*   [https://docs.mendix.com/studio/workflows](https://docs.mendix.com/studio/workflows)
*   [https://www.mendix.com/workflow/](https://www.mendix.com/workflow/)
*   [https://www . mendix . com/live/optimize-your-production-process-with-workflow/](https://www.mendix.com/live/optimize-your-production-process-with-workflow/)

*来自发布者-*

*如果你喜欢这篇文章，你可以在我们的* [*媒体页面*](https://medium.com/mendix) *或我们自己的* [*社区博客网站*](https://developers.mendix.com/community-blog/) *找到更多类似的文章。*

*希望入门的创客，可以注册一个* [*免费账号*](https://signup.mendix.com/link/signup/?source=direct) *，通过我们的* [*学苑*](https://academy.mendix.com/link/home) *即时获取学习。*

有兴趣更多地参与我们的社区吗？你可以加入我们的 [*Slack 社区频道*](https://join.slack.com/t/mendixcommunity/shared_invite/zt-hwhwkcxu-~59ywyjqHlUHXmrw5heqpQ) *或者想更多参与的人，看看加入我们的* [*遇见 ups*](https://developers.mendix.com/meetups/#meetupsNearYou) *。*