# 了解如何使用 Apache Oozie 调度 Hadoop 作业

> 原文：<https://medium.com/edureka/apache-oozie-tutorial-d8f7bbbe1591?source=collection_archive---------0----------------------->

![](img/871d3a6c8275c163a5a559636be51c43.png)

Oozie Tutorial - Edureka

在开始这篇 Apache Oozie 教程之前，让我们先了解一下调度系统用在哪里。在实时场景中，一个作业依赖于其他作业，例如 MapReduce 任务的输出可能会传递给 Hive job 以供进一步处理。下一个场景可以是，根据时间(如每天、每周、每月)或根据数据可用性安排一组任务。Apache Oozie 为您提供了轻松处理这类场景的能力。这就是为什么 Apache Oozie 是 ***Hadoop 生态系统*** 的重要组成部分。

在这个 Apache Oozie 教程博客中，我们将讨论:

*   阿帕奇 Oozie 游戏攻略
*   Oozie 工作流
*   Oozie 协调员
*   Oozie 包
*   字数统计工作流作业
*   基于时间的字数统计协调员工作

我们将从介绍 Apache Oozie 开始这个 Oozie 教程。接下来，我们将了解可以使用 Apache Oozie 创建和执行的作业类型。

# Apache Oozie 简介

![](img/37051c386f12aa03617daf90594465cb.png)

Apache Oozie 是一个调度系统，用于在分布式环境中管理和执行 Hadoop 作业。我们可以通过组合不同类型的任务来创建所需的管道。它可以是你的 Hive，Pig，Sqoop 或者 MapReduce 任务。使用 Apache Oozie，您还可以安排您的工作。在一个任务序列中，两个或多个作业也可以被编程为彼此并行运行。这是一个可升级的、可靠的和可扩展的系统。

Oozie 是一个开源的 Java web 应用程序，负责触发工作流操作。反过来，它使用 Hadoop 执行引擎来执行任务。

Apache Oozie 通过回调和轮询来检测任务的完成。当 Oozie 启动一个任务时，它向该任务提供一个惟一的回调 HTTP URL，并在任务完成时通知该 URL。如果任务调用回调 URL 失败，Oozie 可以轮询任务是否完成。

Apache Oozie 中有三种类型的作业:

*   **Oozie 工作流作业**——这些是有向无环图(Dag ),指定了要执行的一系列动作。
*   Oozie 协调器作业—这些作业由时间和数据可用性触发的工作流作业组成。
*   **oo zie Bundles**——这些可以被称为多个协调器和工作流任务的包。

现在，让我们逐一了解这些工作。

# Oozie 工作流

工作流是排列在有向无环图(DAG)中的一系列动作。这些操作相互依赖，因为下一个操作只能在当前操作输出后执行。工作流动作可以是 Pig 动作、Hive 动作、MapReduce 动作、Shell 动作、Java 动作等。可以使用决策树来决定作业应该如何运行以及在什么条件下运行。

我们可以根据作业创建不同类型的操作，每种类型的操作都可以有自己的标记类型。在执行工作流之前，应该将工作流和脚本或 jar 放在 HDFS 路径中。

**命令:***oo zie job–oo zie http://localhost:11000/oo zie-config job . properties-run*

要检查作业的状态，您可以到 Oozie web 控制台，即 *http://host_name:11000* 。通过单击作业，您将看到作业的状态。

在我们想要并行运行多个作业的场景中，我们可以使用 *Fork* 。每当我们使用 fork 时，我们都必须使用 Join 作为 fork 的端节点。对于每个分叉，都应该有一个连接。Join 假设所有并行执行的节点都是一个 fork 的子节点。例如，我们可以同时并行创建两个表。

如果我们想基于决策的输出运行一个动作，我们可以添加决策标签。例如，如果我们已经有了 hive 表，我们就不需要再创建它了。在这种情况下，我们可以添加一个决策标记，如果表已经存在，就不运行创建表步骤。决策节点有一个类似于开关案例的开关标签。

可以直接传递作业跟踪器、名称节点、脚本和参数的值。但是，这变得难以管理。这是配置文件(即。属性文件)就派上了用场。

# Oozie 协调员

您可以计划复杂的工作流以及使用 Coordinator 定期计划的工作流。Oozie 协调器基于时间、数据或事件谓词触发工作流作业。当满足给定条件时，作业协调器中的工作流就会启动。

协调员工作所需的定义如下:

*   **开始**—作业的开始日期时间。
*   **结束**—结束作业的日期时间。
*   **时区**—协调器应用程序的时区。
*   **频率**—执行作业的频率，以分钟为单位。

还有一些属性可用于控制信息:

*   **超时**—一个动作在被丢弃之前等待满足附加条件的最长时间，以分钟为单位。0 表示如果在动作具体化时没有满足所有的输入事件，那么动作应该立即超时。-1 表示没有超时，操作将永远等待。默认值为-1。
*   **并发**—一个作业可以并行运行的最大动作数。默认值为 1。
*   **执行**–它指定协调器作业的多个实例满足其执行标准时的执行顺序。它可以是:

1.  FIFO(默认)
2.  后进先出法。比较 FIFO
3.  仅最后一个

***命令:*** *oozie 作业——oo zie http://localhost:11000/oo zie-config<coordinator . properties 文件路径>——运行*

如果在提交协调器作业时没有为作业配置提供定义中使用的配置属性，作业提交将会失败。

# Oozie 包

Oozie Bundle system 允许您定义和执行一组协调器应用程序，通常称为数据管道。在 Oozie 包中，协调器应用程序之间没有明确的依赖关系。但是，您可以使用协调器应用程序的数据依赖性来创建隐式数据应用程序管道。您可以启动/停止/暂停/恢复/重新运行软件包。它提供了更好和更容易的操作控制。

**启动时间**—捆绑包应启动并提交协调器申请的时间。

在本 Apache Oozie 教程中，我们将了解如何创建工作流作业。

# 字数统计工作流作业

在这个例子中，我们将使用 Apache Oozie 执行一个字数统计作业。这里我们不讨论如何编写一个 MapReduce 字数统计程序。因此，在阅读这篇 Apache Oozie 教程之前，您需要下载这个字数统计 jar 文件。现在，创建一个 WordCountTest 目录，我们将在其中放置所有文件。创建一个 lib 目录，我们将在其中放置 word count jar，如下图所示。

![](img/140cb5e24f896759a8aca8d3390fd5dd.png)![](img/421380006318928d165b208de277fbce.png)

现在，让我们继续前进&创建***job . properties***&***workflow . XML***文件，我们将在其中指定作业及其相关参数。

# 作业.属性

首先我们创建一个 *job.properties* 文件，在这里我们定义 NameNode & ResourceManager 的路径。解析工作流目录路径&需要 NameNode 路径，jobTracker 路径将有助于向 YARN 提交作业。我们需要提供 *workflow.xml* 文件的路径，该文件应该存储在 HDFS。

![](img/136505cd169dfb09aa3b6df1b9217c87.png)

# workflow.xml

接下来，我们需要创建 *workflow.xml* 文件，在这里我们将定义并执行所有的动作。首先，我们需要指定工作流应用程序名称，即 *WorkflowRunnerTest* 。然后，我们指定**开始节点**。开始节点*(**开始到*标记 *)* 是工作流作业的入口点。它指向作业应该开始的第一个工作流节点。如下图所示，下一个节点是 ***交叉点 0*** ，作业将从这里开始。**

*接下来，我们将在 action 节点中指定要执行的任务。我们正在执行 MapReduce WordCount 任务。我们需要指定执行这个 MapReduce 任务所需的配置。我们正在定义作业跟踪器& NameNode 地址。*

*接下来是 prepare 元素，它专门用于在执行操作之前清理目录。这里我们在 HDFS 执行删除操作，删除已经创建的 *out1* 文件夹。准备标签用于在执行作业之前创建或删除文件夹。然后，我们指定 MapReduce 属性，如作业队列名称、映射器类、缩减器类、输出键类&输出值类。*

*![](img/7e0e58b183bfe53f2fa901e193f5840a.png)**![](img/9fcfcc87cc702156504ff7175828ac51.png)*

*最后一个 MapReduce 任务配置是 HDFS 的输入和输出目录。输入目录是*数据*目录，存储在 NameNode *的根路径中。*最后，如果作业失败，我们将指定 kill 元素。*

*![](img/0091d5598f0d565432489eb81f3eba43.png)*

*现在我们需要移动 HDFS 的 *WordCountTest* 文件夹，正如我们在 *job.properties* 文件的*oo zie . wf . application . path*属性中指定的那样。因此，我们正在复制 Hadoop 根目录中的 *WordCountTest* 文件夹。*

****命令:***Hadoop fs-put word count test/*

*![](img/98874b90313fe8859f1ba4d0ed92f8da.png)*

*要验证这一点，你可以进入 NameNode Web UI，检查文件夹是否已经上传到 HDFS 根目录。*

*![](img/63f740704a80fb22860e28cf94210fd0.png)*

*现在，我们已经准备好继续前进并执行工作流作业。*

****命令:*** *oozie 作业-oo zie http://localhost:11000/oo zie-config job . properties-run**

*![](img/fbbd323710d3c4a38479470f11c77a7c.png)*

*一旦我们执行了我们的作业，我们将得到一个作业 id(即*0000009–171219160449620-oo zie-edur-W*)，如上图所示。您可以在 Oozie Web UI 中查看您提交的作业，即 *localhost:11000* 。您可以在下图中看到，我们提交的工作已列出。*

*![](img/d14a8ffa640f22b59ece08c956b43945.png)*

*如果您仔细观察上图，您会看到作业 ID、作业名称、作业状态、提交作业的用户、创建时间、开始和最后修改时间。您可以点击工作以获取更多详细信息，例如:*

*   *工作信息*

*![](img/6d74adc8100630646638f5c68f37168b.png)*

*   *作业定义*

*![](img/0f6cd22069fdb328a3df17565406f809.png)*

*   *作业配置*

*![](img/6f6973353ff7daf2c7e0af98db8b3e5c.png)*

*由于作业的状态是成功，所以我们需要转到 HDFS 根目录，检查输出目录是否已经创建。*

*![](img/bff8ab8e26b9b6a06d8ef762d1d10b0a.png)*

*正如您所看到的，已经在 HDFS 中创建了 *oozieout* 目录，现在让我们看看已经创建的输出文件。*

*![](img/127a4628871cea7f78849d9f71edd452.png)*

*我们已经看到了如何创建 Oozie 工作流作业，现在我们将在这个 Apache Oozie 教程博客中进一步了解如何创建协调者作业。*

# *基于时间的字数统计协调员工作*

*在本例中，我们将创建一个基于时间的字数统计协调器作业，该作业将在特定的时间间隔后执行。您可以使用 Apache Oozie 创建和调度需要每天或定期执行的作业。*

*让我们在这个 Apache Oozie 教程中快速前进，并创建一个协调者工作。这里我们将创建三个文件，即 *coordinator.properties* 、*coordinator . XML*&*workflow . XML*文件。同样，这里我们将把 w *ordcount* jar 放在 *lib* 目录中，如下图所示。*

*![](img/03d09645501f951b3f169c955d273f6e.png)*

*现在让我们分别看一下这些文件。首先，我们将从 coordinator.properties 文件开始。*

*![](img/80f2e94d73434b3a4e7924571a6f95e1.png)*

*这里，我们指定了工作流的执行频率。频率总是以分钟表示。在我们的例子中，这个协调器作业将在指定时间内每小时执行一次。Frequency 用于捕获产生数据集的周期性间隔，以及协调器应用程序的运行计划。*

*要以分钟、小时、天、月为单位定义频率，请使用以下格式:*

*![](img/d82ccc4c4c0ab0a0e2d51e71002cd1b6.png)*

*接下来，我们定义作业的开始和结束时间，如上图所示。*开始时间*是作业&的开始日期时间*结束时间*是作业的结束日期时间。*

*接下来，我们将指定 NameNode & ResourceManager URL，这将分别用于引用 HDFS 中的 workflow.xml 文件和向 YARN 提交作业。最后，我们指定 workflow.xml 路径，它将存储在 HDFS。我们还将指定存储所有文件和 lib 目录的应用程序路径。*

*第二个文件是 *coordinator.xml* ，在这里我们将使用在 *coordinator.properties* 文件中指定的所有属性。现在，首先我们将指定协调应用程序的属性，即名称、频率&时区。接下来，我们将逐一指定工作流。在这里，我们只有一个工作流程。因此，在 action 元素中，我们将创建 workflow 元素，并在其中指定应用程序路径。*

*![](img/fcc7f8d9bf0a4b29b9e20dc47be2352d.png)*

*接下来，我们必须创建一个 *workflow.xml* 文件，我们将在其中指定任务。它类似于我们在工作流作业中创建的 *workflow.xml* 文件。*

*![](img/08634390a65a62f8d82d4852bf3ca5a6.png)**![](img/2e348959f38e2410bbdcc89f12ac1ce1.png)*

*现在，我们再次将这个*word count test _ timedbase*目录移动到 HDFS。*

***命令**:*Hadoop fs-put word count test _ time based/**

*![](img/4f397dd448923d8f5d7a3a3548f017bf.png)*

*现在，我们已经准备好继续前进，在 Oozie 教程中执行这个协调器工作。让我们继续执行它。*

***命令** : *oozie 作业–oo zie http://localhost:11000/oo zie-config coordinator . properties-run**

*![](img/d4ff619375e52f23a77b7400d874cd53.png)*

*记下该协调员的工作 id(即 0000010–171219160449620-oo zie-edur-C)。它将帮助你在 Oozie Web UI 中找到你的工作。*

*![](img/66697693519695629d82beef8b54f724.png)*

*您可以在 Oozie Web UI 的 Coordinator Jobs 选项卡中看到列出的作业。类似于工作流作业，我们有作业的名称、状态、用户、频率、开始和结束时间。当您单击某个特定作业时，将会看到该作业的详细信息，如下图所示。*

*   *协调员工作信息*

*![](img/104121eb76f676ea1c2b0bef3b0f9376.png)*

*   *协调员工作定义*

*![](img/e43bff2266b369a5aa618b52f2769039.png)*

*   *协调员工作配置*

*![](img/f9822abad6ec96cb0016f7ee97246f32.png)*

*现在，我们已经查看了不同的选项卡。我们将返回到将创建输出文件夹的 HDFS 根目录。正如您在下图中看到的， *oozieTimeBasedout* 目录已经创建，正如我们在 *workflow.xml* 文件中指定的那样。*

*![](img/ab987f93442838fa939e78490db0efe7.png)*

*现在，让我们看看已经创建的输出文件。*

*![](img/02547298121d84acf6e79efc368656f9.png)*

*我希望这篇文章能给你带来信息和附加值。*

*如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。*

*请留意本系列中解释大数据其他各方面的其他文章。*

> *1. [Hadoop 教程](/edureka/hadoop-tutorial-24c48fbf62f6)*
> 
> *2.[蜂巢教程](/edureka/hive-tutorial-b980dfaae765)*
> 
> *3.[养猪教程](/edureka/pig-tutorial-2baab2f0a5b0)*
> 
> *4.[地图缩小教程](/edureka/mapreduce-tutorial-3d9535ddbe7c)*
> 
> *5.[h 基础教程](/edureka/hbase-tutorial-bdc36ab32dc0)*
> 
> *6. [HDFS 教程](/edureka/hdfs-tutorial-f8c4af1c8fde)*
> 
> *7. [Hadoop 3](/edureka/hadoop-3-35e7fec607a)*
> 
> *8. [Sqoop 教程](/edureka/apache-sqoop-tutorial-431ed0af69ee)*
> 
> *9.[水槽教程](/edureka/apache-flume-tutorial-6f7150210c76)*
> 
> *10.[大数据教程](/edureka/big-data-tutorial-b664da0bb0c8)*
> 
> *11. [Hadoop 生态系统](/edureka/hadoop-ecosystem-2a5fb6740177)*
> 
> *12.[HQL 顶级配置单元命令及示例](/edureka/hive-commands-b70045a5693a)*
> 
> *13. [Hadoop 集群搭配亚马逊 EMR？](/edureka/create-hadoop-cluster-with-amazon-emr-f4ce8de30fd)*
> 
> *14.[大数据工程师简历](/edureka/big-data-engineer-resume-7bc165fc8d9d)*
> 
> *15. [Hadoop 开发人员-工作趋势和薪水](/edureka/hadoop-developer-cc3afc54962c)*
> 
> *16. [Hadoop 面试问题](/edureka/hadoop-interview-questions-55b8e547dd5c)*

**原载于 2017 年 12 月 27 日*[*www.edureka.co*](https://www.edureka.co/blog/apache-oozie-tutorial/)*。**