# 兴趣分类法:Pinterest 上用于内容理解的知识图管理系统

> 原文：<https://medium.com/pinterest-engineering/interest-taxonomy-a-knowledge-graph-management-system-for-content-understanding-at-pinterest-a6ae75c203fd?source=collection_archive---------0----------------------->

Song Cui 和 Dhananjay Shrouty |软件工程师，内容知识

# Pinterest 的兴趣分类

我们最近开始推出测试版的 [Pinterest Trends](https://newsroom.pinterest.com/en/post/introducing-pinterest-trends-showing-the-power-of-insights-irl) ，这是一个新工具，可以查看过去 12 个月中 Pinterest 上的美国热门搜索词，并查看搜索词何时达到峰值，以更好地了解内容在平台上的表现。因为人们来到 Pinterest 进行规划，我们对新兴趋势有独特的见解，这就是为什么我们也发布年度 [Pinterest 100 报告(](https://newsroom.pinterest.com/en/post/pinterest-100-the-top-trends-to-inspire-and-try-in-2020)上个月发布的最新报告)来展示 2020 年将会发生什么。

我们能够收集这些见解，因为 Pinterest 从根本上来说是一个不同类型的平台，来自世界各地的超过 3.2 亿人在这里保存想法和*计划*——从决定穿什么去工作的日常生活，到大大小小的生活阶段，如购买新房子或决定去哪里旅行。迄今为止，该平台上已有超过 2000 亿个想法被保存到超过 40 亿个版块，为新兴趋势提供了见解，并为消费者行为和不断变化的口味提供了早期信号。

但是为了理解正在发生的趋势，我们需要理解 Pinners 正在搜索的内容，以及 Pins 所关联的类别。为了做到这一点，我们构建了一个基于分类的知识管理系统，以高效的方式实现内容理解。

分类法是一种对实体进行分类并定义它们之间的层次关系的方法。它被广泛用作行业中的知识管理系统，并已被证明在提高搜索、用户行为建模和分类任务中的机器学习模型的准确性方面取得了成功。

在 Pinterest，我们使用分类法来组织热门话题和实体(我们称之为“兴趣”)，并为广告定向筛选节点。这些兴趣组合在一个父子层次树结构中，每个孩子都是其单亲的子类。顶级分类节点定义了广泛的垂直领域，如“女性时尚”和“DIY 和手工艺”,它们捕捉了 Pinterest 上与 pin 相关的一般兴趣。我们有多达 11 个级别的子节点，可以捕捉更细粒度的主题。

![](img/9749f45f446bbe8f0b13917e9810a56d.png)

Interest Taxonomy Structure Example

# 示例使用案例

兴趣分类在工程中的商业、产品和生产信号中有许多不同的用例。在这篇博客中，我们只分享四个用例。

# 广告经理

广告管理器是广告商的主要界面。为了帮助 Pinterest 广告客户，一些有趣的分类节点可供选择，如下所示:

![](img/cdb9cf894409ad59471c3dfcab6f3463.png)

Ads Manager

兴趣分类法用于基于兴趣的定位，根据 Pinterest 对 Pinners 的兴趣、品味和计划的独特理解，帮助广告商接触到正确的受众。

要按兴趣查看他们的广告活动表现，广告商可以在广告组或推广 Pin 级别选择“兴趣定位”细分。此粒度适用于“交付”、“绩效”或“自定义”报告。

![](img/c5940867f9401cd47556bd7d02b7bf93.png)

Ads Manager Performance Metrics

# 将 pin 映射到兴趣分类

我们构建了[**pin 2 interes**t](/pinterest-engineering/pin2interest-a-scalable-system-for-content-classification-41a586675ee7)(P2I)，一个用于内容分类的可扩展机器学习系统，将我们的 200B+pin 语料库映射到我们的兴趣分类法。来自 P2I 的结果被用于生成个性化推荐，并为其他机器学习模型创建排名特征。P2I 是在生产和有许多消费者，如家庭饲料排名和广告定位。

P2I 利用文本和视觉输入，如[注释](/pinterest-engineering/understanding-pins-through-keyword-extraction-40cf94214c18)，视觉嵌入和董事会名称。它使用自然语言处理(NLP)技术，如词汇扩展和嵌入相似性，将每张图像的输入映射到作为预测候选的分类节点列表。然后，使用搜索相关性模型来预测和排列图像和每个分类节点之间的匹配分数。下面显示了一个示例 P2I 输出，包括与图像得分最相关的兴趣预测。

![](img/81b2c0591d25bdd4758e3b73506df892.png)![](img/4db1a0811ff6da4ca126a693a847c1f6.png)

P2I Model Prediction Example

分类法层级信息也被用作 P2I 排名信息。超过 99%的图钉可以映射到至少一个分类节点。分类法的粒度和质量对于 P2I 准确性至关重要。如果图像的内容属于一个非常特殊的主题，并且分类没有类似的节点来覆盖这个主题，P2I 将把这个图像映射到一个具有不同上下文的节点，并且预测精度下降。

P2I 和兴趣分类法为内容理解提供了重要的见解。例如，我们可以监控每个分类节点的图片数量，这个指标告诉我们主题在 Pinterest 内容中的趋势和下降。

# 将用户映射到兴趣分类

兴趣分类法也在 ML 系统中被用来推断用户的兴趣。这个系统叫做用户 2 兴趣。ML 系统最重要的输入信号之一是从 Pin2Interest 输出的用户参与 Pin 和这些 pin 的相应兴趣标签(在前面的部分中提到过)。

Pinterest 广泛使用用户兴趣信号进行广告定位和有机推荐，它还可以从用户角度提供对兴趣分类的见解。例如，我们可以计算统计数据，如每个分类节点的用户数量，以通知我们的广告客户 Pinners 的整体兴趣变化。

# 将查询映射到兴趣分类

Query2Interest (Q2I)将短文本查询映射到分类节点。这个信号利用 Pinterest 中的多任务文本嵌入系统 [Pintext](/pinterest-engineering/pintext-a-multitask-text-embedding-system-in-pinterest-b80ece364555) 来计算短文本和分类节点之间的相似性得分。它将具有与分类节点相似的类别和含义的查询分组。Q2I 已投入生产，用于各种广告和有机表面。将查询映射到兴趣分类有助于 Pinterest 理解用户的意图，这样我们就可以向他们提供相关的结果。

![](img/587c12069dd1bd3c590584cd5645731d.png)

Q2I Model Prediction Example

# 创建和维护兴趣分类

分类监管过程包含以下两个重要部分:1)RDF graphwebprotégé可视化和监管的数据建模；2)工程工作流程，以促进分类法的增量变化。我们将在下面详细介绍。

# RDF 数据建模、WebProtégé可视化和管理

为了对分类法中的数据进行建模，我们使用 RDF(资源描述框架)三元组来生成图形，这些图形也可以用于管理。我们使用开源工具 [WebProtégé](https://protegewiki.stanford.edu/wiki/WebProtege) 对分类进行可视化和人工监管，这有助于我们通过协作监管创建高质量的分类。我们使用的 RDF 数据模型描述如下:

![](img/87fa4e61ea4209117baf7ec9763ac5e3.png)

RDF Data Model Example

下图显示了我们用于协作监管的 WebProtégé中的数据建模。

![](img/80151211b93de01d8d1d583c6f7a6b47.png)

Data Modeling in Protege

# 从 RDF 到生产数据库

工程工作流将 RDF 图(XML 格式)作为输入，并生成关系数据库表供下游使用。对于分类法开发的每一次迭代，我们都要对从前一次迭代开发的分类法进行开发/扩展。因此，我们遵循分类法生成和发展的增量方式。当我们创建分类的新版本时，我们会始终如一地执行和支持各种操作，如添加新节点、重命名现有节点、删除节点以及将两个或更多节点合并为一个节点，以便开发高质量且与 Pinterest 上的内容相关的分类。我们为所有需要节点改变的情况(例如节点重命名、节点合并和节点删除)开发了启发式规则。

# 更新兴趣分类

Pinterest 分类法旨在从 Pinterest 内容中捕捉最重要、最及时的主题。我们的分类法涵盖了各种产品中使用的活动主题，如主题提要和购物。这些术语是从引脚、电路板名称和热门搜索查询中使用的流行注释中挖掘出来的。

当我们想要向现有的分类法添加一个新主题时，我们首先将候选术语发送给内容安全和法律等团队进行审查。然后，我们依靠基于 ML 算法的神经网络来预测现有节点作为候选项的父项的可能性。预测的父代由人工审核。之后，我们的分类学家会将新节点添加到 WebProtégé的当前分类中。整个过程描述如下:

![](img/b3f7963f844c95cd260a22e8e68a706d.png)

Taxonomy Generation and Update Workflow

由 NTE 模型做出的关键假设是至少存在一个仿射投影，使得一旦使用该矩阵来变换新项的嵌入(例如，“ **litecoin** ”)。变换后的嵌入的最近邻居是其父节点，例如“加密货币”。因此，关键是学习转换矩阵。为简单起见，在下面的模型图中，新术语表示为 **q(查询)**，潜在父项表示为 **p(父项)**:

![](img/5b2519736f99f89edcb333057b1ebc27.png)

Algorithm for Taxonomy Parent Finding

然后，损耗计算为两个分量之和。第一部分鼓励查询投影 p 与其真正的父项的嵌入 ep 相似。第二个组件鼓励查询投影不同于由- *p* 或*p’表示的 *m* “负采样”双亲。*每个查询-父对 *(q，p)* 的总损失是:

![](img/5eac1678749fb588210b3a19f656bb01.png)

我们从现有的分类层次结构中收集正面标签，并使用负面样本来训练模型。该模型用于几个大规模分类扩展项目的生产中。最终的人工审查仍然是必要的，因为分类法是对广告客户公开的，所以我们需要非常高质量的数据。

# 国际化

为了支持 Pinterest 的国际扩张，Pinterest 分类被翻译成 17 种语言，面向 20 个国家，并将继续扩展到新的市场。英语分类法是所有国际版本的基础。

# 展望未来

展望未来，我们很高兴能够以更及时、更系统的方式不断改进我们捕捉和理解趋势的方式。我们的兴趣分类和下游信号(如 P2I、U2I、B2I、Q2I)将定期自动更新。在未来，我们还将致力于在我们的分类法和关联属性中自动建立实体之间的新型关系( [link](/pinterest-engineering/understanding-product-attributes-for-shoppers-77268c746c87) )。如果你有兴趣更多地了解我们的知识工程团队和其他团队做什么，以及如何加入我们，请查看我们的[职业页面](https://www.pinterestcareers.com/homepage)。

# 确认

*我们要感谢所有为此项目做出贡献的人:我们的 EM 李瑞丽，郭；我们的首相 Troy Ma，Miwa Takaki 我们的工程师 Yimeng Zhang、Emaad Ahmed Manzoor(实习生)、国际团队 Helene Labriet-Gross、Evelyn Obamos、Francesca Di Marco、CatherineRose Mountain、Serena Perfetto 和斯坦福大学的 Protege 团队。还要特别感谢赵波、谢觐虞、黄锐提出的周到建议。*