# AWS Elastic Beanstalk —使用 Beanstalk 部署应用程序

> 原文：<https://medium.com/edureka/aws-elastic-beanstalk-647ae1d35e2?source=collection_archive---------2----------------------->

![](img/b7f1379408cab12c2c9d91fe7165117f.png)

AWS Elastic Beanstalk - Edureka

云计算不再处于初级阶段。它现在已经非常成熟，并作为一个创新平台，允许公司实现在传统基础设施上不可能实现的应用程序。这一成功伴随着云计算服务的指数级增长，PaaS 就是其中之一。亚马逊推出了遵循 PaaS 模式的自有服务，即 **AWS** **Elastic Beanstalk！**

让我们来看看这篇 AWS Beanstalk 文章中涉及的主题:

1.  亚马逊弹力豆茎是什么？
2.  AWS 弹性豆茎的好处
3.  AWS 弹性豆茎组件
4.  AWS 弹性豆茎架构
5.  演示-在 Beanstalk 上部署应用程序

# 亚马逊弹力豆茎是什么？

![](img/b446c1d6d34fceea2060465424b0abca.png)

云计算正在重塑整个应用程序开发流程。包括 Amazon Web Services 和 Microsoft Azure 在内的许多云供应商都提供了开发工具，以帮助使这一过程更加简单和安全。AWS Elastic Beanstalk 就是这样一个基于 PaaS 模型实现的开发工具。

*AWS Elastic Beanstalk 是一个易于使用的服务，用于部署和扩展用 Java 开发的 web 应用程序和服务。NET、PHP、Node.js、Python、Ruby、Go、Docker 在 Apache、Nginx、Passenger、IIS 等熟悉的服务器上。*

使用 AWS Elastic Beanstalk，开发人员可以在不提供底层基础设施的情况下部署应用程序，同时保持高可用性。

但是，当我们已经有许多其他平台时，为什么还要选择弹性豆茎呢？所以，我们来讨论一下弹性豆茎的好处。

# AWS 弹性豆茎的好处

以下是 AWS Elastic Beanstalk 相对于其他 PaaS 服务的一些优势

**提供更快的部署:**

![](img/72f8f6b4fb8e63526f4e69f71e49f552.png)

Elastic Beanstalk 为开发人员提供了部署应用程序的最快和最简单的方法。几分钟之内，应用程序就可以使用，用户无需处理底层基础设施或资源配置。

**支持多租户架构:**

![](img/aac02c2f19a5c34d271fc26c8f1914b6.png)

AWS Elastic Beanstalk 使用户可以在不同的设备上共享他们的应用程序，并且具有高可伸缩性和安全性。它提供了应用程序使用和用户配置文件的详细报告。

**简化操作:**

![](img/f505901790ede0b35a18fa1c86b3d667.png)

Beanstalk 提供和操作基础设施，并管理应用程序堆栈。开发人员必须专注于开发应用程序代码，而不是花时间管理和配置服务器、数据库、防火墙和网络。

**提供完整的资源控制:**

![](img/482d00d93b36588adbfbb5ee81b0c6c5.png)

Beanstalk 让开发人员可以自由地选择 AWS 资源，如 *EC2 实例*类型，这对于他们的应用程序来说是最佳的。它允许开发人员保持对 AWS 资源的完全控制，并随时访问它们。

既然我们有充分的理由相信为什么 AWS Elastic Beanstalk 会受到开发人员的青睐，那么让我们来看看它的基本概念。

# AWS 弹性豆茎组件

在 Beanstalk 上部署应用程序时，您会经常遇到一些关键概念。让我们看看这些概念:

# 应用:

*   Elastic Beanstalk 中的应用程序在概念上类似于文件夹
*   应用是包括 ***环境、版本*** 和 ***环境配置*** 的组件的集合

# 应用程序版本:

*   一个应用版本指的是一个 web 应用的可部署代码的特定的、带标签的迭代。
*   一个应用程序版本指向一个包含可部署代码(如 Java WAR 文件)的亚马逊 S3 对象

# 环境:

*   Elastic Beanstalk 应用程序中的环境是当前版本的应用程序将被激活的地方
*   每个环境一次只能运行一个应用程序版本。但是在许多环境中同时运行相同或不同版本的应用程序是可能的

# 环境层:

根据需求，beanstalk 提供了两个不同的环境层:Web 服务器环境、工作环境

*   Web 服务器环境:处理来自客户端的 HTTP 请求
*   工作环境:处理耗费资源和时间的后台任务

下面的图示显示了应用程序、应用程序版本和环境之间的相互关系:

![](img/bce4f6742b6153a5aee65094205c5a37.png)

下面是使用默认容器类型的 Beanstalk 环境的样子:

![](img/0333e098b73cfa217c4e513ada9c5380.png)

现在您已经了解了与 Elastic Beanstalk 相关的不同的关键概念，让我们来理解 Elastic Beanstalk 的架构。

**AWS 弹性豆茎架构**

在进入 AWS Elastic Beanstalk 架构之前，我们先来回答一个最常见的问题，

# 什么是弹性豆茎环境？

环境是指应用程序的当前版本。当您为您的应用程序启动一个环境时，Beanstalk 会要求您在两个不同的环境层中进行选择，即 Web *服务器环境*或*工作环境*。让我们逐一了解。

## Web 服务器环境

安装在 Web 服务器环境中的应用程序版本处理来自客户端的 HTTP 请求。下图说明了一个 Web 服务器环境层的示例 AWS Elastic Beanstalk 体系结构，并显示了该类型环境层中的组件如何协同工作。

**Beanstalk 环境** —环境是应用程序的核心。当您启动一个环境时，Beanstalk 会分配成功运行应用程序所需的各种资源。

**弹性负载均衡器** —当应用程序收到来自客户端的多个请求时，Amazon Route53 会将这些请求转发给弹性负载均衡器。负载平衡器在自动伸缩组的 EC2 实例之间分发请求。

**自动缩放组** —自动缩放组自动启动额外的 Amazon EC2 实例，以适应应用程序上不断增加的负载。如果应用程序的负载减少，Amazon EC2 自动伸缩会停止实例，但总是会让至少一个实例运行。

**主机管理器** —它是一个软件组件，运行在分配给应用的每个 EC2 实例上。主机经理负责各种事情，例如

*   生成和监控应用程序日志文件
*   生成实例级事件
*   监控应用服务器

**安全组** —安全组就像您实例的防火墙。Elastic Beanstalk 有一个默认的安全组，它允许客户端使用 HTTP 端口 80 访问应用程序。它还为您提供了一个选项，您可以在其中为数据库服务器定义安全组。下图总结了我们对 Web 服务器环境的了解。

![](img/a32442bdc7e249cbdf15aa6808e7b6c7.png)

这就是关于 Web 服务器环境的全部内容。但是，如果安装在 Web 服务器层上的应用程序版本因为在处理请求时遇到了时间密集和消耗资源的任务而一直拒绝多个请求，该怎么办呢？嗯，这就是工人层的用武之地。

## 工人环境

工作进程是一个独立的后台进程，通过处理资源密集型或时间密集型操作来协助 Web 服务器层。此外，它还通过电子邮件发送通知、生成报告和清理数据库。这使得应用程序能够保持响应并处理多个请求。

这很好，但是 Worker process 如何知道在什么时候处理哪些任务呢？这两个环境层如何通信？为此，我们使用 AWS 的消息队列服务，称为亚马逊简单队列服务(SQS)。下图让您大致了解了工作进程如何接收和处理后台任务。

工作进程的工作流相当简单。当您启动一个工作环境层时，Elastic Beanstalk 会在 Auto Scaling 组中的每个 EC2 实例上安装一个守护进程。这个守护进程从亚马逊 SQS 队列中提取请求。基于队列的优先级，SQS 将通过一个`POST`请求将消息发送到工作环境的 HTTP 路径。收到消息的工人执行任务，并在操作完成后发送 HTTP 响应。SQS 在收到响应消息时删除队列中的消息。如果未能收到响应，它将继续重试发送消息。

![](img/488aea6c89405374a76842a4a45e7db9.png)

现在我们已经从理论上了解了 Elastic Beanstalk，在这篇博客的剩余部分，我们将了解如何在 Elastic Beanstalk 上部署应用程序。

# 在 Elastic Beanstalk 上部署应用程序

在 Elastic Beanstalk 上部署应用程序是一个相当简单的过程。让我们看看如何逐步部署应用程序。

**步骤 1:** 在弹性豆茎控制台上点击*创建新应用*选项。将出现一个对话框，您可以在其中为应用程序提供名称和适当的描述。

![](img/9513ad347e23f1cc36cbc1239a5a5f64.png)

**步骤 2:** 现在已经创建了应用程序文件夹，您可以点击*动作选项卡*并选择*创建环境*选项。Beanstalk 为您提供了一个选项，您可以在其中为您的应用程序创建多个环境。

![](img/7441d70d2b5b0409a86badcbb6cf463c.png)

**步骤 3:** 在两个不同的环境层选项中进行选择。如果希望应用程序处理 HTTP 请求，请选择 Web 服务器环境，或者选择 Worker 环境来处理后台任务。

![](img/f56362577c7922fa74d1707b89560e4a.png)

**步骤 4:** 出现另一个对话框，您需要为您的应用程序提供域名和描述。

![](img/db1cc6833f3413c0586ce5245e46128c.png)

**第五步:**为您的应用选择一个平台。弹性豆茎将为您提供多种选择。您可以选择 Beanstalk 提供的示例应用程序，或者上传包含应用程序代码的文件。

![](img/5cd7bb999587029a24951c895db1dd01.png)

Beanstalk 将需要几分钟来启动一个环境。环境启动后，在导航窗格中可以看到多个选项，在这些选项中可以更改应用程序的配置、查看日志文件和事件。因为您已经在环境页面上，所以尝试探索 Beanstalk 提供的不同特性。

![](img/baca46ffa0455dbcc89f0132cc258f9b.png)![](img/c44696025917662bf9a1bbfe5d97ba07.png)

**第六步:**在右上角，你会找到你的应用版本的网址。点击那个网址。您将被带到一个页面，该页面将确认您已经在 Elastic Beanstalk 上成功启动了您的应用程序。

![](img/a50a4f07fe301d3d969907ae620f8e2f.png)

**恭喜你！**您已经在 Elastic Beanstalk 平台上成功部署了一个应用程序。

我希望现在您对 Elastic Beanstalk 以及如何使用 Beanstalk 部署您的应用程序有了一个清晰的了解。

原来就是这样！我希望这篇博客能给你提供信息，增加你的知识。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释 AWS 的各个方面。

> *1。* [*AWS 教程*](/edureka/amazon-aws-tutorial-4af6fefa9941)
> 
> *2。* [*AWS EC2*](/edureka/aws-ec2-tutorial-16583cc7798e)
> 
> *3。*[*AWS Lambda*](/edureka/aws-lambda-tutorial-cadd47fbd39b)
> 
> *4。* [*AWS 简历*](/edureka/aws-resume-7453d9477c74)
> 
> *5。* [*AWS S3*](/edureka/s3-aws-amazon-simple-storage-service-aa71c664b465)
> 
> *6。* [*AWS 控制台*](/edureka/aws-console-fd768626c7d4)
> 
> *7。* [*AWS RDS*](/edureka/rds-aws-tutorial-for-aws-solution-architects-eec7217774dd)
> 
> *8。* [*AWS 迁移*](/edureka/aws-migration-e701057f48fe)
> 
> *9。*[*AWS Fargate*](/edureka/aws-fargate-85a0e256cb03)
> 
> *10。* [*亚马逊 Lex*](/edureka/how-to-develop-a-chat-bot-using-amazon-lex-a570beac969e)
> 
> *11。* [*亚马逊光帆*](/edureka/amazon-lightsail-tutorial-c2ccc800c4b7)
> 
> *12。* [*AWS 定价*](/edureka/aws-pricing-91e1137280a9)
> 
> *13。* [*亚马逊雅典娜*](/edureka/amazon-athena-tutorial-c7583053495f)
> 
> *14。* [*AWS CLI*](/edureka/aws-cli-9614bf69292d)
> 
> *15。* [*亚马逊 VPC 教程*](/edureka/amazon-vpc-tutorial-45b7467bcf1d)
> 
> *15。*[*AWS vs Azure*](/edureka/aws-vs-azure-1a882339f127)
> 
> *17。* [*内部部署 vs 云计算*](/edureka/on-premise-vs-cloud-computing-f9aee3b05f50)
> 
> *18。* [*亚马逊迪纳摩 DB 教程*](/edureka/amazon-dynamodb-tutorial-74d032bde759)
> 
> *19。* [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> 20。 [*AWS 代码提交*](/edureka/aws-codecommit-31ef5a801fcf)
> 
> *21。* [*顶级 AWS 架构师面试问题*](/edureka/aws-architect-interview-questions-5bb705c6b660)
> 
> *22。* [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> 23。 [*使用 AWS 创建网站*](/edureka/create-websites-using-aws-1577a255ea36)
> 
> *二十四。* [*亚马逊路线 53*](/edureka/amazon-route-53-c22c470c22f1)
> 
> *二十五。* [*用 AWS WAF 保护 Web 应用*](/edureka/secure-web-applications-with-aws-waf-cf0a543fd0ab)

*原载于 2018 年 10 月 23 日 www.edureka.co*[](https://www.edureka.co/blog/aws-elastic-beanstalk)**。**