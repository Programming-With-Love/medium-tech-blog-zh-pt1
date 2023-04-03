# 阿帕奇卡桑德拉的墓碑

> 原文：<https://medium.com/walmartglobaltech/tombstones-in-apache-cassandra-d0a068a72dcc?source=collection_archive---------0----------------------->

![](img/83f0c978b478dccf8aad592718001281.png)

Photo by [Matt Botsford](https://unsplash.com/@mattbotsford?utm_source=medium&utm_medium=referral)

Apache Cassandra 是一个分布式数据库系统，其中数据总是分布的，并且通常在称为节点的机器集群上复制。在 Cassandra 中删除数据与在关系数据库中不同。与关系数据库系统不同，Cassandra 不会立即删除数据，而是简单地捕获删除操作，作为数据上的标记，称为墓碑。这非常…