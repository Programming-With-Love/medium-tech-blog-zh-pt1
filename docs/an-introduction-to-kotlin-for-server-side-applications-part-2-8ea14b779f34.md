# 用于服务器端应用程序的 Kotlin 介绍—第 2 部分

> 原文：<https://medium.com/globant/an-introduction-to-kotlin-for-server-side-applications-part-2-8ea14b779f34?source=collection_archive---------3----------------------->

![](img/72845a859b9b08402490ba873c951c10.png)

Photo by [Andrew Neel](https://unsplash.com/@andrewtneel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

欢迎来到本系列的第 2 部分…！！！！

正如我们在本系列的第一部分所看到的，它涵盖了大多数理论概念。在本文中，我们将构建一个小型的 todo 应用程序。它将涵盖 REST 和 JSON 处理概念。

我们将使用 MongoDB 服务器在其中存储待办事项。好吧，我们开始吧。首先，我们将创建一个服务器和一些路由。

我们已经创建了一个服务器并引入了一些路由。路线现在是空的，但是在此期间，我们将增加它的生命。

现在让我们创建一个模型，在我们的应用程序中只需要一个模型。

基本上，模型包含核心应用程序信息。它包括数据、验证规则等。在我们的例子中，这是相同的，因此我们已经定义了应用程序中需要的字段。

到目前为止，我们已经创建了服务器、路由和模型。现在，我们将看到应用程序的核心部分。其中主要包含业务逻辑。

上面的代码非常简单，我们刚刚创建了一些简单的方法来返回一些东西。现在，是时候用 util 函数连接我们的路线了。我们现在将返回请求。

我们对主文件做了一些修改。添加了镜头，以检索单个和多个待办事项，并从网址的 ID。到目前为止，我们只是玩了玩路线和工具。

## 有趣的部分现在开始了。

是的…！！！我们将把应用程序与 MongoDB 服务器连接起来。您需要在 Gradle 文件中添加下面一行来使用 MongoDB 驱动程序。

```
compile group: 'org.mongodb', name: 'mongo-java-driver', version: '3.12.1'
```

现在，让我们对我们的 util 函数进行修改。你可以参考这个[链接](https://mongodb.github.io/mongo-java-driver/3.4/driver/getting-started/quick-start/)来获取更多关于 MongoDB 驱动的信息。

> 注意:文档主要是针对 JAVA 的，但是你也可以参考 Kotlin。

这是我最喜欢科特林的地方。你可以使用为 JAVA 设计的插件。

在上面这段代码中，我们创建了一个新实例，并使用该实例连接到数据库和 getCollection()方法中所需的集合。

getCollection()方法是使用 MongoClient 实例创建的，因此它拥有 MongoDB APIs 的所有访问权限。我们已经调用了执行相应操作所需的 API。

现在让我们给路线添加一些生命，以便我们可以继续检查 API 是否工作。

就这样，我们使用 Kotlin 和 MongoDB 创建了一个 todo 应用程序。因此，我们在本文中讨论了以下几点。

1.  使用 Kotlin 创建了一个真实的应用程序。
2.  创建了 REST APIs，我们已经看到了如何处理 JSON。
3.  我们已经看到了 Kotlin 的互操作性特性，它利用了只为 JAVA 构建的依赖关系。

这里是这个应用程序的 Github 库链接。

团队演职员表:[穆昆德](https://medium.com/u/733fa45e5564?source=post_page-----8ea14b779f34--------------------------------) | [尼基尔·贾达夫](https://medium.com/u/62658ead3a79?source=post_page-----8ea14b779f34--------------------------------) | [阿克沙塔·谢拉](https://medium.com/u/bb9487b17334?source=post_page-----8ea14b779f34--------------------------------) | [阿尔恰纳](https://medium.com/u/ddb5232e0b42?source=post_page-----8ea14b779f34--------------------------------)