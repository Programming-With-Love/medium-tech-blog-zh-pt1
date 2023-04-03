# 了解如何使用 Amazon ECS 在生产中运行 Docker

> 原文：<https://medium.com/edureka/docker-container-in-production-amazon-ecs-19857ce96f25?source=collection_archive---------1----------------------->

![](img/5e9588d06ba1d9c2b247dc5f2c679f8b.png)

Amazon ECS — Edureka

我相信你听说过最近科技行业的“Docker 炒作”。Docker 使得在任何环境下运行应用程序都变得非常容易。你知道是什么让 Docker 更加高效吗？和亚马逊的 ECS 一起使用。这篇关于 Amazon ECS 的文章详细解释了如何在使用 Amazon ECS 的生产中使用 Docker。

本文议程:

*   Docker 简介
*   亚马逊 ECS 简介
*   ECS 如何工作
*   使用 ECS 的特点和优势
*   演示:使用 Amazon ECS 在生产环境中运行 Docker 映像

# Docker 简介

Docker 是一个软件开发平台。它基于集装箱化的概念。它将应用程序及其所有依赖项包装到一个容器映像中。该容器可以部署在任何平台上运行该应用程序。因此，基本上这些应用程序运行完全相同，无论它们运行在哪里或运行在什么系统上。它使用一个名为 DockerHub 的在线云存储库来存储所有这些容器图像。

# 亚马逊 ECS 简介

既然你已经理解了 Docker，那我们就来看看 ECR 吧。作为容器服务平台的一部分，AWS 提供了亚马逊 EC2 容器注册中心(Amazon ECR)。这是一个完全托管的 docker 容器注册表。

现在您有了自己的容器，您需要的只是一个托管它们的平台。这就是 ECS 发挥作用的地方。

ECS 代表弹性集装箱服务。这是一种容器管理服务。在 ECS 集群上运行、停止或管理 Docker 容器就像在公园散步一样。您可以通过几个简单的 API 调用来启动、停止或扩展任何基于容器的应用程序。

我相信你们一定听说过微服务这个术语，它现在在技术市场上非常热门。Amazon ECS 可以让您通过在微服务模型上构建复杂的应用程序架构来创建一致的部署。

![](img/cac50de2deaf6dba70b746d04712772a.png)

ECS 还跟踪您的实例及其资源。在上图中，有一个运行两个容器的请求。ECS 将寻找拥有运行这些容器的资源的实例，从注册中心下载容器，并相应地将它们部署到容器上。

# ECS 如何工作

现在您已经理解了理论概念，让我们更深入地了解一下 ECS 是如何工作的。让我们看一个非常常见的场景，假设您正在运行一个使用两个 docker 容器的应用程序。例如，一个容器包含实际的应用程序，另一个包含所有的依赖项。您需要这两者来成功运行应用程序。

Amazon ECS 由以下部分组成:

## 任务内容

任务定义基本上是描述用于运行应用程序的 docker 容器的蓝图。在我们的例子中，它将是两个 docker 容器、所用图像的细节、要分配的 CPU 内存、要声明和使用的环境变量、要公开的端口等。

## 工作

任务是任务定义的实例。它运行容器以及任务定义中提到的容器细节。需要时，单个类定义可以创建多个任务。

![](img/bf3a0badf8a619750036584d7b028098.png)

## 服务

服务定义了在任何给定点从单个任务定义运行的最小和最大任务。在我们的示例中，如果一个任务定义中有一个任务正在运行，并且 CPU 达到极限，ECS 将添加另一个任务。此时，服务将是 2，因为两个任务从一个任务定义中运行。

## ECS 容器实例和 ECS 容器代理

每个 docker 容器都将在 EC2 实例上运行。这样的 EC2 实例被称为 ECS 容器实例。

每个 ECS 容器实例都将运行一个 ECS 容器代理。该代理在实例和 ECS 之间进行通信，这有助于管理正在运行的容器，甚至添加新的容器。

## 串

我们现在有了任务定义、任务和服务。我们只需要一个平台来托管这些。这个平台就是集群。集群是一组 ECS 容器实例。一个集群可以运行许多服务。例如，您的项目中有多个应用程序，您可以在一个集群上运行其中的几个。这减少了资源的浪费，间接节省了你的钱。

![](img/d6886e02c0705b8bbb6cbc9524e00c36.png)

# 使用 ECS 的特点和优势

*   它允许您在一个区域内跨多个可用性区域以高可用性的方式运行应用程序容器
*   它允许您在没有基础设施管理的情况下使用容器。
*   您可以将任何东西都容器化，并让 ECS 来管理。
*   它是超级安全的，因为你可以将你的容器图像存储在 EC2 容器注册表中，这是非常安全的，因为你的图像在静止时是加密的。
*   ECS 的另一个令人惊奇的特性是，它让 IAM 角色在每个任务中保持独立，这是我非常喜欢的。对每个任务的控制访问是非常受指导的。

# 演示:使用 Amazon ECS 在生产环境中运行 Docker 映像

在这个演示中，我将向您展示如何使用 Amazon ECS 并在其上运行 docker 映像。让我们开始吧。

前往[亚马逊的登录页面](https://aws.amazon.com/console/)并登录(如果你已经有账户的话)。如果你没有，那就创建一个免费账户吧。这是您登录后将看到的控制台。

![](img/54f0a7c9dca69350ab9c2075d10e339a.png)

键入“ECS ”,然后单击该服务。如果您之前没有创建集群，您会看到一个**开始**按钮。继续点击**开始**。

![](img/acefdfa3f15c9a58255d67641045dcc6.png)

在 AWS ECS 上运行 docker 映像有 4 个主要步骤，**容器定义、任务定义、服务、集群**。

![](img/1c561fd34b85b5b2dd26d5c2b9907440.png)

**配置容器定义:**为您的容器选择一个图像。您有 4 个选项——一个样例应用程序映像、Nginx 映像、tomcat-webserver 和一个自定义映像。

![](img/a30b47ef618630607574bb3a32e83860.png)

对于这个演示，我选择了 sample-app。

![](img/0b8dd5a308b90bc05fe21acada066fa2.png)

如果您希望更改配置，请单击右上角的编辑。

![](img/d0ced05222ee29fbfa5f48a4904f6adf.png)

您可以编辑**容器名称、要使用的映像、内存限制、端口映射、健康检查配置、环境配置、容器超时配置、网络设置、存储和日志记录配置、资源限制、**和 **Docker 标签。**在本次演示中，我使用了所有默认配置。

**配置** **任务定义:**由**任务定义名称**、**网络模式**、**任务执行角色**、**能力**、**任务内存、**和**任务 CPU** 组成。

![](img/058a737661ee6ea2a3b34f3d1cf11bf4.png)

您可以点击右上角的**编辑**，根据您的需求进行配置。对于这个演示，我使用所有的默认配置。完成后，点击右下角的下一个按钮**。**

![](img/687eacd169de6227536b275e8ff0a8a7.png)

**配置服务:**您可以继续更改**服务名称、所需任务数量**、**安全组**并选择**负载均衡器类型**。对于这个演示，我没有使用负载平衡器。点击**下一步。**

![](img/903ece2219f7e615bfb7a458fece7cf4.png)

**配置集群:**通过添加一个**集群名称**来配置集群，然后单击 **Next。**

![](img/a718b4ef02aaf965bf5c665792089e7a.png)

**回顾:**一旦你配置好了一切，你应该会看到类似这样的东西。

![](img/06d593fd94fde459f8472dc84caa8b65.png)

回顾一切，点击右下角的**创建**。现在将创建所有的服务。这可能需要大约 10 分钟。

![](img/0d0ab864ffbe7820a02504edc971b412.png)

一旦一切都处于**完成**状态，点击**查看服务**

![](img/4dc022dd53719f1255f7b64e632a35d8.png)

你会看到这样的东西。它将向您显示您的**集群详细信息、任务定义**和一个选项，用于**删除**和**更新**它。

![](img/a1b11f91ada0535ecc8651c86b1389f7.png)

您可以查看进一步的详细信息，如 **VPC** 、**子网**、**任务**、**事件**、**自动缩放**、**部署**、**指标**、**标签**和**日志**。

![](img/3f8c9643c08f30e078d09ef6b5df50ad.png)

现在，点击**任务**来检查已部署的容器。

![](img/a267393089eb31e43723c573dd4ed573.png)

点击**任务名称**，如下图所示。

![](img/22d90139e6cabb949ec437dd3acfed8e.png)

点击**网络**部分下的 **ENI Id** 。

![](img/a5a82f65f5dadfbce536fa396d023dd2.png)

您将被带到**网络接口**页面，如下所示:

![](img/4330740e9cd5777bb0e6ec00e4b453c6.png)

向下滚动，你会看到你的 IPV4 公共 IP。复制它。

![](img/d622ef43b7954cc0f7009d6fd5b0af20.png)

像贴网址一样贴在任何浏览器上。您将在那里看到 docker 容器输出。您将看到您的示例应用程序。

![](img/d2a5463d0e960ad40d8d87048ac6cb25.png)

这只是一个示例应用程序。只需几个步骤，您就可以运行任何类型的应用程序或任何类型的 Docker 映像。

这就把我们带到了这篇亚马逊 ECS 文章的结尾。您可以将该服务与各种 DevOps 工具集成在一起，从而简化构建过程。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=docker-container-in-production-amazon-ecs)

请留意本系列中的其他文章，它们将解释 AWS 的各个方面。

> *1。* [*AWS 教程*](/edureka/amazon-aws-tutorial-4af6fefa9941)
> 
> *2。* [*AWS EC2*](/edureka/aws-ec2-tutorial-16583cc7798e)
> 
> *3。*[*AWS Lambda*](/edureka/aws-lambda-tutorial-cadd47fbd39b)
> 
> *4。* [*AWS 弹性豆茎*](/edureka/aws-elastic-beanstalk-647ae1d35e2)
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
> *11。* [*亚马逊*](/edureka/amazon-lightsail-tutorial-c2ccc800c4b7)
> 
> *12。* [*AWS 定价*](/edureka/aws-pricing-91e1137280a9)
> 
> *13。* [*亚马逊雅典娜*](/edureka/amazon-athena-tutorial-c7583053495f)
> 
> *14。*[AWS CLI](/edureka/aws-cli-9614bf69292d)
> 
> 15。 [*亚马逊 VPC 教程*](/edureka/amazon-vpc-tutorial-45b7467bcf1d)
> 
> *15。*[*AWS vs Azure*](/edureka/aws-vs-azure-1a882339f127)
> 
> *17。* [*内部部署 vs 云计算*](/edureka/on-premise-vs-cloud-computing-f9aee3b05f50)
> 
> 18。 [*亚马逊迪纳摩 DB 教程*](/edureka/amazon-dynamodb-tutorial-74d032bde759)
> 
> 19。 [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> 20。 [*AWS 代码提交*](/edureka/aws-codecommit-31ef5a801fcf)
> 
> *21。* [*顶级 AWS 架构师面试问题*](/edureka/aws-architect-interview-questions-5bb705c6b660)
> 
> *22。* [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> *23。* [*使用 AWS 创建网站*](/edureka/create-websites-using-aws-1577a255ea36)
> 
> *24。* [*亚马逊路线 53*](/edureka/amazon-route-53-c22c470c22f1)
> 
> *25。* [*用 AWS WAF 保护 Web 应用*](/edureka/secure-web-applications-with-aws-waf-cf0a543fd0ab)

*原载于 2019 年 6 月 27 日*[*https://www.edureka.co*](https://www.edureka.co/blog/docker-container-in-production-amazon-ecs/)*。*