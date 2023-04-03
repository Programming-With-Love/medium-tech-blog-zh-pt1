# 电极配置

> 原文：<https://medium.com/walmartglobaltech/electrode-confippet-85bafe1c9a42?source=collection_archive---------3----------------------->

![](img/46cec8f025a113995bf442c5c6cb3e94.png)

[电极](https://www.electrode.io/)，由[walmartlab](https://www.walmartlabs.com/)开发的开源平台，用于构建强大的网络和移动应用。[电极配置](https://github.com/electrode-io/electrode-confippet)是[电极网](https://www.electrode.io/site/web.html)中众多可用模块之一，用于管理 Node.js 应用的配置。在这篇博文中，我们将深入了解[电极配置](https://github.com/electrode-io/electrode-confippet)。

# 为什么 Confippet

在软件工程中，当我们使用 **SOA** 或**微服务**架构开发 web 应用或服务时，我们会有**频繁变化的键/值对**属性。这些变化的属性的几个例子可以是`URL:port`、`connection timeout`、`response timeout` 用于我们所依赖的特定服务、`DB connection string`以及各种`framework settings`等。使用配置文件比直接在应用程序代码中硬编码它的好处是，我们有一个集中的配置文件存储库，存放所有这些属性。在这些属性文件中更改一个值比搜索整个代码库来找出在哪里进行更改要快很多倍。它为读取整个应用程序的配置属性提供了统一的界面。我们可以形象地将 confippet 看作一个提供两种操作的容器:

*加载* —加载一堆配置文件，其中文件的**加载顺序**由其**顺序**值定义，值被合并，给定`key`的冲突值被具有`greater order value` 的文件覆盖。

*get(key)* —为给定的键检索一个**可选的**值。

# 积极着手进行

![](img/502c6d8341f2ffcba6fcdc3b855fdccb.png)

Confippet 遵循 [*node-config*](https://github.com/lorenwest/node-config/wiki/Configuration-Files#file-load-order) 文件的约定，在`config/`目录下编译文件，用法非常简单。

1.  去一个文件夹，做`npm install electrode-confippet --save`
2.  创建一个名为`config`的文件夹，添加`3`文件:`default.json`、`development.json`、`production.json`。

`default.json`

```
{
  "settings": {
    "db": {
      "host": "localhost",
      "port": 5432,
      "database": "clients"
    }
  }
}
```

`development.json`

```
{
  "settings": {
    "db": {
      "host": "development-db-server"
    }
  }
}
```

`production.json`

```
{
  "settings": {
    "db": {
      "host": "prod-db-server"
    }
  }
}
```

3.要使用`electrode-confippet`，我们需要做的是，

`run.js`

```
const config = require("electrode-confippet").config;
const db = config.$("settings.db");
```

4.我们可以在两种模式下运行:

*   `node run.js` —这将首先加载`default.json`，然后加载`development.json`，这将确保`development.json`中相同键的值将覆盖来自`default.json`的值。confippet 的加载顺序:`default.json < development.json` 。`settings.db`的值将为`development-db-server`。
*   `NODE_ENV=production node run.js` —这将首先加载`default.json`，然后加载`production.json`，这将确保`production.json`中相同键的当前值将覆盖来自`default.json`的值。confippet 的加载顺序:`default.json < production.json`。`settings.db`的值将为`prod-db-server`。

特别是，confippet 根据`order`值加载文件，合并值，给定`key`的冲突值被具有`greater priority`的文件覆盖。我们能否尝试用编程中其他领域的概念来解释 confippet 的这种行为？

*   *继承和覆盖* —在传统的面向对象编程中，继承使新对象能够呈现现有的属性。在上面的例子中，`development.json`和`production.json`能够重用来自`default.json`的键/值对。同样，在面向对象编程中，覆盖是一个特性，它允许子类或子类提供一个方法的特定实现，该方法已经由它的一个超类或父类提供。在上面的例子中，`default.json`中`host`的值被`development.json`和`production.json`覆盖。
*   *事件顺序* —在编程的许多其他领域中，一个事件在另一个事件发生之前发生的顺序。例如，从服务器上的数据库复制必须按照它们在主服务器上应用的顺序进行，以保证`ACID`。在`redo`日志中发生的写操作的顺序在`master`中被捕获，并在从机中以相同的顺序准确重放。在上面的 confippet 示例中，文件加载的排序是基于它们的`order`值来完成的，使用它们可以实现键/值对的重用和覆盖。

# 容纳不同的观点

![](img/627a191cab9067a64832d2cbeb962f15.png)

如前所述，`confippet`根据`config`文件夹下的`NODE_ENV`环境变量，遵循 [*节点配置*](https://github.com/lorenwest/node-config/wiki/Configuration-Files#file-load-order) 文件的约定，自动加载`default.json`、`development.json`或`production.json`。但是不同的应用程序希望在不同的`order`集合中加载更多(或更少)不同文件名的文件。 [WalmartLabs](https://www.walmartlabs.com/) 使用 [OneOps](http://oneops.com/) 在我们的云环境中部署我们的应用。每个应用程序都有不同的云环境，如`oneops-development`、`oneops-staging`、`oneops-production`等。，他们希望根据应用程序运行的云环境加载不同的配置文件。Confippet 非常易于配置，可以根据各种客户需求进行定制。为了实现这一点，我们需要通过添加如下所示的 [OneOps](http://oneops.com/) 配置文件来扩展`providers`，并将附加配置传递给`Confippet.presetConfig.autoLoad` api。

能够根据不同的客户需求扩展 confippet 是一个出色的特性，这使得它比 [*节点配置*](https://github.com/lorenwest/node-config/wiki/Configuration-Files#file-load-order) *更加强大。*流行的库和框架通常*适应不同的观点*。例如，Spring Framework 支持灵活性，并且不会固执己见地认为事情应该如何完成。它从不同的角度支持广泛的应用需求。我们可以在这里阅读更多关于 Spring 框架设计哲学[的内容。](https://docs.spring.io/spring/docs/current/spring-framework-reference/overview.html#overview-philosophy)

我们可以使用 Confippet 的`composeConfig`特性进行更多的定制。更多信息，请阅读此处的[和](https://github.com/electrode-io/electrode-confippet#customization)

# 电极服务器+ Confippet —强大的组合

[电极服务器](https://github.com/electrode-io/electrode-server)提供了一个标准化的节点 web 服务器，可以使用 [Hapi.js](https://hapijs.com/) 在 [Node.js](https://nodejs.org/en/) 之上为您的 web 应用提供服务。电极服务器使用电极配置来引导 web 应用程序。在配置文件中，电极服务器标准化了我们配置日志设置的方式、与下游服务对话的客户端配置、可由前端代码访问的注册 UI 配置、引导索引页面的各种插件、收集指标等。，下面显示了配置的要点。在我们的生产就绪应用程序中，这些配置文件大约有 100 到 300 多行，具体取决于应用程序。

# 规模经济

![](img/c1cea5fae4049319cb42238c39a8340d.png)

[在](https://www.electrode.io/) [沃尔玛实验室](https://www.walmartlabs.com/)，[电极](https://www.electrode.io/)被整个公司用来驱动前端网络应用。如上所述，电极服务器和电极配置工具标准化了 web 应用程序的引导。同样，Electrode 有大量的模块，可以帮助开发人员以多种方式构建 web 应用程序。因此，在一个团队中使用电极框架的开发人员可以无缝地在一个完全不同的电极 web 应用程序中工作，为网站中的不同页面提供动力，并且加速时间非常短。工程师已经熟悉电极模块如何协同工作、电极伪影如何产生、使用 OneOps 将电极构件部署到生产中的流程，以及如何在生产中监控电极应用。唯一缺少的是新应用程序的领域知识。本质上，电极有助于标准化在生产中如何构建、部署和监控 web 应用程序，这是一个强大的模型，提供了巨大的规模经济，并帮助组织更快地创新。电极成为共享词汇的一部分，使得更多的团队使用它来构建更新的 web 应用程序，在应用程序团队和电极平台团队之间创建了一个强大的反馈循环，以持续改进电极平台，并最终成为一个[雪球效应](https://en.wikipedia.org/wiki/Snowball_effect)。编写一次，在任何地方运行是 Sun Microsystems 提出的口号，用来说明 Java 语言的跨平台优势。同样，Electrode 体现了这样一个事实，即一个强大的平台能够让开发人员以标准化的方式构建、部署和监控 web 应用程序，从而提供巨大的规模经济，并真正帮助企业高效运营。

# 结论

用于管理配置的电极配置工具是一个灵活的、可扩展的通用库，可以通过多种方式构建生产就绪型应用程序。我们展示了电极服务器如何使用电极配置程序在整个组织中以一致的方式引导 web 应用程序。我真诚地希望这篇博客帖子能够激发您开始研究 Confippet 模块的兴趣，最终推动您更深入地研究电极生态系统，并希望在您的公司中使用电极来构建强大的网络应用程序，就像我们在沃尔玛实验室所做的那样。

感谢[马达夫·德弗肯达](https://medium.com/u/e4a810d1adaf?source=post_page-----85bafe1c9a42--------------------------------)和[亚历克斯·格里戈良](https://medium.com/u/54a709eacdb7?source=post_page-----85bafe1c9a42--------------------------------)帮助我审阅和发表这篇博文。