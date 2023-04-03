# Spring Boot 致动器错误配置的危险

> 原文：<https://medium.com/walmartglobaltech/perils-of-spring-boot-actuators-misconfiguration-185c43a0f785?source=collection_archive---------0----------------------->

![](img/417591d04504f4ec70d8475f41d70433.png)

Image Credit: [Pixabay.com](https://pixabay.com/photos/oil-rig-drilling-bop-actuator-1183699/)

Spring framework 提供了名为“ [actuators](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-enabling) 的奇妙特性来帮助工程师监控和管理 web 应用程序。

执行器旨在用于审计、健康和指标收集。这些都是优秀的特性，所以您可能想知道这里有什么危险？稍等一下！虽然这些端点在非生产环境中非常有用，但是如果它们在生产环境中启用并且配置错误，它们也可能会打开服务器的后门。例如，您的应用程序可能会通过堆转储意外泄露活动内存中的凭据，允许恶意参与者**修改**应用程序环境属性，或者在最糟糕的情况下，恶意参与者可能会彻底搞垮您的应用程序。

对于开发人员来说，了解使用执行器端点的安全含义是非常重要的，尤其是当一个应用程序可以通过互联网访问时。

除了*/健康*和*/信息*之外，所有执行器端点对最终用户开放都是有风险的，因为它们会暴露应用程序转储、日志、配置数据和控制。致动器端点具有安全隐患，并且**永远不应**暴露在生产环境中。

以下是常见弹簧靴致动器端点可能公开的数据和功能列表:

*   */heapdump* :显示堆转储，其中可能包含敏感数据，如您的数据库凭据
*   */threadump* :显示线程转储，包括堆栈跟踪
*   */trace* :显示可能包含会话标识符的最后几条 HTTP 消息
*   */logfile* :输出应用程序日志的内容，可能包含调试或其他非公开的细节。
*   */关机*:关闭应用程序
*   */映射*:显示所有的 MVC 控制器映射
*   */env* :提供对配置环境的访问
*   */重启*:重启应用程序

还有很多。请参考 Spring 文档中的[完整端点列表。](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-enabling)

从安全的角度来看，在生产环境中，建议禁用所有的 spring actuator 端点，仅暴露无害的端点，如 */health* ，仅当出于监控应用程序健康等目的需要时。

> 注意，在 Spring 1.x 中，所有的执行器端点都是可以访问的，不需要任何身份验证。

在 Spring 2.x 中，默认情况下，除了*/健康*和*/信息*之外的所有端点都被认为是敏感的和安全的。应用程序开发人员不应禁用安全配置并在生产环境中暴露其他端点。

要禁用所有端点，在`application.properties`中，设置

```
management.endpoints.enabled-by-default = false
```

禁用后，通过设置相应的配置仅启用所需的端点，例如，要启用健康端点，请设置

```
management.endpoint.health.enabled = true
```

如果确实需要启用指标收集，并且您需要为此启用 spring boot actuators 端点，那么就要像保护任何其他敏感端点一样保护 actuator 端点。如果你使用弹簧安全，确保执行器端点也是安全的。如果您希望配置自定义规则，比如只允许具有特定角色的用户访问它们，spring boot 提供了一些方便的`RequestMatcher`对象，可以与 spring security 结合使用。

*附:只有在生产环境中确实需要这些端点时，才应保护致动器端点。如果不需要执行器端点，建议将其全部禁用。*

## 摘要

Spring boot actuator 端点为监控应用程序提供了非常酷的功能，但也带来了安全问题。因此，默认情况下，不应在生产中启用这些端点。但是，如果您真的需要启用执行器，那么保护这些端点，就像对待任何其他具有敏感数据的端点一样。访问权限应该是最低要求。

感谢您的阅读。