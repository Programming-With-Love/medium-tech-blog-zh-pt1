# React 中什么叫做“提升状态”

> 原文：<https://medium.com/bb-tutorials-and-thoughts/what-is-called-lifting-state-up-in-react-785d52da940a?source=collection_archive---------8----------------------->

## 带示例的逐步指南

![](img/40d5af27d4ecd0d264e363e88f0ce772.png)

Photo by [Victor Freitas](https://unsplash.com/@victorfreitas?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

React 建议应用程序中的所有数据采用自顶向下的数据流。有时应用程序中的几个组件需要在数据发生变化时反映相似的数据。React 建议将状态提升到最近的祖先，而不是在单个组件中维护状态。这确保了在…中有一个唯一的真理来源