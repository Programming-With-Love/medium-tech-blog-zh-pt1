# 如何运行 Hive 脚本？

> 原文：<https://medium.com/edureka/how-to-run-hive-scripts-53b052501e92?source=collection_archive---------1----------------------->

![](img/def986255ed3d64e5c522a4c79de69a9.png)

作为建立在 Hadoop 之上的数据仓库包，Apache Hive 越来越多地用于数据分析、数据挖掘和预测建模。组织正在寻找牢牢掌握 ***蜂巢& Hadoop 技能*** 的专业人士。

在这篇文章中，让我们看看如何运行 Hive 脚本。一般来说，我们使用脚本一次执行一组语句。Hive 脚本的使用方式非常相似。这将减少我们手动编写和执行每个命令的时间和精力。

Hive 0.10.0 及以上版本支持 Hive 脚本。由于配置单元 0.90 版本安装在 CDH3 中，我们无法在 CDH3 中运行配置单元脚本。您可以在 CDH4 中尝试以下步骤，因为其中安装了 Hive 0.10.0 版本。你知道如何创建一个配置单元脚本吗？如果没有，请点击此处获得更多澄清。

现在，让我们看看如何在 Hive 中编写脚本并在 CDH4 中运行它们:

# 步骤 1:编写 Hive 脚本。

要编写配置单元脚本，文件应该与一起保存。sql 扩展。在 Cloudera CDH4 发行版中打开一个终端，给出以下命令来创建一个 Hive 脚本。
命令: sudo gedit sample.sql

在执行上述命令时，它将打开包含需要执行的所有 Hive 命令列表的文件。

在这个脚本中，将创建和描述一个表，并从表中加载和检索数据。

**1。在配置单元中创建表:**

*命令:*创建表产品(productid: int，productname: string，price: float，category: string)行格式分隔字段，以'，'终止；

这里，product 是表名，{ productid，productname，price，category}是该表的列。

以'，'结尾的字段表示输入文件中的列由符号'，'分隔。

默认情况下，输入文件中的记录由新行分隔。

**2。描述表格:**

*命令:*描述产品；

## 3.将数据加载到表中。

要将数据加载到表中，首先我们需要创建一个输入文件，其中包含需要插入到表中的记录。

让我们创建一个输入文件。

命令: sudo gedit input.txt

编辑文件中的内容，如图所示。

**4。正在检索数据:**

为了检索数据，使用 select 命令。

*命令:*从产品中选择*；

上面的命令用于检索表中所有列的值。该脚本应该像下图所示。

# 步骤 2:运行配置单元脚本

以下是运行配置单元脚本的命令:

*命令:*hive–f/home/cloud era/sample . SQL

执行脚本时，请确保脚本文件位置的完整路径存在。

我们可以看到所有命令都成功执行了。

这就是在 CDH4 中运行和执行 Hive 脚本的方式。

Hive 是 Hadoop 的关键组件，您在 Hive 方面的专业知识可以让您获得高薪的 Hadoop 工作！Edureka 有专门策划的 Hadoop 课程，帮助你掌握 MapReduce、Yarn、Pig、Hive、HBase、Oozie、Flume 和 Sqoop 等概念。单击下面的按钮开始。

如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中解释大数据其他各方面的其他文章。

> *1。* [*Hadoop 教程*](/edureka/hadoop-tutorial-24c48fbf62f6)
> 
> *2。* [*蜂巢教程*](/edureka/hive-tutorial-b980dfaae765)
> 
> *3。* [*养猪教程*](/edureka/pig-tutorial-2baab2f0a5b0)
> 
> *4。* [*地图缩小教程*](/edureka/mapreduce-tutorial-3d9535ddbe7c)
> 
> *5。* [*HBase 教程*](/edureka/hbase-tutorial-bdc36ab32dc0)
> 
> *6。* [*HDFS 教程*](/edureka/hdfs-tutorial-f8c4af1c8fde)
> 
> *7。* [*Hadoop 3*](/edureka/hadoop-3-35e7fec607a)
> 
> *8。* [*Sqoop 教程*](/edureka/apache-sqoop-tutorial-431ed0af69ee)
> 
> *9。* [*水槽教程*](/edureka/apache-flume-tutorial-6f7150210c76)
> 
> *10。* [*Oozie 教程*](/edureka/apache-oozie-tutorial-d8f7bbbe1591)
> 
> *11。* [*Hadoop 生态系统*](/edureka/hadoop-ecosystem-2a5fb6740177)
> 
> *12。*[*HQL 顶级蜂巢命令与示例*](/edureka/hive-commands-b70045a5693a)
> 
> *13。* [*Hadoop 集群搭配亚马逊 EMR？*](/edureka/create-hadoop-cluster-with-amazon-emr-f4ce8de30fd)
> 
> *14。* [*大数据工程师简历*](/edureka/big-data-engineer-resume-7bc165fc8d9d)
> 
> *15。*[*【Hadoop 开发者-工作趋势与薪资*](/edureka/hadoop-developer-cc3afc54962c)
> 
> 16。 [*大数据教程*](/edureka/big-data-tutorial-b664da0bb0c8)

*原载于 2020 年 4 月 30 日 https://www.edureka.co**[*。*](https://www.edureka.co/blog/kafka-streams/)*