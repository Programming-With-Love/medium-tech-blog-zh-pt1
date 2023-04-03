# JavaScript 中的 Nullish 合并运算符

> 原文：<https://medium.com/version-1/the-nullish-coalescing-operator-in-javascript-721428ef97ca?source=collection_archive---------0----------------------->

![](img/43fbb042684bf91b22fd1b2ec3eed4b4.png)

Photo by [Natalie Pedigo](https://unsplash.com/@nataliepedigo?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**无效合并运算符(？？)**是一个逻辑运算符，当其左侧操作数为 **nullish** ( `null`或`undefined`)时，返回其右侧操作数，否则返回其左侧操作数。

这可以看作是逻辑`OR` ( `||`)操作符的一个特例，如果左边的操作数是任何 **falsy** 值，而不仅仅是 **nullish** ，那么它将返回右边的操作数。

记住一个**空值**和一个**假值**是不同的概念。当`null`、`undefined`、`0`、`''`、`false`和`NaN`被认为是虚假值时，只有`null`和`undefined`被认为是无效值。

检查以下代码片段:

**与可选链接运算符(？。)**

**nullish 合并运算符**将`undefined`和`null`视为特定值，可选链接运算符( `[?.](/@mamunoz-dev/the-optional-chaining-operator-in-javascript-8e9342171c2e)` [)](/@mamunoz-dev/the-optional-chaining-operator-in-javascript-8e9342171c2e) 也是如此，它对于访问可能为 null 或未定义的对象的属性很有用。

检查以下代码片段:

检查浏览器兼容性:[https://can use . com/MDN-JavaScript _ operators _ nullish _ coalescing](https://caniuse.com/mdn-javascript_operators_nullish_coalescing)

# 关于作者:

米格尔·穆尼奥斯是 Version 1 的首席软件工程师。