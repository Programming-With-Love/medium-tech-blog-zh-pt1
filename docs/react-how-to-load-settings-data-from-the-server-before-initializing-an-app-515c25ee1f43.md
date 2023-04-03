# React 如何在初始化应用程序之前从服务器加载设置/数据

> 原文：<https://medium.com/bb-tutorials-and-thoughts/react-how-to-load-settings-data-from-the-server-before-initializing-an-app-515c25ee1f43?source=collection_archive---------0----------------------->

## 了解如何使用 useEffect()钩子来加载设置

![](img/0784cc4b69b09aabeb03bff42604f621.png)

Photo by [Mukiibi John Elijah](https://unsplash.com/@mukiibij?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 web 应用中，有时我们必须在引导前端应用程序之前从服务器加载数据。例如，我们必须在前端页面加载之前加载特定于环境的设置，以便我们可以拉所需的…