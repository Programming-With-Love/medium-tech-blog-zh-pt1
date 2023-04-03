# Ruby 中的并发性:互斥与信号量

> 原文：<https://medium.com/double-pointer/concurrency-in-ruby-mutex-vs-semaphore-af9c30bda29c?source=collection_archive---------6----------------------->

互斥体和信号量的概念和区别会让大多数开发人员感到困惑。在本课中，我们将讨论几乎所有语言框架提供的两种最基本的并发结构之间的差异。互斥体和信号量之间的区别是高级工程职位的面试问题！

> **别忘了拿到你的** [**Ruby on Rails:用 Rails**](https://amzn.to/3HX0Zfl) **学习 Web 开发。**
> 
> **请考虑支持我们的** [**中型**](https://bit.ly/3OvimpR) **或结帐我们的合作伙伴之一**[**uda city**](https://bit.ly/3JIpvl4)**|**[**Coursera**](https://imp.i384100.net/zaYBB0)**|**[**plural sight**](https://pluralsight.pxf.io/Ao7GGK)