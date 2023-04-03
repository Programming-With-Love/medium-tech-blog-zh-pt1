# 利用 JavaFX、Gluon CloudLink 和 Oracle Cloud 进行深空轨迹探索

> 原文：<https://medium.com/oracledevs/deep-space-trajectory-exploration-with-javafx-gluon-cloudlink-and-oracle-cloud-866ee5648ee?source=collection_archive---------0----------------------->

今天，甲骨文发布了一段简短的视频，视频中他们采访了来自人工智能解决方案公司的肖恩·菲利普斯和黛安·戴维斯。在这篇文章中，他们谈到了他们的软件解决方案背后的架构，用于进行极其复杂的深空轨道计算。Sean 过去曾在 JavaOne 和 Oracle Code 大会上展示过这款软件，看到在使用 JavaFX 的用户机器上的富桌面应用程序中生成的惊人的可视化效果总是令人着迷。对于不熟悉这个软件的人来说，人工智能解决方案公司有一个[博客帖子](https://ai-solutions.com/deep-space-trajectory-design-software-ocean-world-orbiters-human-space-flight/)，里面有一些该软件运行的视频。

在下面嵌入的视频中，我们可以看到这是如何在幕后工作的，它是通过 [Oracle 云容器服务](https://cloud.oracle.com/container-opc)、 [Oracle 裸机计算服务](https://cloud.oracle.com/infrastructure/compute)和 [Gluon CloudLink](https://gluonhq.com/products/cloudlink) 的组合实现的。

由于轨迹计算的复杂性，在桌面或移动设备上进行计算需要很长时间。因此，JavaFX 桌面应用程序将大部分计算任务转移给了 Oracle 裸机计算服务，后者提供了强大的计算能力。但是，客户端应用程序和服务器计算之间需要紧密集成，因为更改客户端上的一个参数可能会导致执行新的或额外的计算。

客户端应用程序和 Oracle Bare Metal compute service 之间的通信是通过 Gluon CloudLink 完成的，因为这提供了一种简单的方法来将丰富的用户界面连接到一个或多个云后端，而不需要考虑连接问题、安全性、(解)编组等问题的样板代码……Gluon CloudLink Function Builder 用于配置客户端应用程序和后端系统之间的交互。

一旦计算在 Oracle Cloud 上完成，结果将通过 Gluon CloudLink 返回，并自动同步到请求用户的用户界面，同时考虑到用户可能已经更改了一些设置，例如过滤器参数。由于这种架构，人工智能解决方案能够将其软件部署扩展到移动设备和平板电脑上。事实上，多亏了 [Gluon Mobile](https://gluonhq.com/products/mobile) ，他们现有的 JavaFX 投资直接转移到了移动设备上。

*原载于 2017 年 9 月 14 日*[*【gluonhq.com*](https://gluonhq.com/deep-space-trajectory-exploration-javafx-gluon-cloudlink-oracle-cloud/)*。*