# Oracle 云基础架构队列服务中的延迟交付-基于时间的消息发布

> 原文：<https://medium.com/oracledevs/delayed-delivery-in-oracle-cloud-infrastructure-queue-service-time-based-message-release-581402e03d51?source=collection_archive---------0----------------------->

发布到 Oracle 云基础架构(OCI)队列的消息可能不会立即用于消费。让消息只在特定的时刻可供使用，而不是立即使用，这种要求并不少见。OCI 队列目前没有发布邮件时对“延迟传递时间戳”的内置支持。然而，通过它的可见性超时机制，实现非常接近的功能是非常简单的。