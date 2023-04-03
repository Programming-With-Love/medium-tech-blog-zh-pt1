# 理解节点中的异步控制流。射流研究…

> 原文：<https://medium.com/bb-tutorials-and-thoughts/understanding-asynchronous-control-flow-in-node-js-dbcb34811085?source=collection_archive---------0----------------------->

## 带有示例项目的初学者指南

![](img/eb7b918bafb579a62b8a1826a67a16e6.png)

Photo by [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Javascript 是一种事件驱动的语言，这意味着代码的执行顺序并不总是连续的。您可以计划在未来某个时间点执行的任务。您可以定义一个任务，当某个其他操作在未来某个时间点完成时，可以调用该任务。例如，拿一个…