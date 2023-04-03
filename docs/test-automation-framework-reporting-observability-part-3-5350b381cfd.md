# 测试自动化框架报告和可观察性(第 3/5 部分)

> 原文：<https://medium.com/globant/test-automation-framework-reporting-observability-part-3-5350b381cfd?source=collection_archive---------0----------------------->

![](img/e67e467931eaf0fb3180b3de69f51f8e.png)

报告是一个反应性的活动，信息必须提供完整的细节、可靠的错误证据以及每个测试活动解决方案的完全可追溯性。

警报也是一种反应性的活动，它可以通知涉众并让他们对任何测试情况或行为负责，比如测试结果、测试覆盖范围的恶化、测试时间的增加、基于历史信息的推荐优化任务等等。

为什么报告和警报对可观察性很重要？这两种方法都改进了向团队传达测试结果的方式，并通过快速诊断失败的根本原因，减少了分析结果和排除系统错误所需的时间。团队能够在测试执行完成后立即访问信息，除了点击消息和查看细节之外，不需要任何手动操作。

报告和警告是通知和共享测试执行过程和结果的基本方面，增加了测试自动化过程的可观察性。

![](img/72a74cdde4f4d8138c8af3f00225f63a.png)

Real-life example of integrating test reports and alerts into Slack with options to publish to Jenkins (or another CI tool), connect to a test management tool, or use an SMTP server to send via email

这两种功能都使用先前收集的测试覆盖信息，但是输出是不同的。报告提供了一个包含一般测试信息和测试细节的 HTML 测试报告。Alerting 通过专用的消息传递系统通知您相关的测试信息，以便立即了解和解决问题。

如上图所述，生成 HTML 测试报告并将其附加到测试结果通知中。然而，它也可以通过电子邮件共享，集成到测试管理工具中，或者在 CI 工具中发布。所有的解决方案都是有效的，相辅相成的。

# 测试报告真实示例

生成 HTML 测试报告以提供关于每个测试的全面概述和详细信息，例如测试步骤、API 调用错误、错误消息和完整堆栈跟踪、屏幕截图或支持分析的其他附加信息。

创建报告有两种方法:

*   使用特定的模型和 CSS 样式创建自定义报表。
*   使用任何提供可扩展和灵活 API 的开源工具来配置报告创建，如 [extentreports](https://www.extentreports.com/) 或 [allure-report](https://qameta.io/allure-report/) 。

**ExtentReports** 支持创建多种报告类型和一个自定义 API，允许您实现扩展。ExtentReports 是一个用于自动化测试的日志风格的报告库。记录器只是一个对象，用于记录特定系统或应用程序的消息或事件。ExtentReports 使用 logger 样式来添加关于测试会话的信息，例如创建测试、添加屏幕截图、分配标签，以及添加事件或步骤序列来顺序显示测试步骤的流程。

![](img/622560b8717b446caf175a214dd9dc6b.png)

下面你可以看到如何用 1 个运行日志创建一个测试:

![](img/8a46d68f35b307819992922278e030d8.png)

下面是一些 Web 和 API 测试报告的例子。

![](img/e4a6f141bc4888344f50809196562659.png)

UI Test Report

![](img/cab4e2ab28a92ef3e82544bcd1ad154b.png)

API Test Report

# 警示现实生活的例子

与消息工具集成无非是向使用所需平台的渠道或人群发送特定的通知或警报。那么有哪些消息工具呢？这取决于项目可用的工具以及支持它的测试基础设施。然而，消息工具至少可以分为两类:

*   电子邮件:一封电子邮件被发送到一个特定的分发列表，包含关于测试执行的信息，它的细节和作为附件的 HTML 报告。
*   通过聊天机器人发出警报:聊天机器人会发送一个带有特定结构和相关信息的自定义通知。报告也可以作为通知的一部分。

在这两个类别中，都有一个实体负责发送消息和消息本身。消息必须尽可能有用，以增加测试的可见性，并减少投入分析的时间。

*   负责发送消息的实体支持特定的通信协议和契约，以及关于如何操作、传输和共享数据的特定权限。
*   该消息必须包含尽可能多的细节和审计证据，以便为负责分析和评估结果并最终执行错误纠正的人员提供清晰准确的视图。

Slack 是市场上最常用的消息工具之一。这是一个完美的通信工具，也非常适合使用机器人发送警报和自定义通知。

**1。创建自定义消息**

根据所提供的可见性和详细信息，可以创建一个或多个通知。在这种情况下，将创建两条消息，一条作为打开对话的主消息，另一条在同一条消息中继续对话，但包含完整的详细信息。

主消息通过提供高级信息来开启对话，例如执行的代表性名称、环境、标记、版本和总体成功率，以及关于通过的测试数量、失败的测试和其他测试状态的细节。

作为主消息一部分的次级消息或线程中的消息通过提供附加细节、附加 HTML 测试报告以及按失败原因对测试结果进行分组来继续对话。

**2。发送通知**

首先，BOT 对于集中消息配置和权限(BOT 应该做什么和为谁做)以及避免使用个人帐户非常有用。使用机器人看起来也更好更优雅。

什么是机器人？bot 是一种 Slack 应用程序，旨在与打开对话的渠道和用户进行交互。机器人与普通应用程序一样:它可以访问相同的 API，并做 Slack 应用程序可以做的所有神奇的事情。但是，当你为你的 Slack 应用程序创建一个机器人时，你给这个应用程序一个脸、一个名字和一个个性，并鼓励用户与它交谈。

一旦 BOT 准备好了，就该通过 Slack API 发送消息了。

Slack 提供了一个健壮的 API，可以在通道上、特定的对话上，最重要的是在消息及其部分的配置上执行各种操作。

重要的是，API 调用的必填字段之一是可以通过 Slack bot 应用程序访问的令牌。每个应用程序都有令牌和权限。一旦应用程序配置完毕，通知准备就绪，就可以发布了，也可以通过已经打开的对话发布。

下面是一个使用 Slack bot 发送自定义通知作为测试结果的示例。

![](img/758955772a79d67eaef968c722981b39.png)

Main and secondary custom alerts for test reporting and automatic categorization and analysis of test failures

# 参考

**延伸报道**

*   [https://www . extent reports . com/docs/versions/5/Java/index . html](https://www.extentreports.com/docs/versions/5/java/index.html)

**松弛 API**

*   [https://api.slack.com/bot-users](https://api.slack.com/bot-users)
*   [https://api.slack.com/messaging/composing/layouts](https://api.slack.com/messaging/composing/layouts)
*   [https://api.slack.com/messaging/sending#publishing](https://api.slack.com/messaging/sending#publishing)
*   [https://api.slack.com/messaging/sending#threading](https://api.slack.com/messaging/sending#threading)