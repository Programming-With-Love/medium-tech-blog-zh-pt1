# 中途:简化被嘲笑的回答

> 原文：<https://medium.com/walmartglobaltech/midway-simplifying-mocked-responses-da5601fc667d?source=collection_archive---------4----------------------->

![](img/65fd544c128eee31983f3c8670420ebc.png)

[http://i2.wp.com/www.datadependence.com/wp-content/uploads/2016/03/MOCKS-1.jpg](http://i2.wp.com/www.datadependence.com/wp-content/uploads/2016/03/MOCKS-1.jpg)

> 这篇博客内容假设你对[测试舰队的](http://testarmada.io)开源嘲讽舰队[中途](https://www.npmjs.com/package/testarmada-midway)有基本的了解，并且经历过[中途的训练](http://testarmada.io/documentation/Mocking/rWeb/JAVASCRIPT/Training%20Guide/) 101 和 201。

通过移除所有下划线依赖性来孤立地测试和调试应用程序是非常强大的——这导致了模拟的使用。尽管模拟非常强大，但它同时也非常复杂和费力，尤其是在处理可能需要模拟数十个外部服务的测试用例或场景时，并且可能导致测试:

*   更难理解
*   更难维护

> 当以不同的数据预期多次调用多个 Rest APIs 时，Mocks 的复杂性会增加。

如果我告诉你，使用 Midway 的***Mock from Container***特性，在为复杂场景创建 Mock 时，你可以告别大部分的复杂性，会怎么样？如果这让你兴奋，继续读下去…

当使用内联测试用例模拟或者通过使用模拟服务器来创建模拟时；复杂测试用例的最大挑战是设置模拟*(基于被模拟的 API 被调用的次数，每次都返回单独的数据集)*并在以后维护它们，特别是如果被测试的特性不断发展的话。Midway 的***Mock from Container***特性使得在使用 Midway-Server 进行模仿时，所有被模仿的响应都能够从特定的数据容器中返回，并且与测试用例实现完全分离，这使得维护它们变得轻而易举。

# 它是如何工作的？

**场景:**假设您的测试用例或场景需要模拟两个 GET Rest API`/api/message`和`/api/product/getStatus`，两个端点都被调用了三次。对于第一个端点，您总是希望使用 HTTP 代码 200 返回相同的数据(在 JSON 中),而对于第二个端点，您希望使用 HTTP 代码 200 为第一次和第三次调用返回相同的数据(在 html 中),并使用 HTTP 代码 201 为第二次调用返回不同的数据(在 JSON 中)。

**实现:**在你的测试目录*的`*mocked-data*`文件夹下创建一个文件夹(这个文件夹的名字可以在你启动*中途服务器*时通过传递* `*mockedDirectory*` *选项来配置)*的名字`*test1*`。在这个文件夹下，为您的模拟响应添加以下文件。

1.  `*api-message-GET.json*` -这将为第一个端点的所有调用返回默认 HTTP 响应代码 200
2.  `*api-product-getStatus-GET.html*` -这将为第二个端点的所有调用返回默认的 HTTP 响应代码 200，除了第二个端点，因为它有自己的文件
3.  `*api-product-getStatus-GET-2-code-201.json*` -这将为第二个端点的第二次调用返回响应代码 201。

通过 Midway 对象、Midway-UI 或 REST API——可以调用 *SetMockId* API 来设置值:

```
midway.setMockId(“test1”);or curl http://localhost:8000/_admin/api/midway/setMockId/test1
```

**解释:**underline Midway mock 服务会自动计算出文件扩展名，因此您不必指定它。如果同一文件具有多个文件扩展名，则使用以下顺序:

*   JSON
*   超文本标记语言
*   文本文件（textfile）
*   遇到任何其他扩展名的第一个文件。

**带有模拟响应数据的文件名**是基于被模拟的 Rest API 创建的，例如 Rest API `*/api/message*`的文件名是`*api-message-GET.json*`。方法名用于区分不同的 Rest 方法，因为同一个 Rest API 路径可以支持两种不同的 REST 方法。如果在上面的例子中，我想为第一个请求返回一个不同的响应，那么我会添加另一个名为`*api-message-GET-1.json*`的文件。然后，对于第一次调用，将使用`*api-message-GET-1.json*`，对于所有其他调用，将使用默认文件`*api-message-GET.json*`。对于 POST 调用，文件名将简单地更改为`*api-message-POST.json*`。

> 小费！一旦调用了`midway.setMockId(“test1”)` API，Midway 只在`test1`文件夹下寻找响应。如果没有找到响应，它将返回带有没有找到的文件名的`404`。在调用`midway.setMockId(“test1”)` API 后，Midway 在内部跟踪每个单独端点被调用的次数，并首先查找具有 count 特定名称的文件，如`api-message-GET-1.json`，如果没有找到所述文件，则查找默认文件`api-message-GET.json`。

## **很高兴知道**

如果设置了 *SetMockId* ，则自定义文件路径

```
 midway.util.respondWithFile(this, reply,{filePath:‘./default.json’}
); 
```

和基于 URL 路径的文件`*../mocked-data/api/message/get/default.json*`被忽略。以下是文件查找遵循的顺序:

*   SetMockId
*   默认或变体端点的自定义文件路径。
*   基于默认或变体端点的 URL 路径的文件。

## **其他公用事业**

设置完 *SetMockId* 后，您可能需要重置 URL 计数或重置模拟 Id，以返回到自定义文件路径或基于 URL 或变量的文件路径。为此，您可以使用 UI、Rest APIs 或 Midway 命令:

*   **ResetCount** —将 URL 调用计数重置为零，并且不影响当前模拟 id。此后，Midway 将从零开始计算端点呼叫计数。

```
midway.resetCount();or curl http://localhost:8000/_admin/api/midway/resetURLCount
```

*   **ResetMockId** —这将删除当前的 *SetMockId* 并将 URL 计数设置为零

```
midway.resetMockId();orcurl http://localhost:8000/_admin/api/midway/resetMockId
```

*   **GetMockId** —获取中途服务器的当前 mock-id 集

```
midway.getMockId();orcurl http://localhost:8000/_admin/api/midway/getMockId
```

*   **GetURLCount** —获取所有被中途服务器模拟的 Rest APIs 的 URL 计数

```
midway.getURLCount();or curl http://localhost:8000/_admin/api/midway/getURLCount
```

## ***模仿来自容器方法的优点:***

当您想要为一个特定的测试存根化所有的端点，而不需要手动为这些端点编写路径时，这个特性是非常方便的。通常适用于一个流需要多次调用一个或多个端点，且预期数据结果不同的场景。

*   **Stub Multiple Rest API**—根据 Rest API 路径，根据您的场景需要，在模拟文件夹中放置尽可能多的文件。
*   **多次调用模拟 Rest API&不同的数据预期** —将调用计数添加到文件名中，该文件将根据 API 被调用的时间返回。
*   **降低测试复杂性** —测试与设置模拟响应分离，从而使测试易于阅读。
*   **无变体** —对于同一个 API 的多次调用，可以为不同的数据集添加一个新文件，这样就不需要创建任何变体。
*   **易维护** —如果发生任何变化，只需更改文件内容或名称。
*   **开发者天堂** —让开发一个特性变得超级简单，因为如果模仿行为需要改变，不需要修改任何代码。

***敬请关注未来的博客，深入了解架构和特性。与此同时，请随意浏览中途的*** [***文档***](http://testarmada.io/documentation/Mocking/rWeb/JAVASCRIPT/Introduction) ***或者尝试从*** [***这里***](https://www.npmjs.com/package/testarmada-midway) ***下载。***

*相关博客* : [中途:沃尔玛的嘲讽之旅……](/walmartlabs/midway-walmarts-mocking-journey-84c34fcc4593)