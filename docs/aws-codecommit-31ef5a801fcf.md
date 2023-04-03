# AWS CodeCommit —存储库的新家

> 原文：<https://medium.com/edureka/aws-codecommit-31ef5a801fcf?source=collection_archive---------1----------------------->

![](img/01c4c01ddc22cedb000f1fc5e1db2090.png)

AWS CodeCommit — Edureka

作为一名开发人员，难道您不想将全部精力放在生产上，而不是存储库的管理和维护上吗？这就是 AWS CodeCommit 发挥作用的地方。事实证明，通过提供安全且全面管理的服务，它可以在各个方面提升组织的绩效。

涵盖的主题:

*   AWS 代码提交简介
*   AWS 代码提交 vs GitHub
*   AWS 代码提交工作流
*   案例研究:Edmunds.com 如何将管理和维护时间减少 95%
*   演示:在 CodeCommit 中创建一个存储库，并探索它的特性

# AWS 代码提交简介

AWS CodeCommit 是 Amazon 提供的源代码控制存储和版本代码服务。它利用 CI/CD 的优势，帮助团队更好地进行代码管理和协作。它消除了对第三方版本控制的需要。该服务可用于存储文档、源代码和二进制文件等资产。它还可以帮助您管理这些资产。管理包括缩放、集成、合并、推送和拉取代码变更。让我们更好地了解一下 CodeCommit 提供的服务:

## 全面管理的服务:

如果您是一名 DevOps 工程师，难道您不想将全部精力放在生产上，而不是维护更新、管理自己的硬件或软件吗？AWS CodeCommit 消除了管理资源的枯燥任务，提供了高可用性和持久性的服务。

## **安全存储代码:**

因为它是一个版本控制系统，它存储你的代码。事实上，它可以存储任何类型的数据，无论是文档还是二进制文件。存储的数据非常安全，因为它们在静态和传输过程中都是加密的。

## 与代码协同工作:

AWS CodeCommit 允许您协作处理代码。您可以在代码的一部分工作，而其他人/团队可以在另一部分工作，变更/更新可以在存储库中进行推送和合并。用户可以检查、评论彼此的代码，帮助他们最大限度地开发代码。

## 高度可扩展:

AWS CodeCommit 允许您根据自己的需要进行伸缩。该服务可以处理大型存储库、大量具有大型分支的文件和冗长的提交历史。

## 集成:

您可以轻松地将 AWS CodeCommit 与其他 AWS 服务集成在一起。它使这些服务靠近其他资源，使得获取和使用更容易、更快，提高了开发生命周期的速度和频率。它还可以让您非常容易地集成第三方服务。

## 迁移:

您可以轻松地将任何基于 Git 的存储库迁移到 CodeCommit。

## 使用 Git 的交互:

与 CodeCommit 交互非常简单，因为它是基于 Git 的。您可以使用 Git 命令来拉、推、合并或执行其他操作。它还为您提供了使用 AWS CLI 命令及其自己的 API 的功能。

## 跨账户访问:

CodeCommit 允许您交叉链接两个不同的 AWS 帐户，从而更容易在两个帐户之间安全地共享存储库。有一些事情要记住，比如你不应该共享你的 ssh 密钥或 AWS 凭证。

# **AWS CodeCommit vs GitHub**

GitHub 也是版本控制系统之一。我们先来看看 GitHub 和 CodeCommit 的相似之处。

1.  CodeCommit 和 GitHub 使用 Git 库。
2.  两者都支持代码审查。
3.  它们可以与 AWS CodeBuild 集成。
4.  它们都使用两种认证方法，SSH 和 HTTPS。

现在让我们来看看它们之间的区别。

1.  **安全性:** Git 由 GitHub 用户管理，而 CodeCommit 使用 AWS 的 IAM 角色和用户。这使得它非常安全。使用 IAM 角色可以让您只与特定的人共享存储库，同时限制他们对存储库的访问。例如，很少用户可以查看存储库，很少人可以进行编辑，等等。CodeCommit 允许您使用 MFA 进行第三步身份验证。
2.  **托管:** Git 就像 GitHub 的家，但与 AWS 一起使用时就不一样了。因此，当 GitHub 与 AWS 一起使用时，它就像一个第三方工具。然而，CodeCommit 托管在 AWS 上并由 AWS 管理，使得与 CodeBuild 的集成及其使用更加简单。
3.  用户界面: GitHub 功能齐全，有一个非常好的用户界面。而 CodeCommit 用户界面相当普通。

# AWS 代码提交工作流

看看下面的流程图，了解 CodeCommit 的工作流程。它由三部分组成— **开发机器**、 **AWS CLI/CodeCommit 控制台**、 **AWS CodeCommit 服务**。

![](img/70fa6ff666fff54b2620366040d678e6.png)

*   您可以使用 AWS CLI 或 AWS CodeCommit 控制台创建一个存储库(远程),该存储库将反映到您的 AWS CodeCommit 服务中，以启动您的项目。
*   从你的开发机器上做一个 **git 克隆**，在 CodeCommit 服务端会收到一个 **git 克隆** **reques** t。这将最终同步步骤 1 中创建的远程存储库和刚刚克隆的本地存储库。
*   使用开发机器上的本地存储库来修改代码。运行 **git add** 在本地暂存修改后的文件， **git commit** 在本地提交文件， **git push** 将修改后的更改推送到 CodeCommit。这将依次修改远程存储库。
*   使用 **git pull** 下载在同一个存储库中工作的其他团队成员所做的变更或修改。更新远程存储库，并将这些更新发送到您的开发机器，以保持本地存储库的更新。

# 个案研究

让我们来看一个案例研究，以便更好地指出我的观点。

## 关于公司:

我要谈谈这家名为 Edmunds.com 的公司。这是一个在线网站/应用程序，让买家浏览汽车，查看待售汽车的照片、视频等。

## 挑战:

以前使用的内部 SCM 存在一些问题，如下所述:

*   向 SCM 添加新用户很困难
*   供应链管理有着巨大的运营负担
*   管理和维护硬件和软件既困难又耗时
*   存储库缺乏备份
*   存储库缺乏集群能力
*   服务将受到停机时间的影响

## AWS 代码致力于拯救:

在研究了许多其他服务之后，Edmunds.com 开始使用 AWS 的 CodeCommit。他们将 1000 多个存储库和 270 多个用户迁移到 AWS。CodeCommit 为公司处理托管、维护、备份和扩展。

1.  **完全管理:**该公司的管理和维护时间减少了约 95%。
2.  **高可用性:**通过使用亚马逊的 S3 跨不同的可用性区域存储备份，使得 git 存储库高度可用。
3.  **代码高效:**公司手动为每位用户节省了 450 美元。
4.  **灵活:**使用亚马逊的 CodeCommit 使他们的网站在用户数量方面很容易扩展，非常灵活。

# 演示:在 CodeCommit 中创建一个存储库，并探索它的特性

在这一节中，我将演示在 CodeCommit 上创建一个存储库，创建一个分支，提交更改，查看更改并合并存储库。让我们看一看。

**步骤 1:** 转到您的 [AWS 登录页面](https://aws.amazon.com/)并登录您的 AWS 帐户。如果您没有帐户，请创建一个免费帐户。登录后，您应该会看到如下所示的页面:

![](img/67de09e655ce4d51132dc072eed7409e.png)

搜索 CodeCommit 并单击该服务。此外，单击 Create Repository 创建一个存储库。

![](img/c87f8ea88b6cd06dc09b47c9e1cfb2d1.png)

系统会提示您添加您的**存储库名称**和**描述**。添加这些并点击**创建**。

![](img/9f44d922708a109417553a7910955e27.png)

你应该和我一样得到一个成功的消息。

![](img/5e7f036f06903b8a6283b38955857aa6.png)

有两种方式连接您的存储库— SSH 和 HTTPS。在这种情况下，我将使用 HTTPS。现在已经创建了一个存储库，接下来在存储库中创建文件。创建存储库时，它总是空的。你必须创建和添加文件。进入您创建的存储库，点击**创建文件。**

![](img/0c30191a7427feccbdc4aee6f5d188e1.png)

一旦你创建了文件。继续将代码添加到文件中。

![](img/f1432f214d165a9d3734833ac2a706fa.png)

现在您已经编写了代码，您需要提交这些更改。添加**文件名**、**作者姓名**、**电子邮件 ID** 、**提交消息**，点击**提交更改**。

![](img/662807e7d70833d9e9f70593eb09c4c8.png)

现在，当您通过单击**存储库**导航到存储库部分时，您应该会在那里看到您的存储库。

![](img/f3f3461f08b375b5cc428666b3fd5010.png)![](img/ef619503f2a89a9d0ffb93b95a8febd6.png)

继续点击你的仓库，你应该看到你刚刚创建的文件。

## 什么是分支，为什么使用分支

既然您已经创建了一个存储库、一个文件并将代码添加到文件中，那么让我们学习如何创建分支。你们知道为什么要用树枝吗？在开发或生产环境中，您不是唯一一个处理这些存储库的人。将会有其他人在同一个存储库的不同部分工作。不同的人处理同一个文件会让人感到困惑。

在这种情况下，最好使用分支。分支基本上是原始文件的副本，可以分配给不同的人。他们可以进行更改，提交更改并将其推送到 CodeCommit。在某些测试之后，当这些代码被验证时，它们可以与主分支合并。在下一节中，我将解释如何创建分支、编辑分支、查看和比较更改、查看提交历史以及如何将这些分支与主分支合并。

**第二步:**要创建分支，点击最右边的分支。

![](img/0051603c11ef67dfb4c76675fad714e6.png)

然后点击右上角的**创建分支**，如下图所示:

![](img/7dc915983f3713c6bdbe0a119d098020.png)

添加分支名称和描述，并点击**创建分支**。

![](img/cc0e9ace787bdd0a44fb5230a57c9b23.png)

您应该会看到类似这样的内容:

![](img/bf71dfa5d38a050917e6d0dcf514c339.png)

单击该分支后，您会看到它包含了主分支上存在的所有文件。

![](img/944f76c4a87fbea3daf309380879c973.png)

让我们继续对这个分支进行更改。点击文件 **ec2.txt** 。

![](img/78c81da1dbca8289aa3749982d47b248.png)

点击**编辑**，如下图所示。

![](img/244ad3f9b4c5c2ed06c7f7c5ada4413d.png)

根据您的意愿进行更改，并通过添加**作者姓名**、**电子邮件地址**、**提交消息**来提交这些更改。继续点击**提交更改**。

![](img/6721ac8951a089d4d049a3c985cea62d.png)

你应该和我一样得到一个成功的消息。

![](img/0a106eabaad256305b63ebbb6754f76f.png)

现在您有了一个主分支和另一个与主分支稍有不同的分支，让我们比较它们以寻找不同之处。点击**创建拉动式请求**。

![](img/4ab3cd6923bd3216f35c7f6eae72c260.png)

在比较当前分支和主分支时，选择主分支。点击**比较**。

![](img/a49ff0896d169a6d0f53bfc1768c0c28.png)

这突出了主分支和另一个分支的所有差异。

![](img/13ea8f4e1292da2829c1c649b2c2063e.png)

您还可以检查提交历史。只需点击变更旁边的**提交**。

![](img/1386d4bb1682264673f537f7287f6556.png)

假设您同意在这个分支中所做的更改，并且您想要将这些更改反映到您的主分支中，您可以合并这两个分支。增加**标题**和**描述**。

![](img/10096ffdfc952dc7d9e9a24a2363eb54.png)

并点击**创建**。

![](img/c956761bce00a6d3fb1908cc45e381ae.png)

您会收到成功提取请求的通知。

![](img/b7a0339a5bf1dd5d1d359fd90a67cce8.png)

点击**合并**最终合并两个分支。

![](img/3c1251db43036237f88cb27b089ecc66.png)

这就把我们带到了 AWS CodeCommit 博客的结尾。您可以将该服务与各种 DevOps 工具集成在一起，从而简化构建过程。我希望这篇博客对你有所帮助。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=aws-codecommit)

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
> *14。* [*AWS CLI*](/edureka/aws-cli-9614bf69292d)
> 
> *15。* [*亚马逊 VPC 教程*](/edureka/amazon-vpc-tutorial-45b7467bcf1d)
> 
> *15。*[*AWS vs Azure*](/edureka/aws-vs-azure-1a882339f127)
> 
> *17。* [*内部部署 vs 云计算*](/edureka/on-premise-vs-cloud-computing-f9aee3b05f50)
> 
> 18。 [*亚马逊迪纳摩 DB 教程*](/edureka/amazon-dynamodb-tutorial-74d032bde759)
> 
> *19。* [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> 20。 [*AWS 简历*](/edureka/aws-resume-7453d9477c74)
> 
> 21。 [*顶级 AWS 架构师面试问题*](/edureka/aws-architect-interview-questions-5bb705c6b660)
> 
> *22。* [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> *23。* [*使用 AWS*](/edureka/create-websites-using-aws-1577a255ea36) 创建网站
> 
> *24。* [*亚马逊路线 53*](/edureka/amazon-route-53-c22c470c22f1)
> 
> *25。* [*用 AWS WAF 保护 Web 应用*](/edureka/secure-web-applications-with-aws-waf-cf0a543fd0ab)

*原载于 2019 年 6 月 21 日*[*https://www.edureka.co*](https://www.edureka.co/blog/aws-codecommit/)*。*