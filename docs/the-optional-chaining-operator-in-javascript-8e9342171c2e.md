# JavaScript 中可选的链接操作符

> 原文：<https://medium.com/version-1/the-optional-chaining-operator-in-javascript-8e9342171c2e?source=collection_archive---------1----------------------->

![](img/2997a929ae4af85f6633011f1b78b369.png)

Photo by [Aida L](https://unsplash.com/@aidamarie_photography?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**可选链接操作符(** `**?.**` **)** 使您能够读取位于连接对象链深处的属性的值，而无需检查链中的每个引用是否为 **nullish** ( `null`或`undefined`)。

`**?.**` **操作符**类似于`**.**` **链接**操作符，除了如果引用为 **nullish** ( `null`或`undefined`)时，表达式不会导致错误，而是使用返回值`undefined`短路。当与函数调用一起使用时，如果给定的函数不存在，它将返回`undefined`。

这有助于编写简单易读的代码，避免长时间的条件检查。

请记住，**空值**和**假值**是不同的概念。当`null`、`undefined`、`0`、`''`、`false`和`NaN`被认为是假值时，只有`null`和`undefined`被认为是无效值。

检查以下代码片段:

检查浏览器兼容性:[https://can use . com/MDN-JavaScript _ operators _ optional _ chaining](https://caniuse.com/mdn-javascript_operators_optional_chaining)

# 关于作者:

米格尔·穆尼奥斯是 Version 1 的首席软件工程师。