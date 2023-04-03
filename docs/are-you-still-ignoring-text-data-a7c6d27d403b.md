# 你还在忽略文本数据吗？

> 原文：<https://medium.com/walmartglobaltech/are-you-still-ignoring-text-data-a7c6d27d403b?source=collection_archive---------1----------------------->

![](img/43bdf352f2529f65f27d26e0bdf74e0e.png)

Photo credit: [P](https://pixabay.com/illustrations/mobile-phone-smartphone-app-1087845/)ixabay

文本数据分析是一种活动，通过这种活动，组织可以从客户共享的文本消息中获取价值。文本分析是一种从非结构化数据中获取价值的方法。这种类型的分析使用各种统计和机器学习技术来获取有意义的数据进行分析，以衡量产品评论、反馈和对基于事实的决策的支持。客户可以将这些短信发布在各种社交媒体平台上，如 Twitter、Reddit、脸书等。所有这些渠道都有大量的文本数据，可以用来发现沃尔玛顾客在商店或我们的网站上面临的主要问题或挑战。

# 概观

作为最大的零售公司，沃尔玛拥有数 Pb 的数据，并且在数量、变化速度和复杂性方面拥有最大的数据湖。数据湖对组织来说是强制性的，因为它们有助于整合所有数据。Lakes 托管在内部或云上，进一步提高了组织获取所需洞察力的能力。所有组织都有数据湖，这是一种常见的做法。但是，文本数据湖通常会被忽略，因为从数据中导出分析时会因单个唯一键的不可用而变得复杂，而唯一键对于将文本数据湖与传统的组织数据相结合是必不可少的。忽略文本数据会使各种组织对其客户每天面临的挑战视而不见。因此，重要的是开始思考如何有效地从社交媒体平台、呼叫中心、产品评论、商店和网络评论以及博客中获取社交媒体数据。

![](img/60dc45fb2f963f5aee82540c2f9d5fb6.png)

每个组织都有不同的数据分类方法。对湖泊中的数据进行分类的一些常见阶段是黄金数据(只是原始数据)、冷数据(历史数据—收集的过剩数据)、热数据(实时数据—流数据)、干净数据(目录)。

此活动有助于整合结构化、非结构化和半结构化数据。主动分析的简单关键目标。每个公司希望使用这些数据集实现的关键目标是获得分析并增强其客户、用户、员工等的体验。文本数据湖的作用是识别关键事件，并从社交平台、移动日志、物联网设备和系统日志上的各种社交媒体运动中捕获信息。在不同位置捕获的所有事件有助于提炼数据，这些数据将进一步用于产生价值。这些事件也使企业能够增加他们所做决策的成功概率。

![](img/507db8da132880ac8f1d0f1d3da8647d.png)

# 关键设计考虑因素

以下是关键的设计考虑因素:

*   确保更快的响应，接近实时的数据流
*   确保数据安全绝不存储个人信息
*   确保数据质量，因为任何数据差异都可能导致不正确的信号，并导致错误的客户信号
*   使提要成为多租户
*   优化数据管道，确保管道运行没有任何问题
*   为了使提要易于按类别和来源查询
*   捕捉客户警报和级别类别
*   确保国家代码根据您的主数据管理(MDM)进行规范化
*   确保语言代码是标准化的示例:英语或基于您的 MDM 的全名
*   时间戳应该是规范化的 mm/dd/yyyy 24HH:MI:SS，并将其恢复到标准时区和时间戳。
*   标准化位置识别技术

# 文本数据湖体系结构

最好的云数据工程架构总是从零到无限扩展，以处理批量或实时数据，这可以使用 dockers 或多个云产品来实现。最好的体系结构总是允许以最小的成本按时或实时获取数据。下面的架构是一个示例文本数据湖架构，支持事件收集、数据强制、数据处理、标记和报告。

![](img/c4370ab929560890b46b3a9aca480ed4.png)

**数据反馈回路**

反馈是改进机器学习模型并获得更准确结果的关键。有时反馈被人工审查，并对模型进行调整。

![](img/b389580fff2eaea1d0eaf7ba9e1ea2f7.png)

**数据质量检查**

> 对于湖中存在的数据，永远不要损害您的数据质量检查和数据治理。数据质量规则提高了数据质量，并允许组织基于数据点做出强有力的决策。

1.删除重复项

2.排除关键字

3.内容大小<10 consider removing

4\. Convert all your Emoji to Text

## Data Model Design

Data Models are used to capture all the data attributes. It’s important to identify the key for tables in your data model. Unique identification, text, language, source location, URL, and even time, time of collection of data are key for any text data. Defining the

clear filter mechanism before pulling the data from External sources makes the analysis easy.

Optimize the model after analysis, this can be any supplier, security, product, store, and manager based. Avoid combining two different channels into one model. Always have a defense mechanism for your model by identifying the records such as fake ratings and reviews, competitor reviews and etc.

# Reporting and Alerts Analytics

Reporting is essential to determine the story that your data is trying to tell. If your organization is not doing reporting on top of the data they are collecting, then certainly they need to pay attention to the power of reporting. You can categorize text analytics data based on different levels like Level 1( **直接**，二级**提升**，三级**中度**，四级**参考**。1 级是需要立即关注并需要在 4 小时内解决的数据点。

# 结论

*   这个过程中非常重要的一部分是数据管道。这确保了所提供的数据尽可能的准确和可伸缩。这是通过检查各种源系统，采用适当的数据模型设计来实现的
*   管道的监控和警报框架帮助我们确保管道运行没有任何问题
*   每一层的数据质量检查有助于我们识别在我们作为馈送的一部分生成的数据中看到的差异，并采取必要的措施
*   利用云来运行我们的数据管道和优化作业工作流帮助我们加快了最终快照的生成，整个过程在 5 分钟内完成
*   定义适当的模型和数据结构，以便更好地利用 C 语言来提供和解决一些带宽问题
*   数据安全和数据湖的治理是此类解决方案成功的关键
*   通过使用基于 Docker 的方法来提供提要，新合作伙伴的加入变得更加容易。