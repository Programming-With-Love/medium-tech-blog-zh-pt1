# 用于 Spring Boot 功能测试的 Testcontainers 和 LocalStack

> 原文：<https://medium.com/capital-one-tech/testcontainers-localstack-for-spring-boot-functional-tests-c4c8d06dae88?source=collection_archive---------4----------------------->

![](img/b1ef1ef2e54adf8586d04cf77d139f28.png)

我们都知道开发新应用程序时单元测试和功能测试的重要性。特别是，功能测试有助于我们看到我们的代码与我们认为我们的应用程序应该具有的功能之间的差距。功能测试的一个主要缺点是它们通常依赖于外部服务，无论是数据库、API 还是…