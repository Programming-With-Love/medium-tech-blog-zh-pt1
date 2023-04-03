# 介绍在 Pinterest 上进行可视化搜索的新方法

> 原文：<https://medium.com/pinterest-engineering/introducing-a-new-way-to-visually-search-on-pinterest-67c8284b3684?source=collection_archive---------0----------------------->

Andrew Zhai| Pinterest 视觉搜索工程师

Pinterest 的发现产品是建立在图钉之上的。去年，我们推出了[引导搜索](https://blog.pinterest.com/en/guided-search-new-way-find-what-you%E2%80%99re-looking)，这是一项基于理解 pin 描述的功能。在此之前，我们推出了[相关引脚](https://blog.pinterest.com/en/freshening-your-home-feed-related-pins)，这是一项建立在理解引脚到板连接基础上的服务。虽然我们已经能够使用这些 Pinner 策划的信号来构建新产品和新功能，但在每个 Pin 中有一个信号我们还没有利用，一个 Pin 的图像——直到现在。

明天，我们将推出一个视觉搜索工具，让您可以放大大头针图像中的特定对象，并发现视觉上相似的对象、颜色、图案等。例如，在客厅的大头针上看到你感兴趣的一盏灯？轻按大头针角上的搜索工具，将缩放工具拖到灯上，并向下滚动以查找视觉上相似的大头针。

![](img/ec24d7ec90f4bad92e9302aa2f06f553.png)

我们的视觉搜索系统的核心是我们如何表现图像，它是由一个四人工程师团队在短短几个月内建成的。通过与[伯克利视觉和学习中心](https://bvlc.eecs.berkeley.edu/)成员的密切合作，我们利用深度学习来学习强大的图像特征，方法是利用由 Pinners 管理的数十亿个 pin 的丰富注释数据集。然后，这些特征可以用于计算任意两个图像之间的相似性得分。在过去的几个月里，我们一直在尝试用这些视觉信号来改进相关的引脚，正如我们今天发布的最新[白皮书](https://engineering.pinterest.com/sites/engineering/files/article/fields/field_image/human-curation-convnets%20%281%29.pdf)中所详述的那样。

为了找到视觉上相似的结果，我们考虑给定特征与数十亿其他特征的相似性分数。为了有效地完成这项任务，我们建立了一个分布式索引和搜索系统(使用开源工具)，使我们能够在几分之一秒内扩展到数十亿张图像并找到数千个视觉上相似的结果。我们将在不久的将来发布一篇论文，描述我们在使用深度学习功能构建大规模视觉搜索系统方面的发现。有关我们之前工作的更多信息，请参考我们的[KDD 15 年论文](http://arxiv.org/abs/1505.07647)。

视觉搜索允许人们使用图像进行搜索。在大头针的图像中有许多有趣的项目；我们想给 Pinners 一个工具来学习更多关于这些项目。通过使用裁剪工具指定您感兴趣的图像部分，我们可以实时推荐视觉上相似的结果。我们优化视觉相似性，而不仅仅是让 Pinners 发现精确结果的重复，以及可能在风格或模式或形状上相似的意外结果。

通过在 Pinterest 中加入新的视觉搜索体验，我们希望给 Pinners 提供另一种发现想法和产品的方式。视觉搜索工具明天开始在全球所有平台(iOS、Android 和 web)上向 Pinners 推出，这只是第一步。Pin 的人越多，技术就会变得越好。请关注更多视觉搜索更新。如果你有兴趣帮助我们建立视觉发现和搜索技术，[加入我们的团队](https://careers.pinterest.com/careers/engineering/san-francisco)！

*鸣谢:本产品由视觉探索团队(Dmitry Kislyuk、Jeff Donahue、Eric Tzeng、和 Kevin Jing)、探索产品团队(Kelei Xu、Luna Ruan、Vishwa Patel 和 Naveen Gavini)、产品设计团队(Patrik Goethe、Albert Pereta Farre)、Mike Repass 和 GTM 团队的成员共同努力完成。我们还要感谢来自伯克利咖啡馆团队的杰夫·多纳休、特雷弗·达雷尔和埃里克·曾。*