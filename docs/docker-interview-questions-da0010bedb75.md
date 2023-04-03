# 2019 年你必须准备的 50 个 Docker 面试问题

> 原文：<https://medium.com/edureka/docker-interview-questions-da0010bedb75?source=collection_archive---------3----------------------->

![](img/a299ba5ec7926aec7ef7729ec6798ccf.png)

Docker 于 2013 年推出，轰动了 it 行业。很快，到 2017 年底，它成为一个大热门，有 80 亿次容器图像下载。对码头工人不断增长的需求表明职位空缺呈指数增长。这篇文章列出了 50 个最重要的 *Docker 面试问题，你可以利用所有的新职位空缺。*

我将这 50 个问题分为:

*   Docker 基本问题
*   Docker 基本命令
*   Docker 高级问题

# Docker 基本问题

这类 Docker 面试问题由你应该知道的问题组成。这些都是最基本的问题。面试官会从这些开始，最终增加难度。让我们来看看它们。

## **1。什么是 Hypervisor？**

虚拟机管理程序是一种使虚拟化成为可能的软件。它也被称为虚拟机监视器。它划分主机系统，并将资源分配给每个划分的虚拟环境。基本上，一个主机系统上可以有多个操作系统。有两种类型的虚拟机管理程序:

*   类型 1:也称为本机虚拟机管理程序或裸机虚拟机管理程序。它直接在底层主机系统上运行。它可以直接访问主机的系统硬件，因此不需要基本的服务器操作系统。
*   类型 2:这种虚拟机管理程序利用底层主机操作系统。它也被称为托管虚拟机管理程序。

## 2.什么是虚拟化？

虚拟化是创建某个事物(计算存储、服务器、应用程序等)的基于软件的虚拟版本的过程。).这些虚拟版本或环境是从单个物理硬件系统创建的。虚拟化允许您将一个系统分成许多不同的部分，这些部分就像独立的、不同的单独系统一样。一种名为 Hypervisor 的软件使这种拆分成为可能。由虚拟机管理程序创建的虚拟环境称为虚拟机。

## 3.什么是集装箱化？

让我用一个例子来解释一下。通常，在软件开发过程中，由于依赖关系，在一台机器上开发的代码可能无法在任何其他机器上完美运行。集装箱化的概念解决了这个问题。因此，基本上，正在开发和部署的应用程序与其所有配置文件和依赖项捆绑在一起。这个包称为容器。现在，当您希望在另一个系统上运行应用程序时，会部署容器，这将提供一个无 bug 的环境，因为所有的依赖项和库都包装在一起。最著名的集装箱化环境是 Docker 和 Kubernetes。

## **4。虚拟化和容器化的区别**

一旦你解释了容器化和虚拟化，下一个预期的问题将是差异。这个问题可能是虚拟化和容器化之间的区别，也可能是虚拟机和容器之间的区别。不管怎样，这就是你的反应。

容器为运行应用程序提供了一个隔离的环境。整个用户空间明确地专用于应用程序。容器内的任何更改都不会反映在主机上，甚至不会反映在同一主机上运行的其他容器上。容器是应用层的抽象。每个容器都是不同的应用程序。

而在虚拟化中，虚拟机管理程序向来宾提供整个虚拟机(包括内核)。虚拟机是硬件层的抽象。每个虚拟机都是一台物理机。

## 5.Docker 是什么？

既然是 Docker 面试，那么很明显会有一个关于 Docker 是什么的问题。先从一个小定义说起。

Docker 是一个容器化平台，它将您的应用程序及其所有依赖项以容器的形式打包在一起，以确保您的应用程序可以在任何环境中无缝工作，无论是开发、测试还是生产环境。Docker 容器，将一个软件包装在一个完整的文件系统中，该文件系统包含运行所需的一切:代码、运行时、系统工具、系统库等。它基本上包装了任何可以安装在服务器上的东西。这保证了软件将总是相同地运行，而不管其环境如何。

## 6.什么是 Docker 容器？

Docker 容器包括应用程序及其所有依赖项。它与其他容器共享内核，在主机操作系统的用户空间中作为独立的进程运行。Docker 容器不依赖于任何特定的基础设施:它们可以在任何计算机、任何基础设施和任何云中运行。Docker 容器基本上是 Docker 映像的运行时实例。

## 7.什么是 Docker 图像？

当您提到 Docker 图像时，您的下一个问题将是“什么是 Docker 图像”。

Docker 图像是 Docker 容器的来源。换句话说，Docker 图像用于创建容器。当用户运行 Docker 映像时，会创建一个容器实例。这些 docker 映像可以部署到任何 Docker 环境中。

## 8.什么是 Docker Hub？

Docker 图像创建 docker 容器。这些 docker 图像必须有一个注册表。这个注册表是 Docker Hub。用户可以从 Docker Hub 获取图像，并使用它们来创建定制的图像和容器。目前， [Docker Hub](https://hub.docker.com/) 是世界上最大的图像容器公共存储库。

## 9.解释 Docker 架构？

Docker 架构由 Docker 引擎组成，Docker 引擎是一个客户端-服务器应用程序，有三个主要组件:

1.  服务器是一种长期运行的程序，称为守护进程(docker 命令)。
2.  一个 REST API，它指定了程序可以用来与守护进程对话并指示它做什么的接口。
3.  命令行界面(CLI)客户端(docker 命令)。
4.  CLI 使用 Docker REST API 通过脚本或直接 CLI 命令来控制 Docker 守护进程或与之交互。许多其他 Docker 应用程序使用底层 API 和 CLI。

## 10.什么是 Dockerfile？

让我们先简单解释一下 Dockerfile，然后给出例子和命令来支持你的论点。

Docker 可以通过从一个名为 Dockerfile 的文件中读取指令来自动构建映像。Dockerfile 是一个文本文档，它包含用户可以在命令行上调用的所有命令来组合一个图像。使用 docker build，用户可以创建一个连续执行几个命令行指令的自动化构建。

面试官不仅仅期待定义，因此要解释如何使用经验中的 does 文件。

## 11.给我们讲讲 Docker Compose 吧。

Docker Compose 是一个 YAML 文件，它包含有关设置 Docker 应用程序的服务、网络和卷的详细信息。因此，您可以使用 Docker Compose 创建单独的容器，托管它们并让它们相互通信。每个容器将暴露一个用于与其他容器通信的端口。

## 12.什么是 Docker Swarm？

你应该和 Docker Swarm 一起工作过，因为这是 Docker 的一个重要概念。

Docker Swarm 是 Docker 的本地集群。它将一个 Docker 主机池转变为一个虚拟 Docker 主机。Docker Swarm 提供标准的 Docker API，任何已经与 Docker 守护进程通信的工具都可以使用 Swarm 透明地扩展到多个主机。

## 13.什么是 Docker 名称空间？

名称空间是 Linux 的特性之一，也是容器的一个重要概念。命名空间在容器中增加了一层隔离。Docker 提供了各种名称空间，以便保持可移植性并且不影响底层主机系统。Docker - PID、Mount、IPC、User、Network 支持的命名空间类型很少

## 14.Docker 容器的生命周期是什么？

这是码头工人面试中最常见的问题之一。Docker 容器具有以下生命周期:

*   创建一个容器
*   运行容器
*   暂停容器(可选)
*   取消暂停容器(可选)
*   启动容器
*   停下集装箱
*   重新启动容器
*   关掉容器
*   摧毁集装箱

## 15.什么是 Docker 机？

Docker machine 是一个让你在虚拟主机上安装 Docker 引擎的工具。现在可以使用 docker-machine 命令来管理这些主机。Docker machine 还允许您配置 Docker Swarm 集群。

# Docker 基本命令

一旦你回答了基本的概念性问题，面试官会增加难度。所以让我们继续这篇 Docker 面试问题文章的下一部分。本节讨论 docker 用户中非常常见的命令。

## 16.如何检查 Docker 客户端和 Docker 服务器版本？

以下命令提供了关于 Docker 客户机和服务器版本的信息:

`$ docker version`

## 17.如何获得运行、暂停和停止的容器数量？

您可以使用下面的命令来获得关于您的系统上安装的 docker 的详细信息。

`$ docker info`

您可以获得运行、暂停、停止的容器数量、图像数量等等。

## 18.如果您模糊地记得这个命令，并且想要确认它，您将如何获得关于这个特定命令的帮助？

以下命令非常有用，因为它可以帮助您了解如何使用命令、语法等。

`$ docker --help`

上面的命令列出了所有 Docker 命令。如果您需要某个特定命令的帮助，可以使用以下语法:

`$ docker <command> --help`

## 19.如何登录 docker 知识库？

您可以使用以下命令登录 hub.docker.com:

`$ docker login`

系统会提示您输入用户名和密码，输入后恭喜您，您已经登录了。

## 20.如果你希望使用一个基本图像，并对其进行修改或个性化，你会怎么做？

您从 docker hub 将一个映像拖到本地系统上

从 docker hub 提取图像的命令很简单:

`$ docker pull <image_name>`

## 21.如何从图像创建 docker 容器？

使用上面的命令从 docker 存储库中提取一个映像，并运行它来创建一个容器。使用以下命令:

`$ docker run -it -d <image_name>`

下一个问题很可能是，命令中的'-d '标志是什么意思？

**-d** 表示集装箱需要在分离模式下启动。解释一下分离模式。看看这个博客，更好地理解不同的 docker 命令。

## 22.如何列出所有正在运行的容器？

以下命令列出了所有正在运行的容器:

`docker ps`

## 23.假设您有 3 个容器在运行，您希望访问其中的一个。你如何访问一个正在运行的容器？

以下命令让我们可以访问正在运行的容器:

`$ docker exec -it <container id> bash`

exec 命令允许您进入容器内部并使用它。

## **24。如何启动、停止和终止一个容器？**

以下命令用于启动 docker 容器:

`$ docker start <container_id>`

下面是停止正在运行的容器的步骤:

`$ docker stop <container_id>`

使用以下命令终止容器:

`$ docker kill <container_id>`

## 25.你能使用容器，编辑它，并更新它吗？此外，如何使它成为新的并存储在本地系统上？

当然，你可以使用一个容器，对它进行编辑和更新。这听起来很复杂，但实际上只是一个命令。

`$ docker commit <conatainer id> <username/imagename>`

## 26.一旦你处理了一个图像，你如何把它推送到 docker hub？

`$ docker push <username/image name>`

## 27.如何删除已停止的容器？

使用以下命令删除停止的容器:

`$ docker rm <container id>`

## 28.如何从本地存储系统中删除映像？

以下命令允许您从本地系统中删除映像:

`$ docker rm <image-id>`

## 29.如何构建 Dockerfile？

一旦编写了 docker 文件，就需要构建它来创建具有这些规范的映像。使用以下命令构建 Dockerfile 文件:

`$ docker build <path to docker file>`

下一个问题是你什么时候使用”。dockerfile_name "以及何时使用完整路径？

用”。dockerfile_name "当 dockerfile 存在于同一个文件目录中，并且如果它存在于其他位置，则使用完整路径。

## 30.你知道为什么使用 docker 系统修剪吗？它是做什么的？

`$ docker system prune`

上面的命令用于删除所有停止的容器、所有不使用的网络、所有悬挂图像和所有构建缓存。这是最有用的 docker 命令之一。

# Docker 高级问题

一旦面试官知道你熟悉 Docker 命令，他/她就会开始问一些实际应用的问题。这部分的 Docker 面试问题包括一些你只有在获得了一些使用 Docker 的经验后才能回答的问题。

## 31.当 docker 容器存在时，您会丢失数据吗？

不，当 Docker 容器退出时，您不会丢失任何数据。应用程序写入容器的任何数据都会保留在磁盘上，直到您显式删除该容器。即使在容器暂停后，容器的文件系统仍然存在。

## 32.你认为 Docker 都用在哪里了？

当被问到这样的问题时，通过谈论 Docker 的应用来回答。Docker 用于以下领域:

*   简化配置:Docker 允许您将环境和配置放入代码中并进行部署。
*   代码管道管理:开发和生产使用不同的系统。随着代码从开发到测试再到生产，它在环境中经历了变化。Docker 有助于维护代码管道的一致性。
*   开发人员生产力:使用 Docker 进行开发给了我们两件事——我们更接近生产，开发环境构建得更快。
*   应用程序隔离:因为容器是用所有依赖关系包装在一起的应用程序，所以你的应用程序是隔离的。他们可以在任何支持 Docker 的硬件上独立工作。
*   调试功能:Docker 支持各种调试工具，这些工具不是特定于容器的，但是可以很好地与容器一起工作。
*   多租户:Docker 让您拥有多租户应用程序，避免代码和部署中的冗余。
*   快速部署:Docker 消除了从头启动整个操作系统的需要，减少了部署时间。

## 33.Docker 与其他集装化方式有何不同？

Docker 容器非常容易在任何云平台中部署。与其他技术相比，它可以在相同的硬件上运行更多的应用程序，使开发人员可以轻松快速地创建现成的容器化应用程序，并使管理和部署应用程序变得更加容易。您甚至可以与您的应用程序共享容器。

如果你有更多的观点要补充，你可以这样做，但要确保你的答案中有以上的解释。

## 34.我可以在 Docker 中使用 JSON 而不是 YAML 编写文件吗

对于您的合成文件，您可以使用 JSON 而不是 YAML，要将 JSON 文件与 compose 一起使用，请指定要使用的 JSON 文件名，例如:

`$ docker-compose -f docker-compose.json up`

## 35.你在以前的职位上是如何使用 Docker 的？

解释你如何使用 Docker 来帮助快速部署。解释你是如何编写 Docker 脚本并与其他工具如 Puppet、Chef 或 Jenkins 一起使用的。如果你过去没有使用 Docker 的实践经验，而是在类似领域有使用其他工具的经验，诚实地解释一下。在这种情况下，如果你能在功能性方面将其他工具与 Docker 进行比较，这是有意义的。

## 36.Docker 容器的规模有多大？一样有什么要求吗？

像 Google 和 Twitter 这样的大型 web 部署，以及 Heroku 和 dotCloud 这样的平台提供商，都运行在容器技术上。容器可以扩展到几十万甚至几百万个并行运行的容器。谈到需求，容器始终需要内存和操作系统，以及在扩展时有效使用这些内存的方法。

## 37.docker 运行在哪些平台上？

这是一个非常简单的问题，但可能会变得棘手。去面试前做一些公司调查，了解公司是如何使用 Docker 的。请确保在回答中提到公司使用的平台。

Docker 运行在各种 Linux 管理系统上:

*   Ubuntu 12.04、13.04 等
*   Fedora 19/20+款
*   RHEL 6.5 以上
*   CentOS 6+
*   巴布亚企鹅
*   ArchLinux
*   openSUSE 12.3 以上
*   症结 3.0 以上

它还可以在生产中与具有以下服务的云平台一起使用:

*   亚马逊 EC2
*   亚马逊 ECS
*   谷歌计算引擎
*   微软 Azure
*   Rackspace

## 38.有办法识别 Docker 容器的状态吗？

一个容器在任一给定时刻可能有六种状态——创建、运行、暂停、重新启动、退出、死亡。

使用以下命令检查任意给定点的 docker 状态:

`$ docker ps`

默认情况下，上面的命令只列出正在运行的容器。要查找所有容器，请使用以下命令:

`$ docker ps -a`

## 39.你能从 Docker 中移除一个暂停的容器吗？

答案是否定的。您不能删除暂停的容器。容器必须处于停止状态才能被移除。

## 40.容器可以自己重启吗？

不，容器不可能自己重启。默认情况下，标志-restart 设置为 false。

## 41.使用 rm 命令直接删除容器好，还是先停止容器再删除容器好？

最好是停止容器，然后使用 remove 命令删除它。

`$ docker stop <coontainer_id>`
`$ docker rm -f <container_id>`

停止容器然后移除它将允许向接收者发送 SIG_HUP 信号。这将确保所有的容器都有足够的时间来清理它们的任务。这种方法被认为是一种很好的做法，可以避免不必要的错误。

## 42.云会取代集装箱化的使用吗？

Docker 容器越来越受欢迎，但与此同时，云服务正在进行一场激烈的战斗。在我个人看来，Docker 永远不会被云取代。使用容器化的云服务肯定会炒作这个游戏。组织需要将他们的需求和依赖性考虑在内，并决定什么最适合他们。大多数公司已经将 Docker 与云集成在一起。这样他们就可以充分利用这两种技术。

## 43.每台主机可以运行多少个容器？

每个主机可以有任意多的容器。Docker 对此没有任何限制。但是你需要考虑每个容器需要硬件支持的存储空间、CPU 和内存。您还需要考虑应用程序的大小。容器被认为是轻量级的，但是非常依赖于主机操作系统。

## 44.在 Docker 上运行有状态的应用程序是一个好的实践吗？

有状态应用程序背后的概念是，它们将数据存储在本地文件系统中。您需要决定将应用程序移动到另一台机器上，检索数据变得很痛苦。老实说，我不喜欢在 Docker 上运行有状态的应用程序。

## 45.假设您有一个拥有许多依赖服务的应用程序。docker-compose 会等待当前容器准备好运行下一个服务吗？

答案是肯定的。Docker-compose 总是按照依赖顺序运行。这些依赖项是规范，如依赖项、链接、卷来源等。

## 46.你将如何在生产中监控 Docker？

Docker 提供了像 docker stats 和 docker events 这样的功能来监控生产中的 docker。Docker stats 提供容器的 CPU 和内存使用情况。Docker 事件提供关于 docker 守护进程中发生的活动的信息。

## 47.在生产中运行 Docker compose 是一种好的做法吗？

是的，在生产中使用 docker-compose 是 docker-compose 最好的实际应用。当您使用 compose 定义应用程序时，您可以在各种生产阶段(如 CI、试运行、测试等)使用这个组合定义。

## 48.将 docker-compose 文件转移到生产环境时，它会有什么变化？

在将应用程序迁移到生产环境之前，您需要对合成文件进行以下更改:

*   移除卷绑定，这样代码就保留在容器内部，不能从容器外部进行更改。
*   绑定到主机上的不同端口。
*   指定重启策略
*   添加额外的服务，如日志聚合器

## 49.你用过 Kubernetes 吗？如果有，你更喜欢 Docker 和 Kubernetes 中的哪一个？

在这类问题上要非常诚实。如果你用过 Kubernetes，说说你用 Kubernetes 和 Docker Swarm 的经历。指出你认为 docker swarm 更高效的关键领域，反之亦然。

你的 docker 面试问题不仅仅局限于 Docker 的变通方法，还有其他类似的工具。因此，要准备好能与码头工人竞争的工具/技术。一个这样的例子是 Kubernetes。

## 50.您知道容器和主机之间的负载平衡吗？它是如何工作的？

在跨不同主机对多个容器使用 docker 服务时，您会遇到对传入流量进行负载平衡的需求。负载平衡和 HAProxy 主要用于平衡不同可用(健康)容器之间的传入流量。如果一个容器崩溃，另一个容器应该自动开始运行，流量应该重新路由到这个新运行的容器。负载平衡和 HAProxy 围绕这个概念工作。

这就把我们带到了 *Docker 面试问题*文章*的结尾。随着商业竞争的加剧，公司已经意识到适应和利用不断变化的市场的重要性。让他们留在游戏中的几件事是更快的系统扩展、更好的软件交付、适应新技术等等。这时，docker 加入进来，给这些公司提供支持，让它们继续竞争。*

如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=docker-interview-questions)

请留意本系列中的其他文章，它们将解释 DevOps 的各个方面。

> *1。* [*DevOps 教程*](/edureka/devops-tutorial-89363dac9d3f)
> 
> *2。* [*Git 教程*](/edureka/git-tutorial-da652b566ece)
> 
> *3。* [*詹金斯教程*](/edureka/jenkins-tutorial-68110a2b4bb3)
> 
> *4。* [*码头工人教程*](/edureka/docker-tutorial-9a6a6140d917)
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
> *13。* [*持续交付 vs 持续部署*](/edureka/continuous-delivery-vs-continuous-deployment-5375642865a)
> 
> *14。* [*CI CD 管道*](/edureka/ci-cd-pipeline-5508227b19ca)
> 
> *15。* [*Docker 作曲*](/edureka/docker-compose-containerizing-mean-stack-application-e4516a3c8c89)
> 
> *16。* [*码头工人群*](/edureka/docker-swarm-cluster-of-docker-engines-for-high-availability-40d9662a8df1)
> 
> *17。* [*Docker 联网*](/edureka/docker-networking-1a7d65e89013)
> 
> *18。* [*易变拱顶*](/edureka/ansible-vault-secure-secrets-f5c322779c77)
> 
> *19。* [*岗位职责*](/edureka/ansible-roles-78d48578aca1)
> 
> 20。 [*可用于 AWS*](/edureka/ansible-for-aws-provision-ec2-instance-9308b49daed9)
> 
> *21。* [*詹金斯管道*](/edureka/jenkins-pipeline-tutorial-continuous-delivery-75a86936bc92)
> 
> *22。* [*顶级 Docker 命令*](/edureka/docker-commands-29f7551498a8)
> 
> *23。*[*Git vs GitHub*](/edureka/git-vs-github-67c511d09d3e)
> 
> *24。* [*顶级 Git 命令*](/edureka/git-commands-with-example-7c5a555d14c)
> 
> *二十五。* [*DevOps 面试问题*](/edureka/devops-interview-questions-e91a4e6ecbf3)
> 
> *二十六。* [*谁是 DevOps 工程师？*](/edureka/devops-engineer-role-481567822e06)
> 
> *27。* [*DevOps 生命周期*](/edureka/devops-lifecycle-8412a213a654)
> 
> *28。*[*Git ref log*](/edureka/git-reflog-dc05158c1217)
> 
> *29。* [*易变条款*](/edureka/ansible-provisioning-setting-up-lamp-stack-d8549b38dc59)
> 
> *30。* [*组织正在寻找的顶尖 DevOps 技能*](/edureka/devops-skills-f6a7614ac1c7)
> 
> *30。* [*瀑布 vs 敏捷*](/edureka/waterfall-vs-agile-991b14509fe8)
> 
> *31。* [*詹金斯小抄*](/edureka/jenkins-cheat-sheet-e0f7e25558a3)
> 
> *32。* [*不可译的备忘单*](/edureka/ansible-cheat-sheet-guide-5fe615ad65c0)
> 
> *33。* [*Ansible 面试问答*](/edureka/ansible-interview-questions-adf8750be54)
> 
> *34。* [*Maven 用于构建 Java 应用*](/edureka/maven-tutorial-2e87a4669faf)
> 
> *35。* [*敏捷方法论*](/edureka/what-is-agile-methodology-fe8ad9f0da2f)
> 
> *36。* [*詹金斯面试问题*](/edureka/jenkins-interview-questions-7bb54bc8c679)
> 
> *37。* [*Git 面试问题*](/edureka/git-interview-questions-32fb0f618565)
> 
> 38。 [*码头建筑*](/edureka/docker-architecture-be79628e076e)
> 
> 39。[*devo PS 中使用的 Linux 命令*](/edureka/linux-commands-in-devops-73b5a2bcd007)
> 
> *四十。* [*詹金斯 vs 竹子*](/edureka/jenkins-vs-bamboo-782c6b775cd5)
> 
> *41。* [*Nagios 教程*](/edureka/nagios-tutorial-e63e2a744cc8)
> 
> 42。 [*Nagios 面试问题*](/edureka/nagios-interview-questions-f3719926cc67)
> 
> 43。 [*DevOps 实时场景*](/edureka/jenkins-x-d87c0271af57)
> 
> 44。 [*詹金斯和詹金斯 X 的区别*](/edureka/jenkins-vs-bamboo-782c6b775cd5)
> 
> *45。*[*Windows Docker*](/edureka/docker-for-windows-ed971362c1ec)
> 
> *46。*[*Git vs Github*](http://git%20vs%20github/)

*原载于*[*https://www.edureka.co*](https://www.edureka.co/blog/interview-questions/docker-interview-questions)*。*