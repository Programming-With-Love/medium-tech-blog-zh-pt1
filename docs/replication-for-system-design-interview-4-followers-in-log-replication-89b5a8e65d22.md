# 系统设计访谈之复制(四):日志复制中的追随者

> 原文：<https://medium.com/double-pointer/replication-for-system-design-interview-4-followers-in-log-replication-89b5a8e65d22?source=collection_archive---------5----------------------->

[**上一张**](https://bit.ly/3GVESUY) **|** [**首页**](https://bit.ly/3tVGgRY) **|** [**下一张**](https://bit.ly/3tVIgLN)

***别忘了买一本*** [***设计数据密集型应用***](https://amzn.to/3HWOSPm) ***，这是系统设计面试准备中最重要的一本书！***[***uda city***](https://bit.ly/3JIpvl4)***|***[***Coursera***](https://imp.i384100.net/zaYBB0)***|***[***plural sight***](https://pluralsight.pxf.io/Ao7GGK)***。***

> 查看 [**字节跳动的**](https://bytebytego.com?fpr=datajek34) 热门 [**系统设计面试课程**](https://bytebytego.com?fpr=datajek34)

***考虑*** [***注册付费媒体***](https://bit.ly/3LNjPXB) ***账户，访问我们为系统设计资源策划的内容。***