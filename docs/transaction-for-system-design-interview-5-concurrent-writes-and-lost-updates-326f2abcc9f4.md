# 系统设计面试事务(5):并发写入和丢失更新

> 原文：<https://medium.com/double-pointer/transaction-for-system-design-interview-5-concurrent-writes-and-lost-updates-326f2abcc9f4?source=collection_archive---------1----------------------->

[**上一页**](/double-pointer/transaction-for-system-design-interview-4-read-skew-and-snapshot-isolation-c5dc47d831e) **|** [**首页**](https://bit.ly/3tVGgRY) **|** [**下一页**](/double-pointer/transactions-for-system-design-interview-6-write-skew-12a9fcd7fff5)**|**[**Sys 设计资源列表**](/double-pointer/ultimate-collection-of-system-design-resources-for-tech-interviews-ddc72c4efeac)

***别忘了买一本*** [***设计数据密集型应用***](https://amzn.to/3HWOSPm) ***，这是系统设计面试准备中最重要的一本书！***[***uda city***](https://bit.ly/3JIpvl4)***|***[***Coursera***](https://imp.i384100.net/zaYBB0)***|***[***plural sight***](https://pluralsight.pxf.io/Ao7GGK)***。***

> 查看 [**字节跳动的**](https://bytebytego.com?fpr=datajek34) 热门 [**系统设计面试课程**](https://bytebytego.com?fpr=datajek34)

***考虑*** [***注册付费媒体***](https://bit.ly/3LNjPXB) ***账户以访问我们为系统设计资源策划的内容。***