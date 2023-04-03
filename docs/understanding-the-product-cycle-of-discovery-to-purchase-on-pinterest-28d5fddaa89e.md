# 了解在 Pinterest 上发现和购买的产品周期

> 原文：<https://medium.com/pinterest-engineering/understanding-the-product-cycle-of-discovery-to-purchase-on-pinterest-28d5fddaa89e?source=collection_archive---------3----------------------->

、宋翠|软件工程师、内容兴趣理解团队、Jennifer Zhao |软件工程师、内容核心信号团队、赛晓、Felix Zhou |软件工程师、购物探索团队

Pinners 一直使用 Pinterest 购物，因为他们经常以规划的心态来探索产品和风格。我们的工作是帮助他们从灵感到购买，因此多年来，我们改进了购物产品，让他们更容易发现符合个人品味的产品和品牌。因此，购物只是作为公司的优先事项和使用案例而增长。

此外，在 Pinterest 上购物越来越多地为零售商提供了更多发现其产品和品牌的方式。我们最近[推出了](https://newsroom.pinterest.com/en/post/pinterest-announces-new-global-shopping-and-ad-features-ahead-of-holiday-season)视觉搜索广告，以及更多新的购物广告界面，并向英国消费者介绍了购物。

我们购物计划的核心部分是产品 pin，它是动态的、可购买的 pin，直接指向零售商网站上的产品页面。产品 Pin 码还在 Pin 码上显示有关产品的信息，包括价格、供货情况和零售商。如今，Pinterest 上有数亿个产品图钉。为了向品客推荐最相关的产品，我们需要了解产品以及这些产品从发现到购买的生命周期。我们[开发了](/pinterest-engineering/understanding-product-attributes-for-shoppers-77268c746c87)分类器和系统来理解各种产品属性(品牌、颜色、图案、风格等)。现在我们也需要了解不同类型的产品。

# 动机

除了搜索之外，“相关产品”(位于产品图钉下方)也是发现产品的一个关键区域。然而，在过去，这些 pin 并不总是产品 pin，因此并不总是指向最相关的内容和购买机会。想象一下，看到你梦想中的客厅的一枚大头针。你喜欢它的一切:整体氛围，特定元素，但最重要的是，你需要那张黄色的皮沙发。你在相关产品中找到一个灵感别针。你满怀期望地点击，然后你被引导到一个产品详情页面，是……一张地毯。这种经历让你感到困惑和失望。

![](img/cd9ccad76c164b1b5ad8a7acd755decc.png)

(1) Scene Pin with yellow leather coach that redirect to the product landing page for rugs

我们知道 Pinners 的一个困惑是，他们在浏览 Pinterest 时并不总是知道哪些 pin 是可购买的。即使你的意图很明确——你搜索了“皮沙发客厅创意”并点击了“商店”标签——决定如何购买你喜欢的产品仍然会令人困惑。

![](img/719b8fccdcc223bf0f4aba69f9e77632.png)

(2) Search bubble for bedroom furniture ideas to include shop product type to help proactively shopping experience

# 解决办法

很明显，我们需要高度自信地了解产品 pin 的类别。我们首先构建信号来了解我们的库存(库存产品 pin)和 Pinners 的购物意图(基于搜索查询模式)。我们构建了两个信号来满足需求:Pin2ProductCategory 和 Query2ProductCategory。

然而，在建立信号之前，我们需要首先定义类别，并基于 Pinterest 上最受欢迎的购物类别:时尚和家居装饰。

# pin 2 产品类别分类器

然后，我们构建了一个分类器，将我们的上亿个产品 Pin 映射到产品类别中，并将其称为 Pin2ProductCategory 分类器。

# 特征

我们利用了文本和视觉功能，详细描述如下。

## 产品名称

从离线分析中，我们看到与其他基于文本的信息相比，产品标题包含了更多与产品类型相关的信息。为了充分利用产品标题和提高学习权重，我们使用从产品标题关键字到产品类型类别的精确匹配和同义词匹配作为稀疏分类特征。在未来，我们将探索作为文本分类模型的 [FastText](https://fasttext.cc/docs/en/supervised-tutorial.html) 模型，从产品标题到产品类别，作为一个稀疏分类特征，以便于缩放。

## 嵌入文字的产品文本

我们利用了由[注释](/pinterest-engineering/understanding-pins-through-keyword-extraction-40cf94214c18)系统提取的文本。这些文本是从 Pin 标题、描述、登录页面文本和公告板名称中提取的，并通过 GBDT 排名器进行排名。我们查看了每个 Pin 的前五个术语，然后将每个注释术语的聚合嵌入作为来自我们内部多任务文本嵌入系统 [PinText](/pinterest-engineering/pintext-a-multitask-text-embedding-system-in-pinterest-b80ece364555) 的文本特征。

## 视觉嵌入的产品图像

我们的带有文本嵌入的模型对于时尚类工作得相当不错，但是对于家居装饰类就不行了。这是因为一些家居装饰类别在文字上非常相似，例如，沙发桌、咖啡桌、茶几、餐桌。然而，从视觉上看，由于桌子的形状或桌子的位置，它们有很大的不同。这种差异可以在视觉上捕捉到，所以我们使用嵌入了的 [graphsage 作为视觉特征。](/pinterest-engineering/pinsage-a-new-graph-convolutional-neural-network-for-web-scale-recommender-systems-88795a107f48)

# 模型架构

我们构建了一个 2 层前馈 DNN 模型，其中聚集了来自注释模型的前五个单词的文本嵌入和产品图像上的视觉嵌入。

![](img/bc13615ff61036c3a7028b77e557534f.png)

(3) Model architecture with tokenized annotations, word embedding, visual embedding and product title lexical and synonym match categorical feature 2-layer dnn model.

# 查询 2 产品类别

为了处理产品搜索，我们需要理解搜索查询的意图。

Query2ProductCategory (Q2PC)是一个查询理解引擎，它为广泛的查询推荐产品类别，这些查询可以有几种不同的产品类别用途。我们可以利用这个信号来提炼那些宽泛的购物查询，帮助用户做出购物决定。以查询“客厅创意”为例。我们可以展示来自 Q2PC 信号的“桌子”、“沙发”和“灯”等滤波器。如果 Pinner 点击过滤器“table ”,他/她将直接进入搜索页面，在该页面中，所有购物 pin 都是查询“living room ideas”下的表格。

为了构建信号，我们收集了一年的搜索日志，记录了所有被占用的引脚。通过将日志与 P2PC 信号连接起来，我们可以推断出每个查询下产品类别(PC)的年度总参与度。然后，我们根据约定为每个查询选择前 k 台电脑。以下是 Q2PC 的工作流程:

![](img/2260399513221454f4842bf5b2cb3952.png)

# 采用

## 视觉搜索

我们的一个视觉搜索产品将消费者从他们在大头针场景中看到的东西链接到单个产品。对于场景图像，我们将它分解成可购物的对象。通过放大图像或通过白点或购物标签图像点击特定产品，您可以看到视觉上相似的产品。“相似商店”由 Pin2ProductCategory 分类器支持。

![](img/ab28e784a849b77ab64005312a78f613.png)

## 相关产品表面中的产品过滤器

![](img/5997f4342f802a5d034505c149a83031.png)

在“More to Shop”(相关产品)模块中，给定一个查询 Pin，我们会推荐与之相关的类似产品。如上所述，Pinners 并不总是能够识别哪些 pin 是可购买的。我们利用 Pin2ProductCategory 信号来构建类别过滤器，这极大地帮助 Pinners 将推荐结果缩小到特定类别。我们观察到，在类别过滤的提要中，每次特写的长时间点击率(这里的特写是指点击到 Pin 页面，长时间点击是指从 Pin 页面点击到商家登录页面)是未过滤提要的两倍多。以下是“未过滤进料”与“过滤进料”的比较。我们可以清楚地看到，过滤器更好地引导购物者进入购物旅程的下一阶段。

![](img/306780ade27fd2c2fd4f729491276809.png)

(4) Category filtered feed of Rugs

# 购买电路板

Pinners 在决定买什么的时候，经常把产品图钉保存在他们的板上。他们会重新查看他们保存的 pin 码，以便选择购买哪一个。Pinterest 上的“搜索我的大头针”和“公告板”表面拥有一些最高的长点击率(点击一个结果并在该网站上停留 30 秒以上)和转化率。显然，Pinners 希望能够购买他们保存的 pin。他们也有非常高的目标，因为很明显他们喜欢他们所看到的，这也是他们最初保存它的原因。

除了购买你所存物品的一般需求之外，大多数情况下，你所存的 Pin 码并不直接**起作用**。在这种情况下，我们应该能够显示推荐，无论是保存的场景图像或相关产品的产品图钉，还是缺货的产品照片**。**

在 boards 上的“商店”选项卡中，我们在结构化视图中显示 Pinner 保存或查看的产品项目。具体来说，我们正在引入一个类别**模块**，它按类别显示保存的场景大头针中可购买的物品；以及**“see alternatives”**如果他们最初保存的 Pin 缺货，则推荐可用的产品。

![](img/b7b40bd674fa308e01469f4e9132d369.png)

(5) Category structured shop the board

## 搜索结构化摘要

广义的查询，如“客厅创意”和“夏季服装”，通常有一个较长的可购买性路径，因为它们具有探索性..由于它们通常显示 2 个以上的产品类别意向，我们使用 Q2PC 进行了一个实验，通过一个新的气泡式 UI 推荐产品类别，帮助用户缩小他们的请求范围。实验结束后，我们观察到一个长点击率增加了 10%。泡泡的平均点击率比之前放在相同位置的大头针高 40%。

## 视觉搜索广告

就在本周，我们将广告引入视觉搜索界面，包括相机搜索结果和在 Pin 内进行视觉搜索时出现的结果。这种交互功能非常适合购物，因为 Pinner 可以裁剪图像中的任何对象来查找相关产品。

在这个界面上显示购物广告将使 Pinner 在 Pinterest 上从生活方式图片中搜索视觉上相似的产品时有更好的购物体验。pin 2 产品类型也用作检索候选广告的过滤器，以检查产品类型是否匹配。

![](img/d0504133e06dabcabf77273c5d926c1b.png)

# 结论

**产品类别**广泛用于为 Pinterest 上的购物表面提供捆绑信号:Pin2ProductCategory，Query2ProductCategory。我们期待着更多的收养！

# 致谢:

*我们要感谢所有为此项目做出贡献的人:Alan Li (PM) &李锐(EM)、(购物发现工程师)、(视觉搜索团队工程师)、韩飞任(购物产品工程师)、(购物广告工程师)。*