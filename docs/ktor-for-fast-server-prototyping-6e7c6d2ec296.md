# 用于快速服务器原型制作的 Ktor

> 原文：<https://blog.kotlin-academy.com/ktor-for-fast-server-prototyping-6e7c6d2ec296?source=collection_archive---------5----------------------->

![](img/31e41750a9cd4ebce70255ae9348faa9.png)

Photo by [Mark Seletcky](https://unsplash.com/@seletcky?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Ktor 是一个用 Kotlin 创建服务器和客户端应用程序的框架。正如创造者所说——它的最终目标是为互联的多平台应用提供端到端的解决方案。

在本文中，您将找到简单服务器应用程序的 Ktor 设置和实现的基础。

# 装置

对于快速原型，你通常不需要创建一个新的项目——你甚至可以在一个 Android 项目中添加 Ktor 模块作为一个 Gradle 子项目。具有 Ktor 依赖关系的最基本的 build.gradle 如下所示:

你还应该在`src/resources`中定义`application.conf`文件，这样`application`插件就会知道如何启动你的应用程序:

# 履行

## 运行正常的

让我们从一个简单的端点开始，它将返回预定义的值和 Http 状态代码:

让我们在本地测试这个端点。执行`./gradlew run`并等待应用程序启动。然后使用 curl 或您最喜欢的 rest 客户端，看看会发生什么:

```
$ curl -i localhost:8080/
HTTP/1.1 200 OK
Content-Length: 13
Content-Type: text/plain; charset=UTF-8Test message
```

我们收到了一条原始回复。在应用客户端，我们不喜欢文本响应，我们在任何地方都使用 JSON。如何从你的 API 返回 JSON？没有更简单的了。我们将利用 Ktor JSON 的一个特性。

> KTOR 的特性是可插拔依赖——在大多数情况下，它拦截请求和响应，并能够对它们做一些事情。在应用 DSL 中，我们使用安装功能添加该特性。

添加依赖项:

```
implementation "io.ktor:ktor-gson:$ktor_version"
```

并在应用程序中安装该功能:

如您所见，添加了`mapOf("status" to "Test message")`——Gson 会将该结构解析为可读的 Json。

重新构建项目并再次测试您的端点:

```
$ curl -i localhost:8080/
HTTP/1.1 200 OK
Content-Length: 25
Content-Type: application/json; charset=UTF-8{"status":"Test message"}
```

## 创建、读取、更新、删除

为了使用 CRUD 功能，我们将使用以下带有内存数据源实现的存储库:

## 创建和更新-接收功能

我们将创建`post`路线，在这里我们将**接收**一个合适的项目，将该项目添加到存储库中，并且**用一些**结果**来响应**:

我们在调用中使用了`io.ktor.reqeust.receive<*>`函数——因为我们在前面的步骤中添加了`gson()`, JSON 项将被转换为`Item`数据类。

```
$ curl -i -XPOST -H "Content-type: application/json" 
-d '{
      "id":"3",
      "message":"First message"
    }'
'localhost:8080/items'
```

应该返回什么:

```
HTTP/1.1 201 Created
Content-Length: 36
Content-Type: application/json; charset=UTF-8{"id":"3","message":"First message"}
```

## 删除—调用参数

为了创建`delete`端点，我们将利用**调用参数**属性。

如你所见，我们在路径定义中定义了`{id}`。Ktor 能够**识别和检索路径参数**，所以我们以后可以使用它。

## 阅读—获取

最后，让我们实现`get`端点，它将检索项目的完整列表:

在执行 GET on `/items`之后，您应该会收到您放入存储库的所有项目:

```
$ curl -i localhost:8080/items
HTTP/1.1 200 OK
Content-Length: 77
Content-Type: application/json; charset=UTF-8[{"id":"3","message":"First message"},{"id":"23","message":"Second message"}]
```

# 摘要

*   ✔·科特林首先——不需要处理复杂的基于 Java 的 API
*   ️✔易于入手—只需一个依赖项即可实现
*   ✔可读性—所见即所得。路由 DSL 是定义端点的一种非常优雅的方式
*   ❌没有春天那么多的整合
*   ❌还没有黄金标准

以前我首选的 API 原型工具是 express . js——现在 Ktor 取代了它的位置。我发现它非常容易使用——几乎每一个功能都像我期望的那样工作。对于更大的项目，我仍然会使用 Spring——它更成熟，有很多集成。

那些利弊只是我的拙见。你应该亲自看看 Ktor 如何在后端和前端为你服务。

# 链接和资源

[](https://ktor.io) [## ktor-kot Lin 的异步 Web 框架

### Ktor 是一个框架，用于在连接的系统中使用强大的 Kotlin 构建异步服务器和客户机。

ktor.io](https://ktor.io) [](https://github.com/rozkminiacz/ktor-playground) [## rozkminiacz/ktor-游乐场

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/rozkminiacz/ktor-playground) [](/how-to-create-a-rest-api-client-and-its-integration-tests-in-kotlin-multiplatform-d76c9a1be348) [## 如何在 Kotlin 多平台中创建 REST API 客户端及其集成测试

### 这篇文章是我在博客 xurxodev.com 上的原文的英文翻译。

blog.kotlin-academy.com](/how-to-create-a-rest-api-client-and-its-integration-tests-in-kotlin-multiplatform-d76c9a1be348) 

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)