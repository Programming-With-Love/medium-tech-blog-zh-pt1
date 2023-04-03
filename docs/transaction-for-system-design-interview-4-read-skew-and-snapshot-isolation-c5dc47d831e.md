# 系统设计会议记录(4):读取偏差和快照隔离

> 原文：<https://medium.com/double-pointer/transaction-for-system-design-interview-4-read-skew-and-snapshot-isolation-c5dc47d831e?source=collection_archive---------2----------------------->

[**上一张**](/double-pointer/transactions-for-system-design-interview-3-isolation-levels-6dff935ff241) **|** [**首页**](https://bit.ly/3tVGgRY) **|** [**下一张**](/double-pointer/transaction-for-system-design-interview-5-concurrent-writes-and-lost-updates-326f2abcc9f4)

***别忘了买一本*** [***设计数据密集型应用***](https://amzn.to/3HWOSPm) ***，这是系统设计面试准备中最重要的一本书！***[***uda city***](https://bit.ly/3JIpvl4)***|***[***Coursera***](https://imp.i384100.net/zaYBB0)***|***[***plural sight***](https://pluralsight.pxf.io/Ao7GGK)***。***

> 查看 [**字节跳动的**](https://bytebytego.com?fpr=datajek34) 热门 [**系统设计面试课程**](https://bytebytego.com?fpr=datajek34)

***考虑*** [***注册付费媒体***](https://bit.ly/3LNjPXB) ***账户，访问我们为系统设计资源策划的内容。***