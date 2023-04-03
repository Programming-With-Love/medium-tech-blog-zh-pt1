# 在 Oracle 容器引擎(OKE)上试验入口控制器—第 1 部分

> 原文：<https://medium.com/oracledevs/experimenting-with-ingress-controllers-on-oracle-container-engine-oke-part-1-5af51e6cdb85?source=collection_archive---------1----------------------->

在之前的一篇文章中，我简单的提到了创建一个 Kubernetes [入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)和[入口控制器](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)，但是没有解释太多。在这篇文章中，我将简要解释它们是什么，然后在 OKE 上用 4 个流行的入口控制器进行实验。

## 入口和入口控制器

你可以在这篇精彩的[帖子](/@cashisclay/kubernetes-ingress-82aa960f658e)或 [HAProxy 的](https://www.haproxy.com/blog/dissecting-the-haproxy-kubernetes-ingress-controller/)中读到更多关于他们的内容，但我会在这里给你一个总结。Kubernetes 入口本质上是一组处理外部…