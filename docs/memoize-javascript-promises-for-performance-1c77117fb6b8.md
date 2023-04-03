# 记住 JavaScript 对性能的承诺

> 原文：<https://medium.com/globant/memoize-javascript-promises-for-performance-1c77117fb6b8?source=collection_archive---------0----------------------->

通过*记忆*来增强 web 应用程序的性能，这是一种函数式编程技术，可以让您避免浪费的冗余调用。

![](img/de60d13f56140ab6ee39ff5ee73d2ca7.png)

Photo by [Marc-Olivier Jodoin](https://unsplash.com/@marcojodoin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

当您有一个复杂的 web 应用程序，有许多相互连接的部分，很可能会发生没有好的理由就进行多余的 API 调用，从而减慢速度并产生糟糕的用户体验——如何解决这个问题呢？