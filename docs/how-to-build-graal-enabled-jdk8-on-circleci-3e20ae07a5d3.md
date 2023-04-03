# 如何在 CircleCI 上构建支持 Graal 的 JDK8？

> 原文：<https://medium.com/oracledevs/how-to-build-graal-enabled-jdk8-on-circleci-3e20ae07a5d3?source=collection_archive---------0----------------------->

## 一篇关于如何在 CircleCI 这样的 CI/CD 环境中构建嵌入了 GraalVM 编译器的 JDK8 二进制文件的帖子。

![](img/143abb68a5a0af562210cb10269e4706.png)

GraalVM 编译器 T1 是 HotSpot 服务器端 T2 JIT 编译器 T3 的替代品，也就是广为人知的 T4 C2 编译器 T5。它是用 Java 编写的，目标是获得更好的性能(以及其他目标),因为…