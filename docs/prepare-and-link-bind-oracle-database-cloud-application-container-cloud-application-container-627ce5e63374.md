# 准备并链接绑定 Oracle 数据库云、应用容器云、应用容器缓存和事件中心

> 原文：<https://medium.com/oracledevs/prepare-and-link-bind-oracle-database-cloud-application-container-cloud-application-container-627ce5e63374?source=collection_archive---------0----------------------->

我经常一起使用的 Oracle 公共云服务组合(例如用于实施微服务的组合)是 DBaaS、应用容器云、应用[容器]缓存和事件中心。

在本文中，我展示了几天前我在 Oracle 公共云控制台中完成的一系列步骤，为我在卡萨布兰卡的摩洛哥 Devoxx 上的演示准备了一个演示环境。或者，我可以使用命令行 [*psm*](https://docs.oracle.com/en/cloud/paas/java-cloud/pscli/abouit-paas-service-manager-command-line-interface.html) 工具和一些简单的脚本来创建云环境。我着手创建的设置看起来像这样:

![](img/367f8e644b9a3517bb41332f0fd40934.png)

几个节点应用程序将在应用程序容器云上运行。每个都将有服务绑定到同一个应用程序容器缓存(一个由 Oracle Coherence 提供支持的黑盒子)，一个 Oracle 数据库实例中的指定可插拔数据库中的特定模式和[一个特定主题]事件中心(内部有 Apache Kafka)。应用容器云上的应用需要公共 ip 地址，事件中心上的主题也是如此。在这个演示中，为了方便起见，我还希望能够通过公共 IP 地址从我的笔记本电脑上直接访问数据库。

本文将展示提供以下云服务的步骤，并展示如何从公共互联网访问网络:

*   [数据库云](http://cloud.oracle.com/database)
*   [应用容器云](http://cloud.oracle.com/application-container-cloud)
*   [活动中心](http://cloud.oracle.com/event-hub)
*   应用容器缓存(应用容器云的一部分)

本文还展示了如何为应用程序容器云上的应用程序创建到缓存、事件中心和 DBaaS 实例的服务绑定。

# 数据库云

![](img/ab94706563bede2a3a7ae82e36ab197f.png)![](img/860996c68471b0d78dd00809f1589062.png)

过了一段时间，我收到一封电子邮件，通知我数据库实例的创建已经完成:

![](img/6d57dfca8ea5d12147a2ed3acc759f23.png)

这反映在服务控制台中:

![](img/fdd5c9d070a52bbcf07fe768c3eb4999.png)

我可以深入了解服务实例

![](img/9a41177dd1b3b74a72780c4a623687c6.png)

查看细节——比如已经分配的 150 GB 存储空间(对于我的小演示环境来说有点多)和已经分配的公共 IP 地址

为了在端口 1521 上启用从公共互联网到数据库的网络访问，我需要启用网络访问规则。

![](img/04a660762eb8e76b69f5e2dffcc41a48.png)

在这种情况下，启用端口 1521 的访问规则:

![](img/42894fd40738192ea0d7ed29e6b1cd17.png)![](img/b4e1dbc78b23a03c7f3182c467a3dab9.png)

此时，我可以从运行在我笔记本电脑上的 SQL Developer 中检查数据库云实例是否确实可以访问:

请注意服务名的组成，包括数据库名、身份域名和固定字符串 oraclecloud.internal。

类似地，通过 SQL 连接表明云中的数据库已经启动并运行，可以访问:

![](img/9e61fe8bea060ae3e3eadb287aacac20.png)

# 应用容器云

我为节点运行时创建了一个应用程序[容器]:

![](img/94af94aecf4fe76a2b814892ca43b6cb.png)

在本例中，我上传了一个准系统节点应用程序——打包在一个 zip 文件 order-ms.zip 中——以及一个 manifest.json 文件，该文件描述了几件事情，其中包括如何运行这个应用程序(“node order-ms.js”)

![](img/7f96ce2d28dd3910ed6341511d44490e.png)

上传完成后，应用程序的创建就开始了

![](img/2fded47da9d45f2175b8a99269525b9a.png)![](img/c50ca01eae85e28a893ce423c62e4acb.png)

现在，创建工作已经完成:

![](img/7391babe4be41169c05c3efbf5183aab.png)

并且可以检查应用程序的当前状态:

![](img/57462b390b370716b164c83dfaadc69f.png)

应用程序的 URL 已被分配，可供公众访问:

![](img/62231293fc03929d7f842cb79ddb2427.png)

我可以对 order-ms 应用程序进行测试调用:

![](img/3f9b01b831380208c11ca8685cb349bd.png)

为了方便地从应用程序容器中访问日志记录(写入我的存储云环境),我使用 Cloudberry for OpenStack(参见博客文章[Oracle Storage Cloud Service 之上的图形文件浏览器工具——Cloud berry for easy file inspection and manipulation](https://technology.amis.nl/2016/05/15/graphical-file-explorer-tool-on-top-of-oracle-storage-cloud-service-cloudberry-for-easy-file-inspection-and-manipulation/)),它支持 Oracle Storage Cloud:

现在可以在桌面工具中访问日志文件:

![](img/7c699bd3a9bee2a37685270751361403.png)

此时，我们已经完成了环境设置的一半:

![](img/30752c54bceb5f79d863ac8ec30473ab.png)

接下来是从应用程序容器到 DBaaS 的服务绑定，用于将数据库访问细节注入到节点应用程序中，而不必在节点应用程序本身中显式硬编码数据库名称、网络地址和其他访问细节。服务绑定为这些应用程序可以简单读取的细节产生环境变量。

![](img/740abda396dd1903a49088066586b879.png)![](img/b979f7a3b51161099c3b1b5494c2e769.png)

必须重新启动应用程序，才能应用服务绑定或环境变量中的更改。

# 活动中心和主题

这篇早期的博客文章— [从本地节点 Kafka 客户端](https://technology.amis.nl/2017/09/21/setting-up-oracle-event-hub-apache-kafka-cloud-service-and-pub-sub-from-local-node-kafka-client/)设置 Oracle Event Hub (Apache Kafka)云服务和 Pub&Sub—详细描述了 Event Hub 平台服务的供应。在这种特殊情况下，我使用其中一个预定义的堆栈，通过 Oracle Cloud Stack Manager 创建了一个包含事件中心和大数据计算云的环境:

![](img/87c83c5e0fc6ee37ba7ca063094f3908.png)![](img/8263c626bd95099662badaf935527911.png)![](img/539ddb817a8458ea42950475910b7465.png)

不幸的是，这个堆栈的创建运行了很多个小时(超过 7 个小时)，最终失败了。然而，它提供了事件中心平台——一个非常大的 Apache Kafka 集群。

![](img/f224d2c92cf02ee3e158c8b638dd2059.png)![](img/50466d47acb8481ee9971121fae48f25.png)

对我来说，下一步是在这个平台上创建一个主题，并允许从公共互联网访问该平台，以便 Kafka 客户可以发布和消费该主题。

Zookeeper 端口(2181)和 Kafka 服务器端口(6667)需要访问规则。

![](img/c66440ca0e2eeb3b751d42e4235c588f.png)![](img/3c4d54c81948eaedddf8e4b93b1d8dcf.png)

接下来，创建一个主题 devoxx-topic:

![](img/80074f6e88bb4d10e44c0d89368ba583.png)![](img/60800ad3cc6424609f12e5ca3252d783.png)![](img/b9d681da683344c0e82f33112666ef5f.png)![](img/99c6b52995627456954ab96ca0871850.png)

新主题的详细信息——包括其完全限定的名称(包括身份域名，这意味着某种形式的多租户正在幕后进行)

![](img/92f48c5459586286a2927821314b8f04.png)

这就是我们现在所处的环境:

![](img/803c3b569e809096b2a51feac8ec78c5.png)

下一步是创建从应用程序容器云上的 order-ms 应用程序到事件中心实例的服务绑定:

![](img/5ed2105a33c12cb15fd73d9e0d15af03.png)![](img/5a37e914bd5d16272fcd708ecb9ccafe.png)

现在，基于这个服务绑定，通过几个额外的环境变量，可以在节点应用程序中获得事件中心的详细信息:

![](img/5179cc2a14397fe68527a3265fbcf73a.png)

# 应用程序容器缓存

最后需要的服务是应用程序容器缓存——一个内存网格，充当非常快速的键值存储。使用简单的 HTTP 调用，我们可以将东西(比如 JSON 文档、简单值、图像和其他二进制块)放在缓存中，以便在多个请求、一个应用程序的多个实例以及多个应用程序之间进行安全保存。在我们的无状态微服务架构中，缓存可用于保存最接近会话状态或应用状态的内容。

![](img/80419b4b6898a82ffe5f5a6c0c311a44.png)

现在缓存已经准备好了:

![](img/2c5811f6af00eee7558cc3ecfe565733.png)

注意:不能在 Oracle 公共云中的身份域之外直接访问缓存。事实上，只有运行在应用程序容器云上的应用程序才能访问这个缓存。

首次创建应用程序时，可以创建从应用程序容器云上的应用程序绑定到缓存的服务:

![](img/f16166a67510ba25e2748e73c4f61908.png)

或者以更平常的方式。

这意味着现在我们需要的环境已经完全配置和绑定，并且可以在其上运行我们的服务。

*原载于 2017 年 11 月 19 日*[*technology . amis . nl*](https://technology.amis.nl/2017/11/19/prepare-and-link-bind-oracle-database-cloud-application-container-cloud-application-container-cache-and-event-hub/)*。*