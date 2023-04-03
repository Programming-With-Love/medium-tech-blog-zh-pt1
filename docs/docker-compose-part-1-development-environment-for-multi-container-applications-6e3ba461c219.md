# Docker 编写第 1 部分——多容器应用程序的开发环境

> 原文：<https://medium.com/bb-tutorials-and-thoughts/docker-compose-part-1-development-environment-for-multi-container-applications-6e3ba461c219?source=collection_archive---------0----------------------->

我们可以用 Dockerfile 创建一个容器并在任何地方运行它。如果我们想在不同的端口上运行多个服务，我们仍然可以在多个终端上用两个 docket 文件来运行它。

但是，如果我们想定义一个包含所有容器细节的配置文件，用一个命令启动容器，并在任何需要的地方使用这个配置文件，那该怎么办呢？答案是 ***docker-compose。***

## docker-撰写