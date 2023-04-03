# 使用二级缓存优化搜索索引生成

> 原文：<https://medium.com/walmartglobaltech/optimizing-search-index-generation-using-secondary-cache-172c085a636a?source=collection_archive---------4----------------------->

[巴拉特·文卡特](https://www.linkedin.com/in/bharatvenkat)，[斯拉万·雷库拉](https://www.linkedin.com/in/sravan-rekula-0b567023)，[赫马德里·阿南塔](https://www.linkedin.com/in/hemadriananta?trk=people-guest_profile-result-card_result-card_full-click)

# 介绍

为了支持沃尔玛搜索，会定期生成完整的索引，并通过实时流处理应用增量更新。他们一起保持沃尔玛搜索索引的更新。完整索引是作为基于 [Spark](https://spark.apache.org/) 的批处理作业实现的，它对底层的条目存储进行完整的表扫描( [Apache Cassandra](http://cassandra.apache.org/) )。完整索引生成的要求是捕获整个沃尔玛商品目录的当前状态，并且…