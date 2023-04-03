# Docker 网络——探索容器如何相互通信

> 原文：<https://medium.com/edureka/docker-networking-1a7d65e89013?source=collection_archive---------1----------------------->

![](img/9ed0410eaa308d93eeeeb5a2385212f5.png)

在当今世界，企业已经变得热衷于集装箱化，这需要强大的网络技能来适当地配置集装箱架构，因此，这引入了 Docker 网络的概念。

在这篇关于 Docker Networking 的博客中，您将了解以下主题:

*   Docker 是什么？
*   Docker 中的网络
*   Docker 网络的目标
*   集装箱网络模型
*   网络驱动程序
*   亲自动手

# Docker 是什么？

要理解 Docker，您需要了解应用程序以前是如何部署的历史，以及现在如何使用容器部署应用程序。

![](img/97466490a51de95261ea4f2304e5b0d6.png)

正如您在上面的图表中看到的，旧方法在主机上有应用程序。因此，n 个应用程序共享该操作系统中的库。但是，随着容器化，操作系统将有一个内核，这是所有应用程序之间唯一通用的东西。因此，应用程序不能访问彼此的库。

因此，简单来说，Docker 是一个用于开发、运输和运行应用程序的开放平台，使用户能够借助容器将应用程序从基础设施中分离出来，从而快速交付软件。

那么，这些容器在各种情况下是如何相互通信的呢？

嗯，那是通过 Docker 网络。

# Docker 网络

在我深入研究 Docker 网络之前，让我向你展示一下 Docker 的工作流程。

![](img/f6422e6bf3a72ef42b8ecacc5253f6d3.png)

正如你在上图中看到的。开发者在易于编写的 Docker 文件中编写规定应用需求或依赖性的代码，并且该 Docker 文件产生 Docker 图像。因此，特定应用程序所需的任何依赖关系都会出现在此映像中。

现在，Docker 容器只不过是 Docker 映像的运行时实例。这些图像被上传到 Docker Hub(Docker 图像的 Git 存储库),其中包含公共/私有存储库。

因此，从公共存储库中，你也可以提取你的图像，你可以把你自己的图像上传到 Docker Hub 上。然后，从 Docker Hub，各种团队(如质量保证或生产团队)将提取该图像并准备他们自己的容器。这些单独的容器通过网络相互通信以执行所需的操作，这就是 Docker 网络。

> 因此，您可以将 Docker 网络定义为一个通信通道，所有隔离的容器通过它在各种情况下相互通信，以执行所需的操作。

你认为 Docker 网络的目标是什么？

# Docker 网络的目标

![](img/220a0ef04ea91769383e8c940a1014f9.png)

> **灵活性** — Docker 通过支持各种平台上任意数量的应用程序相互通信来提供灵活性。
> 
> **跨平台** —在 Docker 集群的帮助下，Docker 可以很容易地用于跨平台，跨各种服务器工作。
> 
> **可扩展性** — Docker 是一个完全分布式的网络，支持应用程序在确保性能的同时单独增长和扩展。
> 
> **分散式** — Docker 使用分散式网络，这使得应用程序能够分散且高度可用。如果您的资源池中突然缺少一个容器或一个主机，您可以调用额外的资源或者转移到仍然可用的服务。
> 
> **用户友好型**——Docker 使服务的自动化部署变得容易，使它们在日常生活中易于使用。
> 
> **支持** — Docker 提供开箱即用的支持。因此，使用 Docker 企业版并获得所有功能的能力非常简单明了，使得 Docker 平台非常易于使用。

为了实现上述目标，您需要一种称为容器网络模型的东西。

# 集装箱网络模型(CNM)

在我告诉你到底什么是容器网络模型之前，让我简单介绍一下在你理解 CNM 之前需要用到的 Libnetwork。

Libnetwork 是一个开源的 Docker 库，它实现了构成 CNM 的所有关键概念。

![](img/8d34d2542f7f77c54b193aa077a410c4.png)

因此，**容器网络模型(CNM)** 标准化了使用多个网络驱动程序为容器提供网络所需的步骤。CNM 需要像控制台一样的分布式键值存储来存储网络配置。

CNM 有 IPAM 插件和网络插件的接口。

IPAM 插件 API 用于创建/删除地址池和分配/解除分配容器 IP 地址，而网络插件 API 用于创建/删除网络和从网络添加/移除容器。

CNM 主要建立在 5 个对象上:网络控制器、驱动程序、网络、端点和沙箱。

# 集装箱网络模型对象

> **网络控制器:**提供 Libnetwork 的入口点，为 Docker 引擎提供简单的 API 来分配和管理网络。由于 Libnetwork 支持多个内置和远程驱动程序，网络控制器使用户能够将特定的驱动程序连接到给定的网络。
> 
> **驱动程序:**拥有网络，并负责通过让多个驱动程序参与来管理网络，以满足各种用例及部署场景。
> 
> **网络:**提供属于同一网络并与其他网络隔离的一组端点之间的连接。因此，每当创建或更新网络时，相应的驱动程序都会收到事件通知。
> 
> **端点:**为网络中容器公开的服务提供与网络中其他容器提供的其他服务的连接。端点代表一个服务，而不一定是一个特定的容器，端点在集群中也具有全局范围。
> 
> **沙箱:**当用户请求在网络上创建端点时创建。一个沙箱可以有多个端点连接到不同的网络，代表容器的网络配置，如 IP 地址，MAC 地址，路由，DNS。

这就是 CNM 的五个主要目标。

现在，让我告诉你 Docker 网络中涉及的各种网络驱动程序。

# 网络驱动程序

主要有 5 种网络驱动程序:网桥、主机、无、覆盖、Macvlan

**网桥:**这是由主机上的 docker 创建的私有默认内部网络。因此，所有容器都有一个内部 IP 地址，并且这些容器可以使用这个内部 IP 互相访问。当您的应用程序在需要通信的独立容器中运行时，通常使用桥接网络。

![](img/66d80e7456bad298a8ea676925dc1108.png)

**Host** :这个驱动去掉了 docker 主机和 docker 容器之间的网络隔离，直接使用主机的网络。这样，你就不能在同一个主机、同一个端口上运行多个 web 容器，因为这个端口现在是主机网络中所有容器的公共端口。

![](img/131a7d6f239cedfe6cbdaff3e7362d62.png)

**无**:在这种网络中，容器不附属于任何网络，并且不能访问外部网络或其他容器。因此，当您想要完全禁用容器上的网络堆栈，并且仅创建环回设备时，可以使用此网络。

![](img/dc5d22e73ca06879e1f1adbbc370cc79.png)

**覆盖**:创建一个内部私有网络，跨越所有参与群集群的节点。因此，覆盖网络促进了群服务和独立容器之间的通信，或者不同 Docker 守护进程上的两个独立容器之间的通信。

![](img/af243f5a44a365eaa04106a2ae9847b7.png)

**Macvlan:** 允许您为容器分配 MAC 地址，使其在您的网络上显示为物理设备。然后，Docker 守护进程根据容器的 MAC 地址将流量路由到容器。当您希望直接连接到物理网络，而不是通过 Docker 主机的网络堆栈路由时，Macvlan 驱动程序是最佳选择。

![](img/21cf0fca282c451e9abe4ba7c3a18ddc.png)

好了，这就是理解 Docker 网络所需的全部理论。现在，让我继续向您实际展示网络是如何创建的，容器是如何相互通信的。

# 亲自动手

因此，假设你们所有人都在系统上安装了 Docker，我有一个场景要展示。我希望你已经安装了 Docker。

> 假设您想要存储课程名称和课程 ID，为此您需要一个 web 应用程序。基本上，你需要一个 web 应用程序容器，你还需要一个后端 MySQL 容器，MySQL 容器应该链接到 web 应用程序容器。

我实际执行上面的例子怎么样。

# 涉及的步骤:

*   初始化 Docker Swarm 以形成 Swarm 集群。
*   创建叠加网络
*   为 web 应用程序和 MySQL 创建服务
*   通过网络连接应用程序

我们开始吧！

**第一步:**在机器上初始化 Docker Swarm。

```
docker swarm init --advertise-addr 192.168.56.101
```

![](img/072d005a448f905df0862a664fce7cea.png)

–advertise-addr 标志将管理中心节点配置为将其地址发布为 192.168.56.101。群中的其他节点必须能够通过 IP 地址访问管理器。

**步骤 2:** 现在，如果你想把这个管理节点加入到工作者节点，复制你在工作者节点上初始化 swarm 时得到的链接。

![](img/a05cf28163ca5f98de0ab0bdfe771322.png)

**第三步:**创建叠加网络。

```
docker network create -d overlay myoverlaynetwork
```

![](img/17a273659644cca314857615845e0319.png)

其中 myoverlay 是网络名称，而-d 允许 Docker 守护进程在后台运行。

**步骤 4.1:** 创建服务 webapp1，并使用您已经创建的网络在 swarm 集群上部署该服务。

```
docker service create --name webapp1 -d --network myoverlaynetwork -p 8001:80 hshar/webapp
```

![](img/d5909058bba261e97ac657f7fe1d76f7.png)

其中-p 表示端口转发，hshar 是 Docker Hub 上的帐户名称，webapp 是 Docker Hub 上已经存在的 web 应用程序的名称。

**步骤 4.2:** 现在，检查服务是否已创建。

```
docker service ls
```

![](img/853c0532eab1be7f3929746281ca5633.png)

**步骤 5.1:** 现在，创建一个服务 MySQL，并使用您创建的网络在 swarm 集群上部署该服务。

```
docker service create --name mysql -d --network myoverlaynetwork -p 3306:3306 hshar/mysql:5.5
```

![](img/89222c0cbb6b79c52f8f2e59f73cdd4b.png)

**步骤 5.2:** 现在，检查服务是否已创建。

```
docker service ls
```

![](img/07e096fede05a8b3b8d2533b410ded98.png)

**步骤 6.1:** 之后，检查哪个容器正在您的主节点上运行，并进入 hshar/webapp 容器。

```
docker ps
```

![](img/82b95426f8ed1770b20ed2d3fa3ee591.png)

**步骤 6.2:** 所以，你可以看到 manager 节点上只有 webapp 服务。因此，进入 webapp 容器。

```
docker exec -it container_id bash 
nano var/www/html/index.php
```

![](img/122ff8a595587931e0da8b15ca9f2804.png)

docker ps 命令将列出两个容器及其各自的容器 id。第二个命令将在交互模式下启用该容器。

**第 7 步:**现在，将$servername 从 localhost 更改为 mysql，将$password 从"""更改为" edureka "，同时更改所有需要填写的数据库详细信息，并使用键盘快捷键 Ctrl+x 保存您的 index.php 文件，之后使用 y 保存，并按 enter 键。

![](img/1a49406b639fb8aaac330bac0043136a.png)

步骤 8: 现在，进入运行在另一个节点上的 MySQL 容器。

```
docker exec -it container_id bash
```

![](img/03355f8e691a52b82b0b4399e8d194d4.png)

**步骤 9:** 进入 mysql 容器后，输入以下命令使用 MySQL 中的数据库。

**步骤 9.1:** 获得使用 mysql 容器的权限。

```
mysql -u root -pedureka
```

其中-u 代表用户，而-p 是您的机器的密码。

**步骤 9.2:** 在 mysql 中创建一个数据库，用于从 webapp1 中获取数据。

```
CREATE DATABASE HandsOn;
```

![](img/ae8d130b978423259c86a37a1a1d0747.png)

**步骤 9.3:** 使用创建好的数据库。

```
USE HandsOn;
```

![](img/d638ab3d141ca5dc263a224e857eed3f.png)

**步骤 9.4:** 在该数据库中创建一个表，该表将用于从 webapp1 获取数据。

```
CREATE TABLE course_details (course_name VARCHAR(10), course_id VARCHAR(11));
```

![](img/56b33e26187892bba4927bbc5b7148b1.png)

**步骤 9.5:** 现在，使用命令 **exit** 退出 MySQL 和 container。

**第十步:**进入你的浏览器，输入地址为**localhost:8001/index . PHP**。这将打开您的 web 应用程序。现在，输入课程的详细信息并点击**提交查询**。

![](img/12d42dfb02c6eb810e18735364a02e04.png)

**步骤 11:** 单击提交查询后，转到运行 MySQL 服务的节点，然后进入容器。

```
docker exec -it container_id bash
mysql -u root -pedureka
USE HandsOn;
SHOW tables;
select * from course_details;
```

这将向您显示所有课程的输出，其中您已经填写了详细信息。

![](img/bbcb89b29a70352a8fd20d93e92a59f5.png)

在这里，我结束了我的 Docker 网络博客。我希望你喜欢这篇文章。

如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=docker-networking)

请留意本系列中的其他文章，它们将解释 DevOps 的各个方面。

> *1。* [*DevOps 教程*](/edureka/devops-tutorial-89363dac9d3f)
> 
> *2。* [*饭桶教程*](/edureka/git-tutorial-da652b566ece)
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
> 17。 [*可替代角色*](/edureka/ansible-roles-78d48578aca1)
> 
> 18。[](/edureka/ansible-vault-secure-secrets-f5c322779c77)
> 
> **19。* [*适用于 AWS*](/edureka/ansible-for-aws-provision-ec2-instance-9308b49daed9)*
> 
> **20。* [*詹金斯管道*](/edureka/jenkins-pipeline-tutorial-continuous-delivery-75a86936bc92)*
> 
> **21。* [*顶级 Git 命令*](/edureka/git-commands-with-example-7c5a555d14c)*
> 
> **22。* [*顶级 Docker 命令*](/edureka/docker-commands-29f7551498a8)*
> 
> **23。*[*Git vs GitHub*](/edureka/git-vs-github-67c511d09d3e)*
> 
> **24。* [*DevOps 面试问题*](/edureka/devops-interview-questions-e91a4e6ecbf3)*
> 
> **25。* [*谁是 DevOps 工程师？*](/edureka/devops-engineer-role-481567822e06)*
> 
> **26。* [*DevOps 生命周期*](/edureka/devops-lifecycle-8412a213a654)*
> 
> **27。*[*Git ref log*](/edureka/git-reflog-dc05158c1217)*
> 
> **28。*[](/edureka/ansible-provisioning-setting-up-lamp-stack-d8549b38dc59)*
> 
> ***29。* [*组织寻找的顶尖 DevOps 技能*](/edureka/devops-skills-f6a7614ac1c7)**
> 
> ***三十。* [*瀑布 vs 敏捷*](/edureka/waterfall-vs-agile-991b14509fe8)**
> 
> **31。 [*Maven 用于构建 Java 应用*](/edureka/maven-tutorial-2e87a4669faf)**
> 
> **32。 [*詹金斯小抄*](/edureka/jenkins-cheat-sheet-e0f7e25558a3)**
> 
> ***33。*[](/edureka/ansible-cheat-sheet-guide-5fe615ad65c0)**
> 
> ****34。* [*Ansible 面试问答*](/edureka/ansible-interview-questions-adf8750be54)***
> 
> **35。 [*50 码头工人面试问题*](/edureka/docker-interview-questions-da0010bedb75)**
> 
> ***三十六。* [*敏捷方法论*](/edureka/what-is-agile-methodology-fe8ad9f0da2f)**
> 
> **37。 [*詹金斯面试问题*](/edureka/jenkins-interview-questions-7bb54bc8c679)**
> 
> ***38。* [*Git 面试问题*](/edureka/git-interview-questions-32fb0f618565)**
> 
> ***39。* [*Docker 架构*](/edureka/docker-architecture-be79628e076e)**
> 
> **40。 [*Linux 中使用的命令 DevOps*](/edureka/linux-commands-in-devops-73b5a2bcd007)**
> 
> ***41。* [*詹金斯 vs 竹子*](/edureka/jenkins-vs-bamboo-782c6b775cd5)**
> 
> ***42。* [*Nagios 面试问题*](/edureka/nagios-interview-questions-f3719926cc67)**
> 
> ***43。* [*DevOps 实时场景*](/edureka/jenkins-x-d87c0271af57)**
> 
> ***44。* [*詹金斯和詹金斯 X 的区别*](/edureka/jenkins-vs-bamboo-782c6b775cd5)**
> 
> ***45。*[*Windows Docker*](/edureka/docker-for-windows-ed971362c1ec)**
> 
> ***46。*[*Git vs Github*](http://git%20vs%20github/)**

***原载于 2018 年 8 月 9 日*[*www.edureka.co*](https://www.edureka.co/blog/docker-networking/)*。***