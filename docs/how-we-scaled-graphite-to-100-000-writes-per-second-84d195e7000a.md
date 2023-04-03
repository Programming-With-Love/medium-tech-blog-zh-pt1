# 我们如何将 Graphite 扩展到每秒 100，000 次写入。

> 原文：<https://medium.com/walmartglobaltech/how-we-scaled-graphite-to-100-000-writes-per-second-84d195e7000a?source=collection_archive---------2----------------------->

![](img/e338ddb859b4817d8866e60fed2f95b3.png)

Photo by [Luke Chesser](https://unsplash.com/@lukechesser?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 石墨简介

目前有许多可用的监控工具，包括 Graphite、Prometheus 和 Datadog。

虽然在过去五年中，人们对 Prometheus 和类似的云产品的兴趣有所增加，但石墨仍然是受欢迎的选择之一。(来源:[https://graphiteapp.org/#caseStudies](https://graphiteapp.org/#caseStudies))