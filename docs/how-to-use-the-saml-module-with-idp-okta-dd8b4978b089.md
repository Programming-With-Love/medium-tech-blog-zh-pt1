# IDP Okta 如何使用 SAML 模块？

> 原文：<https://medium.com/mendix/how-to-use-the-saml-module-with-idp-okta-dd8b4978b089?source=collection_archive---------0----------------------->

![](img/ed45adc073fa4e4e3fea33bb75f37f61.png)

[身份提供者](https://en.wikipedia.org/wiki/Identity_provider)是创建、维护和管理身份信息的系统实体，通常用于用户认证。人们试图更频繁地使用 IDP，因为该技术对外部用户很有帮助，不依赖于 [LDAP 认证](https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol)。

您可以尝试许多开源的 IDP，比如 Shibboleth、Keycloak 等等。然而，我会决定使用 [Okta 开发环境](https://developer.okta.com/)，因为它很容易使用，我们不需要配置那么多的设置来使用它。

让我们在下面的概览图中看看 [SAML 协议](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language)。

![](img/71248e873f7158a15b4a81c8210566f1.png)

我从 [Mendix 8.15](https://docs.mendix.com/releasenotes/studio-pro/8.15#8150) 开始，使用一个空白的 web 应用程序模板。接下来，我安装两个模块: [MxModelReflection](https://marketplace.mendix.com/link/component/69) 和 [SAML2.0](https://marketplace.mendix.com/link/component/1174)

![](img/54307b24ab48bff29fb533c5cf04c7d2.png)

接下来，我将安全级别设置为生产模式。

![](img/da4966808ae15c20f983b39ad45fc93f.png)

我还授予管理员用户角色访问 SAML 模块的管理员角色的权限。

![](img/2412cb5c49c0359c11709e0a25676966.png)

现在，我用两个用户角色配置我的第一个模块。

![](img/13a145a91c7d1779731511ff8f445ef0.png)

并将 SAML 模块中的 app 常量 DefaultLoginPage 设置为 login.html。

![](img/b981390fd232ef766f715b2b643a2147.png)

在导航菜单中，我让管理员可以访问 MxModelReflection 和 SAML 模块概述页面。还授予管理员管理用户帐户的权限。

![](img/600d841f9ae3c2e13a8aabff033482a6.png)

现在，我必须在本地运行我的应用程序，并同步所有模块的反射数据。

![](img/dfeb5130a14c2dcf9f00fc4076981c69.png)

回到 SAML 页面，我如下所示配置它。

![](img/9997faef5fd9d7cf3640db7198047cd8.png)

完成 SAML 的设置后，我单击“下载 SP 元数据”来获取 XML 文件，其中也包含您的 SP ID。

![](img/3cb79e5245e473453ea76fe433a2acda.png)

以下 SSO 信息对于 IDP okta 非常重要。

![](img/6005a75f2142aba450db9735d138cbeb.png)

此时，我有了 SP ID 和 SSO 断言 URL。

配置我们去 OKTA 看看。确保 OKTA 开发者 UI 设置为经典 UI 模式。

![](img/e6fcce4588043e4d732c019b42c0fd8e.png)

现在，我能够在 OKTA 中用 SAML 协议创建一个新的 web 应用程序。

![](img/9f3d177783d09b613b3de9f0da51e86d.png)

在下面的这一部分中，应该需要您的 SP ID。

![](img/13adc14f4658790c8ae3c417b9254743.png)

下面的这一部分是断言数据，它将允许 IDP 在成功登录后在您的应用程序的$Account 或$User 实体内创建用户帐户。

![](img/0275c17177821be3da10501feca412d3.png)

设置完成后，我会被导航到此页面。在此页面中，我可以看到身份提供者的 SSO 元数据。

![](img/c824ae6a65d5d92f8069fd641a191783.png)

格式应该是这样的。

![](img/4adccb26494a75b0abd6e5981e3b0f06.png)

我们需要在 Mendix 应用程序中使用这个 URL 来设置 SAML 模块中的 IDP 配置。

现在让我们转到 Mendix 应用程序。

别名很重要，应该是唯一的。这个字段没有验证，只要记住不要重复就行。

![](img/3a2d7db884c01be887af46e0fb953099.png)

对于“请求授权上下文”选项卡，我将其配置如下。

![](img/28a1b35377ef081c3d8ca60a28c15c76.png)

在“Provisioning”选项卡上，设置应该如下所示。

![](img/28a1b35377ef081c3d8ca60a28c15c76.png)

对于映射选项卡，您应该创建一个能够匹配 OKTA IDP 属性的字段。

![](img/90458550c7e1b24c2c346547df01af67.png)![](img/1263b23d0fe5ece5db1664c9fa000ed4.png)

这都是为了 Mendix 中的 IDP 配置。

返回 OKTA 应用程序，为组或用户分配应用程序。

![](img/2f5923ab17da7b1339f1db0213ff0f2c.png)

现在让我们测试应用程序。

我的 Mendix 应用程序还没有用户登录

![](img/9469947ac06952b7b5f2d67a74430d45.png)

通过您在上一步中分配的用户登录 okta 仪表板。

![](img/8adf1cf69f9d79000d55c51ec134a873.png)

点击 PoC 应用程序。

![](img/aee88b1c0c6db3d14e50fc7845d7e832.png)

出现此错误是因为缺少 id。按下再试一次

![](img/6d6bc0c03c2c4ca34e47180133c2ef3a.png)

你被录取了

![](img/186d0ebc17808c58790d36e4dff3f0d4.png)

现在回到 okta web，我们需要解决一个 id 丢失的问题。

您需要在 okta 应用程序的默认 SSO 登录中添加别名，格式应该是这样的/SSO/login/{aliasname}

![](img/8473bf41987d95c9c6e2c641554c1172.png)

然后再试一次。

*来自发布者-*

*如果你喜欢这篇文章，你可以在我们的* [*媒体页面*](https://medium.com/mendix) *或我们自己的* [*社区博客网站*](https://developers.mendix.com/community-blog/) *找到更多类似的文章。*

*希望入门的创客，可以注册一个* [*免费账号*](https://developers.mendix.com/meetups/#meetupsNearYou) *，通过我们的* [*学苑*](https://academy.mendix.com/link/home) *即时获取学习。*

有兴趣更多地参与我们的社区吗？你可以加入我们的 [*slack 社区频道*](https://join.slack.com/t/mendixcommunity/shared_invite/zt-hwhwkcxu-~59ywyjqHlUHXmrw5heqpQ) *或者那些想更多参与的人，看看加入我们的* [*聚会*](https://developers.mendix.com/meetups/#meetupsNearYou) *。*