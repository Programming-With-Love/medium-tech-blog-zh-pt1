# 创建运行剧作家场景的 OCI 函数

> 原文：<https://medium.com/oracledevs/create-oci-function-running-a-playwright-scenario-a92ee7ab6de2?source=collection_archive---------4----------------------->

TL；DR — *如何在 Oracle 云基础设施上创建一个功能，该功能使用剧作家库来运行无头浏览器场景，例如用于 Web UI 运行状况检查和性能监控、用于战术集成和简单 RPA 以及用于基于 Web 的报告。本文展示了一个基于 headless chrome 和剧作家社区容器图像的自定义 Docker 容器图像，以及一个与 Google Translate web ui 交互的自定义节点应用程序；将项目 Fn hotwrap 实用程序添加到此映像中，以提供从 OCI FaaS 无服务器* …