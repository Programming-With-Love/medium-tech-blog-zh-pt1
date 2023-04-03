# Java 8，如何处理流中的异常？

> 原文：<https://medium.com/quick-code/java-8-how-to-handle-exceptions-in-a-stream-f884172b10?source=collection_archive---------0----------------------->

迁移到 Java 8 很容易采用新的方法，并且产生的代码更短更容易理解，除非您必须处理流中的异常。

![](img/c27992f49226b099b6ff3c13175430f6.png)

Photo by [Math](https://unsplash.com/@builtbymath?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

让我们考虑一下 John 的案例，他是一名开发人员。他的老板告诉他，客户希望将这段代码移植到 Java 8: