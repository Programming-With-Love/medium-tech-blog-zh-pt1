# Livin' La Vida 低码

> 原文：<https://medium.com/oracledevs/livin-la-vida-low-code-27b19588a814?source=collection_archive---------1----------------------->

![](img/10c13b6bc27d81170f31d05772e5f952.png)

Oracle Application Express 是一个领先的开发平台。

> Oracle Application Express (APEX)是一个低代码开发平台，使您能够构建可扩展的、安全的企业应用程序，这些应用程序具有世界一流的特性，可以部署在任何地方。

开始使用 Oracle Application Express 非常简单。

有多种方法可以创建驻留在内部和云中的 APEX 应用程序。

[](https://apex.oracle.com/en/learn/getting-started/) [## 入门指南

### Oracle APEX 应用程序开发服务提供了一个预配置、完全受管且安全的环境…

apex.oracle.com](https://apex.oracle.com/en/learn/getting-started/) 

这些方法包括:

*   在数据库应用程序开发虚拟设备中运行 APEX。即 Oracle 数据库、Oracle SQL Developer 和 Oracle Application Express。
*   在本地下载并安装 Oracle APEX。
*   在自治数据库上使用 Oracle APEX。
*   下载 APEX 并将其安装到其他 Oracle 云服务中。

但是，对于原型和 Oracle 云中的免费评估，有两种方法:

1.  自由 APEX 工作空间
2.  永远免费的 APEX 服务

让我们来看看如何使用这些方法进行设置。

## 自由 APEX 工作空间

对于没有 OCI 帐户的人来说，评估 Oracle APEX 最简单快捷的方法是申请一个托管在 apex.oracle.com 上的免费工作空间。

![](img/91e8e6db65dd1f5f39b95105b5a68571.png)

点击按钮申请一个免费的工作空间。

![](img/6e387c72ae7b68ace5f57e6a32644fbb.png)

您的电子邮件地址将成为您的用户名，您的密码将在稍后设置。

完成提示的其余部分，然后继续您需要检查您的电子邮件的过程。

![](img/cdb834d715dc826e8016e6724a7c4f74.png)

该电子邮件将有一个按钮，带您到浏览器创建您的新工作区。

![](img/1e27e5efd3e276f9667e84f2c2aeb29c.png)

下一个屏幕将允许您为您的帐户设置密码，然后您就可以登录了。

登录后，您将能够看到 APEX 仪表盘。

![](img/f8674c368515425085280bcb35238f3f.png)

## 永远免费的 APEX 服务

如果您已经有一个 OCI 帐户，那么设置 APEX 实例也同样简单。

> Oracle APEX 应用程序开发(APEX 服务)在 OCI 提供 APEX 应用程序的低代码开发和部署，包括自治数据库。

在 OCI 仪表板中，选择开发人员服务，然后选择 APEX 实例。

![](img/cbc886144d1b0496b9e71fdf8fd48fed.png)

在下一个屏幕中，您将能够创建 APEX 服务。

![](img/7f3c940d44ad19f861ee596910ca7f06.png)

在以下屏幕中，选择默认值，然后选择管理员密码。

在页面下方，您可以选择 APEX 应用程序的网络访问。

![](img/d04729eb9d22c6a46e7c444305fb450d.png)

现在您已经准备好创建服务了。

![](img/72d4a161bf3f79673b29a88759f9c563.png)

单击按钮，将显示 APEX 实例的仪表板。

![](img/bd56376f4ef5b7c555f3b60ca1c6bcc3.png)

当 APEX 实例准备就绪时，您将能够启动它。

![](img/29db6ec1018afaac7e4ec35ea797d57a.png)

首次创建 APEX 服务时，您可能会收到以下电子邮件。

> 祝贺您，您新的始终免费 APEX 服务已成功创建，现已推出。这将消耗 Oracle 自治数据库类别中 2 个允许的始终空闲实例中的 1 个。

登录后，您可以使用之前设置的管理员密码。

![](img/61fdd802f29c2d512c739b784e84bf07.png)

在下一个屏幕中，您将能够为非管理员用户创建一个工作区。

![](img/cd4db8d545364357909f0141dc7be64c.png)

指定非管理员用户以及符合以下规则的用户密码:

> 密码长度必须在 12 到 30 个字符之间，并且必须包括至少一个大写字母、一个小写字母和一个数字字符。
> 
> 密码不能包含用户名。
> 
> 密码不能是同一用户名使用的最后四个密码之一。
> 
> 密码不能包含双引号(")字符。
> 
> 该密码不得与 24 小时前设置的密码相同。

![](img/6d3d538edba06fb858d8a4e086bf2c56.png)

您可以指定新用户和密码。并且工作区的名称将默认为用户的名称。

然后，您将能够创建一个工作区。

因此，接下来您应该会看到 APEX 仪表板。

![](img/50cc5d0a71df4afadf5dabbae7c01611.png)

请注意顶部的消息:

> 工作区已创建。注销 Administration Services，然后登录测试以开始构建应用程序。

让我们开始吧。

![](img/0186c2f22a3101771eae58b60bbf069c.png)

现在以非管理员身份登录。我们将指定名为 Test 的用户。

![](img/f10773d53516c070d95152c155c0cca7.png)

现在我们回到 APEX 仪表板，但这次是作为测试用户。

![](img/dbd792b21be59906b209ba8dfca46493.png)

现在，我们可以从这里创建一个应用程序。

Paul Guerin 是一名专注于 Oracle 数据库的国际顾问。此外，他还出席了一些世界领先的甲骨文会议，包括甲骨文 2013 年世界开放大会。自 2015 年以来，他的工作一直是 IOUG 最佳实践技巧小册子以及 AUSOUG、Oracle Technology Network、Quest 和 Oracle Developers (Medium)出版物的主题。2019 年，他被授予 My Oracle 支持社区最有价值贡献者。他是一名 DBA OCP，并将继续参与 Oracle ACE 计划。