# 詹金斯主从架构

> 原文：<https://medium.com/edureka/jenkins-master-and-slave-architecture-e3d6c4728945?source=collection_archive---------0----------------------->

![](img/44da9aa734057cd62cc0a379776165d3.png)

詹金斯是 DevOps 中最重要的工具之一。Jenkins 用于 DevOps 的持续集成阶段。在本文中，我将讨论 Jenkins 主从架构。我将涉及的要点如下:

*   詹金斯是什么？
*   詹金斯建筑
*   詹金斯主从架构是如何工作的？
*   用 Jenkins Master 设置奴隶

让我们从第一个话题开始。

# 詹金斯是什么？

![](img/f4cfabed7701e96a1c6efa9437b42029.png)

Jenkins 是一个用 Java 编写的开源自动化工具，带有为持续集成目的而构建的插件。Jenkins 习惯于不断构建和测试您的软件项目，这使得开发人员更容易将更改集成到项目中，也使得用户更容易获得新的构建。它还允许您通过集成大量的测试和部署技术来持续地交付您的软件。

有了 Jenkins，组织可以通过自动化加速软件开发过程。Jenkins 集成了所有类型的开发生命周期过程，包括构建、文档、测试、打包、登台、部署、静态分析等等。

Jenkins 借助插件实现持续集成。插件允许集成各种 DevOps 阶段。如果你想集成一个特定的工具，你需要安装这个工具的插件。比如 Git，Maven 2 项目，亚马逊 EC2，HTML publisher 等。

**詹金斯的优势包括:**

*   它是一个开源工具，拥有强大的社区支持。
*   太容易安装了。
*   它有 1000 多个插件来减轻你的工作。如果一个插件不存在，你可以把它编码并分享给社区。
*   这是免费的。
*   它是用 Java 构建的，因此可以移植到所有主流平台。

# 詹金斯建筑

让我们看看下面的詹金斯架构图描绘的同样。

![](img/bd51740c9bc62e34dbfaabd3ec24aa8b.png)

这一台 Jenkins 服务器不足以满足某些要求，例如:

*   有时您可能需要几个不同的环境来测试您的构建。这不能由单个 Jenkins 服务器来完成。
*   如果定期构建更大更重的项目，那么单个 Jenkins 服务器无法简单地处理全部负载。

为了解决上述需求，Jenkins 分布式体系结构应运而生。

# 詹金斯分布式体系结构

Jenkins 使用主从架构来管理分布式构建。在这种体系结构中，主机和从机通过 TCP/IP 协议进行通信。

**詹金斯大师**

你的主服务器是主服务器。主人的工作是处理:

*   计划生成作业。
*   将构建分派到从属服务器进行实际执行。
*   监控从机(可能根据需要使它们联机和脱机)。
*   记录和展示构建结果。
*   Jenkins 的主实例也可以直接执行构建作业。

# 詹金斯奴隶

从机是在远程机器上运行的 Java 可执行文件。以下是詹金斯奴隶的特征:

*   它侦听来自 Jenkins 主实例的请求。
*   从机可以运行在多种操作系统上。
*   从机的工作是按照要求去做，包括执行主机分派的构建任务。
*   你可以配置一个项目总是在一个特定的从属机器或特定类型的从属机器上运行，或者简单地让 Jenkins 挑选下一个可用的从属机器。

下图不言自明。它由一个管理三个 Jenkins 从机的 Jenkins 主机组成。

![](img/0544879051c4bb4ce12e40d5d1aeb8f3.png)

# 詹金斯主从架构是如何工作的？

现在让我们看一个例子，在这个例子中，我们使用 Jenkins 在不同的环境中进行测试，比如 Ubuntu、MAC、Windows 等。

下图表示同样的情况:

![](img/a0aefd729c04a40772b3dff1430bcf5b.png)

上图代表以下功能:

*   Jenkins 定期检查 Git 存储库，查看源代码中是否有任何更改。
*   每个构建都需要不同的测试环境，这对于单个 Jenkins 服务器来说是不可能的。为了在不同的环境中执行测试，Jenkins 使用了各种从设备，如图所示。
*   Jenkins Master 请求这些从设备执行测试并生成测试报告。

# 如何设置詹金斯主人和奴隶？

1.  转到“管理 Jenkins”部分，向下滚动到“管理节点”部分。

![](img/c272540e90c2764f116a5d85bf333e73.png)![](img/3b71374560ada1ea5d08b828bdf99272.png)

2.单击新节点

![](img/36bec4e8c361bbf2c3fa660958fb97e7.png)

**3。**为节点命名，选择永久代理选项，然后单击 Ok。

![](img/ba67926219306ac7274e538e9c5d18bb.png)

**4。**输入节点从机的详细信息。这里**的执行者数量**只不过是这个奴隶可以并行运行的作业数量。这里我们把它保持在 2。名称输入为“Slave1”的**标签**可用于配置作业以使用该从机。选择**用法**尽可能多地使用该节点。对于 l **启动方法，**我们选择“通过将代理连接到主服务器来启动代理”选项。如果此选项不可见，请转到 J**Jenkins 主页- >管理 Jenkins - >配置全局安全性**。在代理部分**点击随机**并保存。现在，您将找到所需的选项。输入**自定义工作目录路径**作为从节点的工作空间。在**可用性**中，选择“尽可能保持该代理在线”。点击**保存**。

![](img/6b1359d8f0405d0100f74da002c8fcd8.png)

完成上述步骤后，新节点计算机最初将处于脱机状态，但如果正确输入了上一屏幕中的所有设置，它将会联机。如果需要，可以随时使节点从机脱机。

![](img/d2ce4ef6cc76b638d7e203a5db053641.png)

5.现在，既然您的从属服务器已经启动并运行，让我们在从属服务器上执行一个作业。为此，我已经有了一个现有的作业，我将在这个从属服务器上运行这个作业。打开此工单并点击配置。现在，在“常规”部分，单击“限制该项目的运行位置”。在标签表达式中，输入从属服务器的名称并保存。现在，单击 Build now，查看该作业的输出。如果一切都正确，您将看到输出为成功。

![](img/8b0e37730fd2fc1fb9c5ec58b81c020f.png)

至此，我们结束了这篇关于詹金斯主从架构的博客。我希望你已经理解了这里解释的所有概念。

在这篇**厨师面试问题**的文章中，我已经尽量涵盖了几乎所有与厨师相关的问题。如果你有任何其他问题，你可以在下面的评论区提出来，你会尽快得到答案。我也会建议在面试之前。

如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=jenkins-master-and-slave-architecture-a-complete-guide)

请留意本系列中解释 DevOps 各个方面的其他文章。

> *1。* [*DevOps 教程*](/edureka/devops-tutorial-89363dac9d3f)
> 
> *2。* [*Git 教程*](/edureka/git-tutorial-da652b566ece)
> 
> *3。* [*詹金斯教程*](/edureka/jenkins-tutorial-68110a2b4bb3)
> 
> *4。* [*Docker 教程*](/edureka/docker-tutorial-9a6a6140d917)
> 
> *5。* [*Ansible 教程*](/edureka/ansible-tutorial-9a6794a49b23)
> 
> *6。* [*木偶教程*](/edureka/puppet-tutorial-848861e45cc2)
> 
> *7。* [*厨师教程*](/edureka/chef-tutorial-8205607f4564)
> 
> *8。* [*Nagios 教程*](/edureka/nagios-tutorial-e63e2a744cc8)
> 
> *9。* [*如何编排 DevOps 工具？*](/edureka/devops-tools-56e7d68994af)
> 
> *10。* [*连续交货*](/edureka/continuous-delivery-5ca2358aedd8)
> 
> *11。* [*持续集成*](/edureka/continuous-integration-615325cfeeac)
> 
> *12。* [*连续部署*](/edureka/continuous-deployment-b03df3e3c44c)
> 
> 13。 [*持续交付 vs 持续部署*](/edureka/continuous-delivery-vs-continuous-deployment-5375642865a)
> 
> *14。* [*CI CD 管道*](/edureka/ci-cd-pipeline-5508227b19ca)
> 
> *15。* [*码头工人撰写*](/edureka/docker-compose-containerizing-mean-stack-application-e4516a3c8c89)
> 
> 16。 [*码头工人群*](/edureka/docker-swarm-cluster-of-docker-engines-for-high-availability-40d9662a8df1)
> 
> 17。 [*Docker 联网*](/edureka/docker-networking-1a7d65e89013)
> 
> 18。[](/edureka/ansible-vault-secure-secrets-f5c322779c77)
> 
> **19。* [*可担任的角色*](/edureka/ansible-roles-78d48578aca1)*
> 
> **20。* [*适用于 AWS*](/edureka/ansible-for-aws-provision-ec2-instance-9308b49daed9)*
> 
> **21。* [*詹金斯管道*](/edureka/jenkins-pipeline-tutorial-continuous-delivery-75a86936bc92)*
> 
> **22。* [*顶级 Docker 命令*](/edureka/docker-commands-29f7551498a8)*
> 
> **23。*[*Git vs GitHub*](/edureka/git-vs-github-67c511d09d3e)*
> 
> **24。* [*顶级 Git 命令*](/edureka/git-commands-with-example-7c5a555d14c)*
> 
> **25。* [*DevOps 面试问题*](/edureka/devops-interview-questions-e91a4e6ecbf3)*
> 
> **26。* [*谁是 DevOps 工程师？*](/edureka/devops-engineer-role-481567822e06)*
> 
> **27。* [*DevOps 生命周期*](/edureka/devops-lifecycle-8412a213a654)*
> 
> **28。*[*Git ref log*](/edureka/git-reflog-dc05158c1217)*
> 
> **29。* [*可变配置*](/edureka/ansible-provisioning-setting-up-lamp-stack-d8549b38dc59)*
> 
> **三十。* [*组织正在寻找的顶尖 DevOps 技能*](/edureka/devops-skills-f6a7614ac1c7)*
> 
> *三十岁。 [*瀑布 vs 敏捷*](/edureka/waterfall-vs-agile-991b14509fe8)*
> 
> *31。 [*詹金斯小抄*](/edureka/jenkins-cheat-sheet-e0f7e25558a3)*
> 
> **32。*[](/edureka/ansible-cheat-sheet-guide-5fe615ad65c0)*
> 
> ***33。* [*Ansible 面试问答*](/edureka/ansible-interview-questions-adf8750be54)**
> 
> **34。 [*50 码头工人面试问题*](/edureka/docker-interview-questions-da0010bedb75)**
> 
> ***三十五。* [*敏捷方法论*](/edureka/what-is-agile-methodology-fe8ad9f0da2f)**
> 
> **36。 [*詹金斯面试问题*](/edureka/jenkins-interview-questions-7bb54bc8c679)**
> 
> ***37。* [*Git 面试问题*](/edureka/git-interview-questions-32fb0f618565)**
> 
> ***38。* [*Docker 架构*](/edureka/docker-architecture-be79628e076e)**
> 
> **39。 [*Linux 中使用的命令 DevOps*](/edureka/linux-commands-in-devops-73b5a2bcd007)**
> 
> ***40。* [*詹金斯 vs 竹子*](/edureka/jenkins-vs-bamboo-782c6b775cd5)**
> 
> ***41。* [*Nagios 教程*](/edureka/nagios-tutorial-e63e2a744cc8)**
> 
> ***42。* [*Nagios 面试问题*](/edureka/nagios-interview-questions-f3719926cc67)**
> 
> ***43。* [*DevOps 实时场景*](/edureka/jenkins-x-d87c0271af57)**
> 
> ***44。* [*詹金斯和詹金斯 X 的区别*](/edureka/jenkins-vs-bamboo-782c6b775cd5)**
> 
> ***45。*[*Docker for Windows*](/edureka/docker-for-windows-ed971362c1ec)**
> 
> ***46。*[*Git vs Github*](http://git%20vs%20github/)**

***原载于 2019 年 12 月 12 日*[*https://www.edureka.co*](https://www.edureka.co/blog/jenkins-master-and-slave-architecture-a-complete-guide/)*。***