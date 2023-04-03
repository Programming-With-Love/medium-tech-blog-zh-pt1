# 构建 RoadRunner，实现近乎实时的反馈循环

> 原文：<https://medium.com/pinterest-engineering/building-roadrunner-for-a-near-real-time-feedback-loop-1bcad821496a?source=collection_archive---------1----------------------->

ram ki Venkatachalam & Mukund Narasimhan | Pinterest 工程师、数据和发现

我们广泛使用 Hadoop/MapReduce 批处理作业来处理内容和活动流。home feed 就是一个很好的例子，我们使用这样的批处理作业来计算信号和特征，为每个 Pinner 创建一个有趣的 pin 的个性化 feed。虽然这种批处理方法有效且可伸缩，但它无法响应最近的活动。RoadRunner 是一个流计算基础设施，它的诞生是为了满足近实时反馈周期的需求。

## 介绍 RoadRunner

RoadRunner 处理一系列活动(重复、搜索、点击等)。)并近乎实时地将它们聚合在一起。从这些数据中，我们可以得出对内容的质量和类别、爱好者的兴趣等有意义的估计。这些信号可用于我们的搜索和 home feed 排名模型，以确定哪些引脚最有可能吸引 Pinner。RoadRunner 还保存了大量关于活动流的时间信息。例如，我们可以辨别质量信号如何随时间变化，这使我们能够了解周末什么内容受欢迎，什么是趋势或最近发生了什么变化。

## 在后台

RoadRunner 基于 Apache Storm 构建，用于流计算，HBase 用于存储，Finagle 用于服务。它每天处理大约 100 亿个事件，峰值速率约为 30K/秒，端到端延迟仅为几秒钟(p99 < 10s)。

我们需要一种存储技术来支持低延迟、高 QPS 增量和极低延迟的读取/扫描(查找过去七天内一个 Pin 的每小时指标)。HBase 非常适合，因为它支持原子增量和扫描，并且它是可水平扩展的，在我们的工程团队中广泛使用。我们的 HBase 集群使用固态硬盘来满足我们的延迟要求。HBase 键是 objectID 和时间窗口 ID 的组合，用于终止旧数据、捕获时间序列并支持动态范围内的聚合。这种设计允许合并批处理系统和 roadrunner 的结果。

我们还花时间调整了风暴拓扑。为了保持 storm 拓扑自由流动，我们使用未来池来阻止对 HBase 的更新。这比增加负责执行更新的螺栓数量更有效。我们构建了一个端到端的审计系统，该系统为测试对象抽取事件，并在几秒钟内检查服务的预期指标更新。这种定期检查有助于确保整个系统健康。

## 使用 RoadRunner 数据

来自 RoadRunner 的数据与来自夜间聚合的数据相结合，以生成进入 home feed 排名模型的特征向量。RoadRunner 计算的特性和 Hadoop 作业计算的特性之间有一些重叠。虽然两者都计算 Pin 质量的估计值，但是 Hadoop 的估计值是在 Pin 的整个生命周期内(或者至少在夜间作业运行之前)，RoadRunner 的估计值是在过去几天内。两者都有长处和短处。RoadRunner 的估计更新鲜、更灵活，允许我们将基于一天中的时间或一周中的日期的变化纳入模型。然而，它们的噪音更大，因为它们基于的数据更少，也更不完整。我们的模型将批量估算和实时估算视为两种完全不同的特征。我们使用的训练过程不要求特性是独立的，所以这对我们来说很好。

我们首先为搜索和 home feed 构建并推出了 RoadRunner。自那以后，我们发现了几个额外的用例，从新用户产品内指南的个性化到货币化产品。我们已经开始开发第 2 版来支持这一大堆需求。我们设想下一个版本是一个自助服务平台，允许您轻松声明功能，并应用 [lambda 架构](http://lambda-architecture.net/)来提高容错能力。

请继续关注未来的博客文章，讨论使用这些动态特性的模型的训练和评估，以及版本 2 中的新特性。

*Ramki Venkatachalam 和 Mukund Narasimhan 分别是数据团队和推荐团队的软件工程师。*

*鸣谢:RoadRunner 是 Pinterest 实现流计算解决方案的长期战略努力的一部分。它是与来自平台、发现和云团队的许多人合作开发的。*

*获取 Pinterest 工程新闻和更新，关注我们的工程*[*Pinterest*](https://www.pinterest.com/malorie/pinterest-engineering-news/)*，* [*脸书*](https://www.facebook.com/pinterestengineering) *和* [*推特*](https://twitter.com/PinterestEng) *。有兴趣加入团队吗？查看我们的* [*招聘网站*](https://about.pinterest.com/en/careers/engineering-product) *。*