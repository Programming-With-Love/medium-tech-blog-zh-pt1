# Kubernetes 中负载平衡器 TLS 证书的自动更新

> 原文：<https://medium.com/compendium/automatic-renewal-of-tls-certificates-for-loadbalancers-in-kubernetes-738813e20404?source=collection_archive---------4----------------------->

![](img/ced554c44ecd447427b50b2068d0382e.png)

HTTPs 协议上的安全 web 流量是必须具备的，但不是你想花太多时间的东西。它应该只是简单地工作——并从那里维护自己。然而，这并不总是像听起来那么简单。这篇博客文章将展示如何拥有一个全自动的体系结构来请求、更新和部署证书到 HTTPs…