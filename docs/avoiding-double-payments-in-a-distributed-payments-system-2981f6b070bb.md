# 在分布式支付系统中避免重复支付

> 原文：<https://medium.com/airbnb-engineering/avoiding-double-payments-in-a-distributed-payments-system-2981f6b070bb?source=collection_archive---------0----------------------->

## 我们如何构建一个通用的等幂框架来实现我们的支付微服务架构的最终一致性和正确性。

**作者:** [周润发](/@jon.j.chew)和[尼纳德·希斯蒂](/@ninadkhisti)

![](img/740d99bdd3a661f5e369f164f5a89522.png)

One of the conference rooms in our San Francisco office

# 背景