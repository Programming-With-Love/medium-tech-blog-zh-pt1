# vue . js-如何在初始化应用程序之前从服务器加载设置/数据

> 原文：<https://medium.com/bb-tutorials-and-thoughts/vue-js-how-to-load-settings-data-from-the-server-before-initializing-an-app-cb7d0123c516?source=collection_archive---------0----------------------->

## 了解如何使用 mounted()生命周期挂钩来加载设置

![](img/87333db8e356d255d803a67719076f44.png)

Photo by [Mike van den Bos](https://unsplash.com/@mike_van_den_bos?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 web 应用中，有时我们必须在引导前端应用程序之前从服务器加载数据。例如，我们必须在前端页面加载之前加载特定于环境的设置，以便我们可以拉…