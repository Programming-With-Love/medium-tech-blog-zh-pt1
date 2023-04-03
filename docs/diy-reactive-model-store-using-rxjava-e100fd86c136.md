# 使用 RxJava 的 DIY 反应模型存储

> 原文：<https://medium.com/google-developer-experts/diy-reactive-model-store-using-rxjava-e100fd86c136?source=collection_archive---------2----------------------->

![](img/018ddaec91fb696adb2e27678db1150f.png)

在 Android 的过去几年中，我们已经看到了基于单向数据流思想的架构的爆炸式增长。

我第一次接触到这个想法是在使用 RxJava 开发一个基于 MVI 的应用程序时。MVI 的一个关键概念是干净地管理应用程序状态的变化。模型存储模式是实现这一目标的关键。

# 使用不可变状态