# 如何在 Javascript 中克隆对象

> 原文：<https://medium.com/bb-tutorials-and-thoughts/how-to-clone-objects-in-javascript-ea30a5fc9955?source=collection_archive---------3----------------------->

## 用实例探索三种方法

![](img/e3cf4b1f7cee224b72f567de25d1572c.png)

Photo by [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 javascript 中克隆对象在函数式编程中非常常见，在函数式编程中，您会大量使用不可变的对象。例如，如果您在任何前端框架中使用状态管理库，则在对该对象进行任何更改之前，您不能改变您需要进行深度克隆的状态(如果它是嵌套结构)。