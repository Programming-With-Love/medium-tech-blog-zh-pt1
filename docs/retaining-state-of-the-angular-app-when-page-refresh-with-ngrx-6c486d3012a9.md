# 使用 NGRX 刷新页面时保留 Angular 应用程序的状态

> 原文：<https://medium.com/bb-tutorials-and-thoughts/retaining-state-of-the-angular-app-when-page-refresh-with-ngrx-6c486d3012a9?source=collection_archive---------0----------------------->

## 使用一个示例项目重新合并 ngrx/store

![](img/2581554e746a6c6957eb34904522a798.png)

Photo by [Peter Osmenda](https://unsplash.com/@digitalunknown?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

web 应用大部分是用 Angular、React、Vue.js 等 SPA 框架编写的。这些 SPAs 的问题是，单个页面在浏览器中加载一次，然后框架会处理所有页面之间的路由，并给用户留下印象…