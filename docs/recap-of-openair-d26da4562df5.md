# OpenAir 概述

> 原文：<https://medium.com/airbnb-engineering/recap-of-openair-d26da4562df5?source=collection_archive---------2----------------------->

迈克·柯蒂斯

三周前，我们举办了第二次技术大会 [OpenAir 2015](http://nerds.airbnb.com/openair-tech-conference-2015/) 。我们有来自整个行业的聪明人参加，比 2014 年增加了一倍多。

新一代的公司正在出现，他们的客户不是通过他们的应用和网站来评价他们，而是通过产品与他们联系的体验和内容来评价他们。考虑到这一点，OpenAir 2015 的主题是扩展人际关系，我们专注于线上到线下以及实现这一点的更好匹配。

一整天，我们了解了 Instagram 如何帮助用户发现启发他们的新内容；Stripe 如何帮助人们跨境交易，LinkedIn 如何使用数据来支持他们的社交网络，Periscope 如何在 Android 上实现，当然，Airbnb 如何帮助陌生人变成朋友。

在所有这些挑战的背后，有一些我们作为技术行业需要更好理解的核心概念——信任、个性化和支持这两者的数据。

考虑到这一点，Airbnb 开源了两个新的数据处理工具。第一个叫做[气流](http://airbnb.io/projects/airflow/)，这是一个以编程方式创作、调度和监控数据管道的复杂工具。业内人士将这项工作称为 ETL 工程。第二个是[航空溶解](http://airbnb.io/projects/aerosolve/)。Aerosolve 是一个针对 [Apache Spark](https://spark.apache.org/) 的机器学习包。它旨在将高学习能力与可访问的工作流相结合，该工作流鼓励迭代和深入理解人类层面上的潜在模式。自从我们推出这些工具以来，他们已经在 GitHub 上获得了 2000 多颗星——我们迫不及待地想看看人们如何使用它们并为之做出贡献。

我们还为我们的主机发布了一个名为[价格提示](http://www.theverge.com/2015/6/4/8730141/airbnb-price-tips-machine-learning)的新工具，它由 Aerosolve 提供支持。Price Tips 为我们的主机创建持续的提示，告诉他们如何为他们的列表定价，不只是一天，而是一年中的每一天。这种定价是完全动态的——它考虑了需求、位置、旅行趋势、便利设施、房屋类型等等。有数百个信号进入模型来产生每个价格提示。我们相信，更好的价格将是一个伟大的方式，进一步授权我们的主机，以满足他们的个人目标，通过托管。

最后，我们发布了全新的[礼品卡](https://www.airbnb.com/gift)网站，结束了上午的开幕主题演讲。现在，美国的任何人都可以在 Airbnb 上给他们的家人、朋友、同事、朋友或任何人送上旅行的礼物。对于那些观众中的幸运儿，我们给了每个人一张 100 美元的礼品卡。

我们将继续关注此次活动的更多视频，敬请关注。

![](img/3913f6470a7657e02386189e67b4eb30.png)

## 在 [airbnb.io](http://airbnb.io) 查看我们所有的开源项目，并在 Twitter 上关注我们:[@ Airbnb eng](https://twitter.com/AirbnbEng)+[@ Airbnb data](https://twitter.com/AirbnbData)

*原载于 2015 年 7 月 6 日 nerds.airbnb.com**的* [*。*](http://nerds.airbnb.com/large-scale-payments-systems-ruby-rails/)