# OCI Kubernetes 引擎上的分布式应用运行时

> 原文：<https://medium.com/oracledevs/kubernetes-dapr-on-oke-distributed-application-runtime-on-oci-kubernetes-engine-abd9bede9907?source=collection_archive---------0----------------------->

我近年来遇到的最令人兴奋的框架之一是 Dapr ( [dapr.io](https://dapr.io/) ) — *d* 分布式*AP*application*r*untime。Dapr 是一个跨技术的运行时框架，支持多种语言的应用程序。

Dapr 文档描述了如何在本地和 Kubernetes 集群上安装 Dapr。该文档明确链接到 Azure、AWS 和 GCP 文档，但没有提到 OCI 上的 OKE。所以在这篇文章中，我将向你展示如何在 OKE 上使用 Dapr。