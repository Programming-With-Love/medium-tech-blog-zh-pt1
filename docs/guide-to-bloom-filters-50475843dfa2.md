# 系统设计面试的布鲁姆过滤器

> 原文：<https://medium.com/double-pointer/guide-to-bloom-filters-50475843dfa2?source=collection_archive---------0----------------------->

[**上一张**](https://bit.ly/3w2k4bU) **|** [**首页**](https://bit.ly/3tVGgRY) **|** [**下一张**](https://bit.ly/3CCStyW)

***别忘了买一本*** [***设计数据密集型应用***](https://amzn.to/3HWOSPm) ***，这是系统设计面试准备中最重要的一本书！***[***uda city***](https://bit.ly/3JIpvl4)***|***[***Coursera***](https://imp.i384100.net/zaYBB0)***|***[***plural sight***](https://pluralsight.pxf.io/Ao7GGK)***。***

> 查看 [**字节跳动的**](https://bytebytego.com?fpr=datajek34) 热门 [**系统设计面试课程**](https://bytebytego.com?fpr=datajek34)

***考虑*** [***注册付费媒体***](https://bit.ly/3LNjPXB) ***账户，访问我们为系统设计资源策划的内容。***

在设计应用程序时，程序员经常面临这样的用例，他们需要检查一个元素是否存在于…