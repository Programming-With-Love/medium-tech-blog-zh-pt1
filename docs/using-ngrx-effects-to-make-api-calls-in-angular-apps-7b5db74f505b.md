# 在 Angular 应用程序中使用 NGRX 效果进行 API 调用

> 原文：<https://medium.com/bb-tutorials-and-thoughts/using-ngrx-effects-to-make-api-calls-in-angular-apps-7b5db74f505b?source=collection_archive---------0----------------------->

## 包含示例项目的逐步指南

![](img/aa413471b0c17e871b399b614888ef76.png)

Photo by [Osman Yunus Bekcan](https://unsplash.com/@osilost?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 Angular 中，我们可以直接通过服务进行 API 调用。我们通常在大型应用程序中使用某种状态管理工具，比如 NGRX，这使得组件之间的整体数据流更加容易。您的组件只知道状态，通常不应该知道…