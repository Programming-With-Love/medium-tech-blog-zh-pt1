# 角度的交流模式

> 原文：<https://medium.com/bb-tutorials-and-thoughts/communication-patterns-in-angular-9b0a829aa916?source=collection_archive---------2----------------------->

## 数据如何以角度形式流动

![](img/d0f21d48b4f4d5a847b858effac6b3fb.png)

Photo by [Nick Fewings](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Angular 遵循双向数据流模式，这意味着您可以沿着组件树向上*和向下*发送数据。因为 Angular 中的一切都是组件，所以理解组件之间的通信对于每个成功的 Angular 项目都是至关重要的。

在这篇文章中，我将介绍组件间通信的所有方式。这些是…