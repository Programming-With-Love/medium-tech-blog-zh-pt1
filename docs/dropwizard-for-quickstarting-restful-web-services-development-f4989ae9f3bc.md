# 快速启动 RESTful web 服务开发的 Dropwizard

> 原文：<https://medium.com/globant/dropwizard-for-quickstarting-restful-web-services-development-f4989ae9f3bc?source=collection_archive---------1----------------------->

Dropwizard 是一个开源的 Java 框架，用于快速开发高性能 RESTful web 服务。它是一个用于构建 RESTful web 服务的框架，或者更准确地说，是一组用于构建 RESTful web 服务的工具和框架。

Dropwizard 拥有对复杂的**配置**、**应用度量**、**日志**、**操作工具**等的*开箱即用*支持，让您和您的团队在尽可能短的时间内交付一个*生产质量的* web 服务。

## Dropwizard 库

下表总结了 dropwizard 提供和支持的重要库。

![](img/fdf69f1fb007285e5ab1befd57a07276.png)

## 使用 Dropwizard 的简单 REST 应用程序

只需几个步骤就可以在 dropwizard 中创建一个简单的 REST 应用程序:
1。创建一个 maven 项目。

![](img/935230adb57b53297fa6373929fc48d8.png)

提及 groupId、artifactId 和其他详细信息。

![](img/8931fff4e1b2b824ae5a0246a539d254.png)

添加***drop wizard-core***作为依赖。您的 pom.xml 应该如下所示:

![](img/9b369e4ccd62a99e58c9b546621ed79d.png)

下面是添加所有必需依赖项后的 *pom.xml* :

![](img/9c0af12d7b4706dac2be3d0eea1c6bcc.png)![](img/c24196e84ae638bb5cae7eb317ae26d2.png)

2.创建一个新的配置文件 ***conf.yaml*** ，它将保存应用程序属性。将文件放在 resources 文件夹中。请注意 yaml 文件中的*产品*配置，我们将在示例中进一步使用。

![](img/2de4f34bc9e3cac0729fe7ab7a6f4d4e.png)

下面是最终配置的样子。

![](img/14a300d398ce04165819f0fae23079c3.png)

3.在你的应用中定义一个模型类，比如“产品”有一些属性:

![](img/702166fc2aed9673f4e2e84c92645cec.png)

4.用 API 编写一个控制器，它将返回产品资源列表。

![](img/7d89a946eb503466cef7b9b0d8442017.png)

该控制器有两个资源方法，第一个方法返回产品对象列表，第二个方法只返回从默认配置创建的*产品*。为了简单起见，这里没有使用进一步的业务逻辑。您可能会得到一个错误，指出缺少类 DropwizardExampleConfiguration。放松，我们接下来将创建它。

5.创建一个配置类来表示应用程序配置。

![](img/11d6e246573592479da23d8de4d0f984.png)

6.最后，这里是实际启动应用程序的主要应用程序类。 *DropwizardExampleApp* 类除了 main 方法之外还有两个方法， *initialize* ()和 *run()。在 initialize()方法中，****ResourceConfigurationSourceProvider***类在资源中查找配置文件并初始化应用*。*而*，c* 控制器类注册在 run 方法中。

![](img/88c1833f6bde8ba2f5909e8fcbdc5ee7.png)

创建完所有这些类后，项目结构应该是这样的。

![](img/7a2fb2089ee3bda34dbefb01e4166b21.png)

就是这样。REST 应用程序准备就绪。您可以使用 maven-shade-plugin(已经是上面 pom.xml 的一部分)来创建一个 jar 文件，在命令行中执行以下命令将启动服务器。

***Java-jar target/DropwizardExample-1.0-snapshot . jar server conf . YAML***

您将能够在终端上看到类似于以下快照的输出。

![](img/f51742f0034f08c992197aa26bac500e.png)

请注意关于缺少 healthcheck 的警告，Dropwizard 希望每个应用程序都实现 Healthcheck 类，并定期检查应用程序的健康状态。

一旦服务器启动，如果您打开浏览器并键入[*http://localhost:8080/products，*](http://localhost:8080/products,) ，您可以看到 api 返回了一个 json 格式的产品列表。而[*http://localhost:8081/*](http://localhost:8081/)*是一个管理端点，它返回可用指标的列表*

*下面是如何在管理端口上看到指标*

*![](img/a5197e5c347b6d1e8511a4a9f5cf6237.png)*

*下面是*/产品*终点的输出:*

*![](img/84dc6f8c8e0a64303bd96be45e3ad739.png)*

*或者，您也可以使用 integration test 在 ProductController 中测试上面创建的资源。Dropwizard 通过 dropwizard-testing 插件提供测试支持。您可以向 pom.xml 添加以下依赖项来使用该插件*

```
*<dependency>
  <groupId>io.dropwizard</groupId>
  <artifactId>dropwizard-testing</artifactId>
  <version>${dropwizard.version}</version>
</dependency>*
```

*添加完依赖项后，创建一个测试类 ProductConrollerTest。将`DropwizardExtensionsSupport`注释和`DropwizardAppExtension`扩展添加到您的 JUnit5 测试类，它将在任何测试运行之前启动应用程序，并在测试完成后再次停止。`DropwizardAppExtension`还公开了应用程序的`Configuration`、`Environment`和应用程序对象本身，以便这些可以被测试查询。测试类可以通过调用扩展的 Client()方法获得 HttpClient，并执行请求。*

*![](img/7ed95d571e22e265b0c1279e7b95b424.png)*

*从上面的测试中可以看出，您可以使用集成测试来验证两种控制器方法的结果。*

*以上示例的完整源代码可以在这里找到[。](https://github.com/snehalwa/DropwizardExample)*

*总之，如果需要快速开发 REST web 服务，Dropwizard 是一个高效的轻量级框架。它的功能可以通过集成像 Guice 和 DB framework JDBI 这样的 DI 框架来进一步增强。此外，默认情况下，它提供了出色的指标，可以洞察应用程序的性能。*