# 将 Fn 功能与 Dapr.io 相结合——利用代理的力量

> 原文：<https://medium.com/oracledevs/marrying-fn-functions-to-dapr-io-leverage-the-power-of-the-proxy-45590d08333a?source=collection_archive---------0----------------------->

项目 Fn 为创建和运行无服务器功能提供了一个框架。它是 Oracle 云基础架构上的功能服务的基础。Dapr.io 是一个开源项目，它为任何应用程序提供了一个强大的个人助理，以及一个特别闪耀着微服务光芒的分布式应用程序运行时。Dapr.io 的某些方面不适用于 Fn 风格的无服务器功能——比如订阅消息主题——但其他方面可能非常有用。Dapr 提供了大量的绑定集合，可以绑定到许多…