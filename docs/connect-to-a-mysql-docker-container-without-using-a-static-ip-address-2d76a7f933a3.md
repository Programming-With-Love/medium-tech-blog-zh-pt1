# 在合成中连接容器

> 原文：<https://medium.com/capital-one-tech/connect-to-a-mysql-docker-container-without-using-a-static-ip-address-2d76a7f933a3?source=collection_archive---------2----------------------->

## 如何不使用静态 IP 地址连接到 MySQL Docker 容器

![](img/b7ea2ef6374f29e6c3200a6844633b17.png)

> **TL；DR-使用 Docker Compose 简化您的代码！**

如果您在同一个应用程序中处理不止一个容器，那么它们很可能需要相互通信，但是拥有静态的 IP 地址。这个的代码看起来…