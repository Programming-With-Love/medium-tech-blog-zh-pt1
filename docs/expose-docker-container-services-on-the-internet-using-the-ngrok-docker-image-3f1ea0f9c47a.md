# 使用 ngrok docker 映像在互联网上公开 Docker 容器服务

> 原文：<https://medium.com/oracledevs/expose-docker-container-services-on-the-internet-using-the-ngrok-docker-image-3f1ea0f9c47a?source=collection_archive---------0----------------------->

挑战:您正在 Docker 容器中、本地笔记本电脑上或基于云的 VM 或容器平台上运行服务、API 或 web 应用程序。您希望向外部消费者提供访问权限，包括您自己的智能手机、运行在云环境中的一段代码、本地网络上的同事或世界另一端的人。问题是:如何将这些外部方的请求发送到不公开也不能公开外部端点的应用程序。