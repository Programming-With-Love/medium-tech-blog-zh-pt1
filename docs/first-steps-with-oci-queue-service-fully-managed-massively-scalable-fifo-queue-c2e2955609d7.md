# OCI 队列服务的第一步——完全托管的大规模可扩展 FIFO 队列

> 原文：<https://medium.com/oracledevs/first-steps-with-oci-queue-service-fully-managed-massively-scalable-fifo-queue-c2e2955609d7?source=collection_archive---------1----------------------->

在之前的一篇文章中，我已经探索了 OCI 队列的前景——一种在 2022 年 12 月推出的管理队列服务。在本文中，我将展示我在 OCI 队列中的第一次真实活动。小步骤，理所当然。但肯定有助于让您了解这项服务的内容。

我将描述的步骤简单明了:

*   创建队列—定义队列配置(默认保留时间、可见性超时)
*   将消息发布到…