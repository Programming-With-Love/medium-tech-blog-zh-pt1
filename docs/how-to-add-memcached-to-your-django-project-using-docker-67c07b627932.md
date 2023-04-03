# Django 与 docker 的 Memcached

> 原文：<https://medium.com/quick-code/how-to-add-memcached-to-your-django-project-using-docker-67c07b627932?source=collection_archive---------0----------------------->

使用 Docker 将 Memcached 添加到 Django 项目的指南。
在这本操作手册中，我将向您展示如何配置 docker-compose.yml，
安装依赖项，编写 Django 项目的配置，
并通过代码示例向您展示这种缓存解决方案的优势。

![](img/3bf7463966b1f754acab65c4919aeb9b.png)

Photo by [Kevin Ku](https://unsplash.com/@ikukevk?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 为什么是 Memcached？