# 将自定义日历与 Salesforce 中的 Google 日历同步

> 原文：<https://medium.com/globant/sync-custom-calendar-with-google-calendar-in-salesforce-39531b72396d?source=collection_archive---------1----------------------->

![](img/97dbc06e9142874a6d349cacea2227db.png)

Sync calendar with google calendar

我叫泰比尔·辛格。我是有 6 年经验的高级销售人员。我在销售云、社区云以及 salesforce 与外部系统的集成方面拥有专业知识。

本博客将解释如何使用 fullcalendar 库创建自定义日历，以及如何在 salesforce 中将它与 Google calendar 同步。

**按照以下步骤在 salesforce 中实施自定义日历:**

1.在静态资源中存储 fullcalendar.js、fullcalendar.css、moment.js、jQuery。这些文件可以在[这里](https://github.com/tejbir92/fullCalendar)找到。

2.将 fullcalendar.js、fullcalendar.css、moment.js 和 jQuery 包含到 lightning 组件中。

![](img/3abc0953a34b2747ae57830d0769f039.png)

3.在 lightning 组件中包含 below div。

![](img/1e68c5e148bdbdef7a9316a84af11d78.png)

4.脚本加载后初始化完整日历。

![](img/3a4de9db467278b5b87f702b1b1d978e.png)

实施上述步骤后，日历将如下所示:

![](img/56cbc05e967a0b7b9377378cac236a9a.png)

**现在，第二个重要步骤是获取 google 日历事件，并在日历中显示它们。**按照以下步骤成功连接 Salesforce 和 Google 日历:

1.在 Google API 控制台中创建新项目。

![](img/a2aab52c13a278f139b5195f8f043d7c.png)

2.使用 OAuth 同意屏幕完成应用程序的注册。

![](img/c71fb8bbc590c1f41c4fc8d2263af0a9.png)

3.使用 OAuth 客户端 ID 创建凭据。

![](img/5cd6a74e6743ce76e158817213d17e84.png)

4.添加应用程序类型并重定向 URI。授权码将被附加在重定向 URI 的末尾。为重定向 URI 创建一个闪电应用程序。

![](img/8e36b0de581b34f949b909a0e3b6901a.png)![](img/bc66c1668d03c3592630634d85db4b69.png)

5.创建 OAuth 客户端后，将客户端 Id 和客户端机密存储在自定义元数据类型中。

![](img/e674d8cd80bfca5b85044c1d5272e07b.png)

6.现在，对 google authentication URL 进行 HTTP 标注，以授予对 Google Calendar 的访问权限。

![](img/a578202288c2ab21907b1e347cb35dbb.png)![](img/2b4ebcfae47fe1bb2aa02fb54afe987f.png)

7.一旦许可被授予，它将被重定向到“重定向 URI”与授权代码附加在它的结尾。

8.使用 auth code、client_id 和 client_secret 进行 HTTP 标注，以获取访问令牌，存储访问令牌并在自定义设置中刷新令牌。

![](img/efdd912b46720cbb7a84ee1b7f1e8dff.png)

9.现在使用访问令牌获取日历事件，并在 salesforce 的自定义日历中显示这些事件。

![](img/87521dc387c66e1e7f471b53bcfb52ab.png)

作为响应，google calendar API 将发送所有日历事件。解析响应并在 salesforce 的日历中显示它们。类似地，可以通过 salesforce 在 google calendar 中创建活动。

实施上述步骤后，google 日历事件将显示在 salesforce 的日历中。

![](img/bded852e7b5df06ac880d47c1c4388c6.png)