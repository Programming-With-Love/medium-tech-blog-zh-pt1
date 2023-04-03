# 什么时候应该在 React 中使用 setState()。

> 原文：<https://medium.com/bb-tutorials-and-thoughts/when-should-we-use-setstate-in-react-202e3e86bc75?source=collection_archive---------3----------------------->

## 在哪些生命周期方法中可以设置组件的状态，在哪里可以创建无限循环？

![](img/690ea59442a6ea2aa1fd54eb5eb196e2.png)

Photo by [Moja Msanii](https://unsplash.com/@mojamsanii?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 React 中以正确的方式使用`setState`非常重要。在组件树中，数据从上到下流动。如果我们在顶层组件中设置状态，所有子组件都是…