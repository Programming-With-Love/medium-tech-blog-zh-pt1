# airpal:PrestoDB 的 Web 用户界面

> 原文：<https://medium.com/airbnb-engineering/airpal-a-web-based-query-execution-tool-for-data-analysis-33c43265ed1f?source=collection_archive---------0----------------------->

作者:詹姆斯·梅菲尔德

![](img/f31a08bbd366054f3477aeb6a0a733b4.png)

Airbnb 很高兴地宣布推出 Airpal，这是一款基于网络的查询执行工具，利用脸书的 PrestoDB 来促进数据分析。

花时间使用 SQL 进行探索和研究的人都知道，工作流并不总是一帆风顺的。记住一个查询是如何编写的，从命令行复制和粘贴，以及运行多个终端窗口会减慢分析速度，并且令人沮丧。此外，当不同的团队使用 SQL 进行分析时，初学者的学习曲线可能会很陡，因此好的 UI 工具可以帮助推动采用和促进知识共享。

在 Airbnb，我们大约一年前在内部推出了 Airpal，现在超过 1/3 的员工通过该工具发出了查询。这是一个令人震惊的统计数据，它显示了 Presto 对于我们公司的数据基础架构是多么不可或缺。

我们目前在 HDFS 拥有大约 1 . 5 Pb 的数据作为 Hive 管理的表，我们重要的“core_data”表的相对较小的数据大小允许我们使用 Presto 作为默认的查询引擎进行分析。当运行特殊查询和迭代分析步骤时，Presto 比传统的 map reduce 作业更快、响应更快。然而，将 Presto 添加到我们的基础设施堆栈中的最大好处是，我们不必增加额外的复杂性来允许“交互式”查询。因为我们是针对我们的一个中心 Hive 仓库进行查询的，所以我们可以保留一个“真实的单一来源”,而不需要大规模复制到单独的存储/查询层。此外，我们不需要改变 RC 格式的数据存储类型就可以看到速度的提高，这使得 Presto 成为我们基础设施的绝佳选择。

我们很高兴与开源社区分享这个工具，我们希望它能为其他人提供类似的实用工具。

![](img/a56032ab3b14b63cb6085fbb2ab6879b.png)

# Airpal 的主要特点

*   用户的可选访问控制
*   搜索和查找表格的能力
*   请参见元数据、分区、架构和示例行
*   在易读的编辑器中编写查询
*   通过 web 界面提交查询
*   跟踪查询进度
*   通过浏览器以 CSV 格式获取结果
*   基于查询结果创建新的配置单元表
*   编写后保存查询
*   工具中运行的所有查询的可搜索历史记录

本着 Presto 的精神，我们试图通过提供本地存储选项来简化 Airpal 的安装，以便那些想测试它的人无需任何开销或成本。更多详细信息，请访问 GitHub 页面:https://github.com/airbnb/airpal

Airpal 的几个显著技术特性是:

1.  使用 Dropwizard 作为在 Java 中提供 REST 服务的简单方法
2.  使用 SSE(服务器发送事件)将消息从服务器推送到客户端
3.  前端 JavaScript 使用 react.js

最后，如果我们不提到脸书作为 Hive 的原始开发者和构建 UI 工具以方便访问大数据的先驱所提供的令人敬畏的指导，那将是我们的失职。我们站在巨人的肩膀上制作这个工具，我们感谢脸书的数据基础设施和数据工具团队能够提供的影响和投入。

如果你有兴趣帮助建立一套世界级的数据工具，看看这个空缺的职位:[https://www.airbnb.com/jobs/departments/position/48112](https://www.airbnb.com/jobs/departments/position/48112)

![](img/3913f6470a7657e02386189e67b4eb30.png)

## 在 [airbnb.io](http://airbnb.io) 查看我们所有的开源项目，并在 Twitter 上关注我们:[@ Airbnb eng](https://twitter.com/AirbnbEng)+[@ Airbnb data](https://twitter.com/AirbnbData)

*原载于 2015 年 3 月 5 日 nerds.airbnb.com**的* [*。*](http://nerds.airbnb.com/airpal/)