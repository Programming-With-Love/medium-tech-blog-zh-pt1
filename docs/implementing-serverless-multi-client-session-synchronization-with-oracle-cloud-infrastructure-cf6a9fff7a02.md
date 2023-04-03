# 使用 Oracle 云基础架构实施无服务器多客户端会话同步

> 原文：<https://medium.com/oracledevs/implementing-serverless-multi-client-session-synchronization-with-oracle-cloud-infrastructure-cf6a9fff7a02?source=collection_archive---------0----------------------->

TL；灾难恢复—多客户端会话可以通过完全无服务器的方式实现。两个玩家在玩井字游戏或国际象棋，团队在一个文档或图表上协作，观众在观看演示者的演示——这些只是多个分布式应用程序实例应该保持同步的例子。这可以用没有后端独立应用程序来完成。本文演示了在 Oracle 云基础架构上的一个简单设置——使用无服务器功能(这不是…