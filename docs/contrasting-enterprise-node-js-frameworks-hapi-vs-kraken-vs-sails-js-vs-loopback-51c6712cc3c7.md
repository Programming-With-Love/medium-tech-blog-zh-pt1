# 对比企业 Node.js 框架:哈比神 vs 北海巨妖 vs. Sails.js vs. Loopback

> 原文：<https://medium.com/capital-one-tech/contrasting-enterprise-node-js-frameworks-hapi-vs-kraken-vs-sails-js-vs-loopback-51c6712cc3c7?source=collection_archive---------1----------------------->

![](img/11d1750f400bfe0e591c0f6053125e72.png)

[Node.js](https://nodejs.org/) / [Io.js](https://iojs.org/) 是一个基于 JavaScript 语言的非阻塞 I/O 平台，在过去的几年里，它一直吸引着软件开发人员的注意力。它不仅为初创公司和小公司的开发人员这样做，更有趣的是也为企业开发人员这样做。例如， *PayPal，LinkedIn，Walmart，网飞，易贝，优步和其他科技巨头都已经从 Java 等转向 Node* 。它的流行归功于更好的性能和对所有层使用一种语言(JavaScript ):后端、数据库和前端。

与任何新平台一样，有许多 Node.js/Io.js 框架可供选择。然而，在我们继续之前，我们需要定义*企业*的含义。

> 为了简单起见，一个企业项目是这样的:你有超过十个开发人员的团队，你有巨大的流量要处理，高风险的服务必须 24x7x365 运行。

判断框架，尤其是在企业层面，是非常主观的。在构建企业级应用程序时，我们需要考虑以下几点:

**1。最佳实践和模式:**框架是 DIY 的还是提供清晰的模式来使用。

**2。配置:**配置框架有多容易。

**3。惯例:**如果这是首选路线，是否有惯例可循？

**4。水平扩展:**扩展用这个框架构建的应用程序有多容易。

**5。测试:**如何测试应用。

**6。Scaffolding:** 与使用内置代码生成器相比，开发人员需要手动编写多少代码。

7。监控:如何监控应用程序

8。跟踪记录:一个框架是如何被证明的，例如，谁支持它，它被维护的有多好。

9。集成:插件/连接器的生态系统有多丰富。

10。ORM/ODM: 有没有对象关系/文档映射器。

虽然性能很重要，但它会根据特定项目的需求和业务逻辑而变化。运行有意义的基准测试是非常重要的。

这篇帖子的主要重点是比较四个 Node.js/Io.js 框架:[哈比神](http://hapijs.com/)、[北海巨妖](http://krakenjs.com/)、 [Sails.js](http://sailsjs.org/) 和[回环](http://loopback.io/)。有许多其他的框架需要考虑，但是我必须在某处划清界限。

像 [Express.js](http://expressjs.com/) 和 [Restify](http://mcavage.me/node-restify) 这样的框架，虽然在企业项目中被广泛使用，但由于其 DIY 性而被排除在外；这意味着它们提供很少的结构，并且需要引入许多其他模块来配置它们，以完成最基本的工作。另一方面，黑客马拉松的典型代表 Meteor 框架被排除在外，因为我找不到它的任何大型企业部署。如果你知道任何，给我一个链接。

> 有人可能会说，比较这些框架就像比较苹果和橙子一样。在某种程度上，的确如此。

哈比神和回送可能属于稍微不同的子类别，但事实是它们的类别足够接近，因为框架解决了构建可伸缩或可维护的 web 应用程序的类似高级问题。

在 Sails、北海巨妖、LoopBack 或哈比神上交付成功的企业应用程序是可能的。甚至可以使用 Express.js 或核心 Node.js 模块构建高规模和大型项目。事实上，当除了 DIY 别无选择时，我们在 Storify 就是这么做的。

那么使用一个极简的 Node.js 框架有什么好处呢？代价是增加了时间和更难的可维护性，因为当团队选择开源项目时，他们可以利用其他贡献者进行维护。当同一个团队选择了一个仅由该公司支持的闭源内部系统时，情况就不一样了。

最后，你需要自己思考，自己做决定。您的目标应用程序可能会关注和/或需要不同的东西，本文只能强调某些事实，即使这样也很可能是以一种主观的方式，就像一个人写的或说的几乎任何事情一样。；-)

让我们根据这些项目来讨论哈比神、北海巨妖、风帆和回环框架。

# **哈比神**

[哈比神](http://hapijs.com/)(用于 **H** TTP **API** 服务器)由[沃尔玛实验室](http://www.walmartlabs.com/)支持，因此它显然有着在生产中服务大量流量的良好记录( [#nodebf](https://twitter.com/hashtag/nodebf) - Node 黑色星期五)。

哈比神内置了对输入验证、缓存、身份验证和其他功能的支持。它没有提供开箱即用的 ORM/ODM，但是有一个广泛的第三方插件列表。

**哈比神的强大之处在于你可以在请求处理上获得很大的控制权**。这在企业应用程序中很方便，因为它们需要处理大量的逻辑。

**其他哈比神优点:**

基于插件的架构:挑选模块来扩展你的应用

缓存:提高性能

以配置为中心:使用配置文件

丰富的 web 服务器功能:加速您的开发

详细的 API 文档:快速学习框架

可靠的跟踪记录和支持:从社区和贡献者那里获得支持

支持微服务:使用 [Seneca](http://senecajs.org/) 和 [chairo](https://github.com/hapijs/chairo) 插件获得更好的业务逻辑分离和可伸缩性。

**哈比神的缺点:**

开发人员需要自己找出代码结构

“锁定”开发者使用 hapi 特有的[模块](http://hapijs.com/plugins)和插件，如 catbox、joi、boom、tv、good、travelogue 和 yar 并且与 Express/Connect 不兼容

端点是手工创建的

重构是手动的

手动测试端点

没有内置的 ORM/ODM 本身并不是一个缺点，因为并不是所有的企业应用程序都需要数据库。例如，从遗留 SOAP 服务中提取数据的编排层不需要带有模型和模式的 MongoDB 驱动程序，因为它从服务中获取数据，并可能在 Redis 中缓存数据。

就代码而言，哈比神不同于本文中的其他框架，因为它不是构建在 Express 之上的。这种架构需要熟悉 Express 的开发人员额外学习(正如我们大多数人一样)，因为他们无法将自己的 Express.js 技能应用到哈比神。

具有两条路由 GET /和 GET /name 的简单哈比神服务器如下所示:

```
**var** Hapi = require('hapi');**var** server = **new** Hapi.Server();
server.connection({ port: 3000 });server.route({
    method: 'GET',
    path: '/',
    handler: **function** (request, reply) {
        reply('Hello, world!');
    }
});server.route({
    method: 'GET',
    path: '/{name}',
    handler: **function** (request, reply) {
        reply('Hello, ' + encodeURIComponent(request.params.name) + '!');
    }
});server.start(**function** () {
    console.log('Server running at:', server.info.uri);
});
```

正如您所看到的，每个路由都被定义为一个 JSON 对象，并且有许多属性，开发人员可以通过这些属性来控制这个路由的行为。

要开始使用 npm，只需像安装任何其他依赖项一样安装哈比神:

```
$ npm **install** hapi --save
```

在撰写本文的最后一个月(2015 年 7 月)，哈比神的社交证明获得了 4337 颗 GitHub 星和 67390 次 npm 下载。

网址:[http://hapijs.com](http://hapijs.com/)

GitHub:【http://github.com/hapijs/hapi 

https://www.npmjs.org/package/hapi

# **北海巨妖**

[北海巨妖](http://krakenjs.com/)本质上并不是一个框架，它更多的是 Express.js 之上的一层，在处理大型项目时**提供了额外的结构和控制**。北海巨妖是由贝宝的工程师建造的。他们遇到了特定的问题，并通过使用 Express.js 和增加的组织级别解决了这些问题。

北海巨妖为 Node.js 开发人员提供了以下主要优势:

1.内部化:让你的应用程序从头开始支持多种语言。

2.安全性:预配置模块[张亿嘟嘟](http://github.com/krakenjs/lusca)提供简单而关键的最佳应用安全实践。

3.开发速度:使用生成器自动创建代码，节省开发时间并减少人为错误。

**其他北海巨妖优点:**

比 Express.js 更多的现成功能

与基本的 Express.js 相比，结构和代码组织清晰

安全模块

代码生成器

熟悉 Express.js 的开发人员可以轻松学习

可以利用北海巨妖丰富的 Express.js/Connect 中间件模块生态系统

**北海巨妖的缺点:**

没有内置模型或 ORM/ODM

DIY 方法(这可能是高度定制项目的优势)

不是很全面

一个简单的北海巨妖服务器看起来很像 Express.js 服务器:

```
'use strict';**var** kraken = require('kraken-js'),
    app = require('express')(),
    options = {
        onconfig: **function** (config, next) {
            config.get('view engines:js:renderer:arguments').push(app); next(null, config);
        }
        /* more options are documented in the README */
    },
    port = process.env.PORT || 8000;app.use(kraken(options));app.listen(port, **function** (err) {
    console.log('[%s] Listening on http://localhost:%d', app.settings.env, port);
});
```

从北海巨妖脚手架开始:

```
$ npm install -g yo generator-kraken bower grunt-cli
$ yo kraken
```

项目的框架结构可能如下所示:

*   /config:应用程序配置，包括特定于环境的配置
*   /控制器:路线和逻辑
*   /locales:特定于语言的内容包
*   /模型:模型
*   /public:公开可用的静态资产以及服务器模板
*   /tasks:由 [grunt-config-dir](https://github.com/logankoester/grunt-config-dir) 自动注册的 Grunt 任务
*   /tests:单元和功能测试用例

社交证明，截至本文撰写之时(2015 年 7 月)，北海巨妖在上个月获得了 3399 颗 GitHub 星和 22248 次 npm 下载。

网址:[http://krakenjs.com](http://krakenjs.com/)

GitHub:【https://github.com/krakenjs/kraken-js 

https://www.npmjs.org/package/kraken-js

# **风帆**

Sails.js 建立在 Express.js 之上；因此，对于已经熟悉 Express.js 的人来说，学习起来更容易。

Sails.js 有丰富的脚手架。想想 Ruby on Rails(因此得名“sails”)。这允许开发人员创建 RESTful API 端点**，而无需编写任何代码**。自动生成的代码可以在以后进行定制，以满足特定的业务需求。

Sails.js 是一个 MVC 框架，它附带了数据库 ORM/ODM [Waterline](https://github.com/balderdashy/waterline-docs) ，支持各种数据库。

Sails.js 还通过 Socket.io 和一个资产工具(Grunt)内置了对 WebSockets 的支持。然而，Sails.js 让你决定前端层，这通常是用 Angular.js、Backbone.js 或[任何其他前端框架](http://todomvc.com/)实现的。

**Sails.js 优点:**

提供良好的代码组织和蓝图

对 WebSockets 的内置支持

支持各种[数据库](http://sailsjs.org/#!/documentation/concepts/extending-sails/Adapters/adapterList.html)

数据有效性

控制器、模型和路线的自动生成代码

许多现成的安全功能，例如 CSRF 和与张亿嘟嘟的兼容性

内置文件上传库

良好的文档

带有挂钩和插件的灵活模块化架构

**Sails.js 缺点:**

陡峭的学习曲线

固执己见的

以下是在 Sails.js 项目的 config/routes.js 文件中定义路线的示例:

```
module.exports.routes = {
  'get /signup': { view: 'conversion/signup' },
  'post /signup': 'AuthController.processSignup',
  'get /login': { view: 'portal/login' },
  'post /login': 'AuthController.processLogin',
  '/logout': 'AuthController.logout',
  'get /me': 'UserController.profile'
}
```

如您所见，抽象— —意味着路由的逻辑在其他地方— —保持 routes.js 文件简洁明了。这在大型企业级应用程序中非常重要，因为它提供了控制和良好的代码组织。

要开始使用 Sails.js，请将其作为命令行工具安装到 npm 中，并运行生成器:

```
$ npm -g install sails
$ Sails.js new sails-test
$ cd sails-test
$ Sails.js lift
```

生成的框架项目将包含以下文件夹:

*   /api:所有服务器端逻辑，如控制器、模型、策略、响应(请求处理程序)和服务(可重用组件)
*   /assets:静态资产，如图片、前端 JavaScript、样式等。
*   /config:配置设置，如环境、区域设置、中间件
*   /tasks:强制生成任务
*   /views:服务器端模板

在撰写本文的最后一个月(2015 年 7 月)，Social proof，Sails.js 的 GitHub 星级为 10，963，npm 下载量为 63，859。

网址:[http://sailsjs.org](http://sailsjs.org/)

GitHub:【https://github.com/balderdashy/sails/ 

https://www.npmjs.org/package/sails

# **回送**

最后但同样重要的是，我们对比一个来自 Express.js 的维护者和贡献者的框架，( [StrongLoop](https://strongloop.com/) )。遇见回环！

该框架作为企业产品进行销售(和许多其他 StrongLoop 的解决方案一样)。Loopback 是一个开源项目，它非常适合 StrongLoop 提供的其他工具和服务(有些服务是付费的)。该工具链称为[强循环圆弧](https://strongloop.com/node-js/arc)。

简而言之，LoopBack 是一个构建在 Express.js 之上的灵活而全面的框架。 **LoopBack 被认为是 Express.js 的下一个发展**，它内置了 ORM/ODM，具有广泛的驱动程序(官方列表和第三方列表)、现有 SOA 的连接器、第三方服务的组件和移动 SDK。

有三种开发回送的方法:

1.基于网络的图形用户界面:易于使用，但与下一个选项相比功能有限

2.命令行:自动生成代码，创建 API 端点，设置访问控制级别，数据库关系等。使用命令

3.API，即手动编写代码

环回企业用户列表[正在确认中。](http://loopback.io/users)

与 Sails.js 类似，LoopBack 是*自带客户端* (BYOC)。这意味着，LoopBack 不提供前端层，但它可以很好地与最流行的框架如 Backbone.js 和 Angular 配合使用。

虽然有集成前端的服务器端 Node.js 框架，例如 Meteor、Derby.js，但是没有内置的前端层并不一定是一个缺点，因为大多数项目不希望被锁定，而是希望能够定制，或者可以选择重用现有的前端组件。

**环回优点:**

Android、iOS 和 Angular.js 的移动客户端 SDK

利用 StrongLoop 工具生态系统进行构建和部署、监控和分析(StrongLoop Arc、API 网关)

内置 API explorer 和 API 文档，带有 [Swagger](http://swagger.io/)

内置访问级别控制(ACL)

内置 ORM/ODM，带有带各种驱动程序的[杂耍器](https://www.npmjs.com/package/loopback-datasource-juggler)

出色的文档

提供代码组织和结构以及最佳实践

支持扩展和微服务

内置文件上传库

**环回缺点:**

由于其综合性，学习曲线很陡

固执己见，意味着你必须知道如何以回送的方式去做

要开始使用 LoopBack，我们需要安装 StrongLoop toolchain，然后运行生成器并回答问题:

```
$ npm install -g strongloop
$ slc loopback
```

典型的回送应用将利用以下文件夹结构:

*   /client:所有客户端文件和资产
*   /server:所有服务器端的 Node.js/Io.js 文件，包括引导脚本，以及中间件、模型、数据源等的 JSON 配置文件。

截至本文撰写之时(2015 年 7 月)，LoopBack 在上个月获得了 4117 个 GitHub 星级和 21960 次 npm 下载。

网址: [http://loopback.io](http://loopback.io/)

GitHub:[https://github.com/strongloop/loopback](https://github.com/strongloop/loopback)

国家预防机制:[https://www.npmjs.com/package/loopback](https://www.npmjs.com/package/loopback)

# **决定，决定，决定**

我们有意忽略了构建 Node.js/Io.js 应用程序的默认选择 Express.js，因为它缺乏代码生成器、组织和内置数据库支持。然而，你不应该低估 Express.js。对于快速原型或高度定制的项目，它可能是一个更好的选择。Express.js/Connect 中间件模块的数量是巨大的。这就是为什么你可能会选择北海巨妖、Sails.js 或 LoopBack。它们与 Express.js 中间件兼容。

北海巨妖非常轻便。 [PayPal 团队甚至没有将其定位为框架](https://blog.safaribooksonline.com/2014/02/21/use-kraken)，只是作为 Express.js 之上的一层，然而这一层非常有用，因为它会给你安全性、内部化、代码组织和代码生成器。

接下来的竞争者是 Sails.js 和 LoopBack。目前(2015 年 7 月)，它们绝对是 Node.js-framework 领域的重量级人物。他们带来了内置的 ORM/ODM 和丰富的脚手架，这将节省开发人员的时间。第三方选择的回送数据库驱动程序似乎提供了比 T2 的 Sails.js ORM/ODM 社区更多的库。

Loopback 可能具有更清晰的服务器-客户端分离(对于为单页应用程序构建 RESTful 服务器很有用)以及 API Explorer(文档),这是一个用于创建、管理和测试端点的很好的可视化工具。

Sails.js 和 LoopBack 的弊端显而易见。与任何全面的框架一样，特别是那些使用约定而不是配置的框架，它们对开发人员来说很神奇，需要一定的学习。你甚至可能需要一本书[*sails . js in Action*【2015，曼宁】](http://www.manning.com/mcneil)。

哈比神独树一帜，因为它的架构在设计上与 Express.js 不同。这允许对请求和响应生命周期进行更细粒度的控制。

我不打算支持列表中的任何特定框架(哈比神、北海巨妖、Sails、LoopBack)。在大多数情况下，它们明显优于编写和维护自己的库或使用基本的 Express.js *。我的建议是根据它们的优缺点，根据具体情况使用它们，因为它适合您的特定项目。*

> 关于精选的 Node.js/Io.js 框架和库的更新列表，请访问 NodeFramework.com。

附注:作为本书的作者，我可能偏向于基于 Express 的框架，但是(如果你想知道的话)**我个人最喜欢的企业项目 Node.js 框架是 Loopback** ！

*欲了解更多关于 Capital One 的 API、开源、社区活动和开发人员文化的信息，请访问我们的一站式开发人员门户网站 DevExchange。*[【https://developer.capitalone.com/】T21](https://developer.capitalone.com/)