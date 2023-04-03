# 如何通过 Square 的电子商务 API 为您的网站添加 Masterpass 支持

> 原文：<https://medium.com/square-corner-blog/how-to-add-masterpass-support-to-your-site-through-squares-ecommerce-api-284ffd28b44e?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

作为我们持续工作的一部分，使我们的开发者更容易支持不断增长的在线支付方式，我们很高兴地宣布，now Square 的电子商务 API 将为我们的美国卖家支持 [Masterpass](https://masterpass.com/) 。通过这样做，我们保证我们的卖家能够以买家感兴趣的每种支付方式完成销售。

Masterpass 是万事达卡的数字钱包，让买家可以使用所有主要的卡品牌在您的网站上轻松、快速、安全地结账。

要启用 Masterpass，只需遵循以下三个步骤:

步骤 1 -将您的位置 ID 添加到 SqPaymentForm。(注:您可以通过登录您的[开发者门户](http://connect.squareup.com/apps)找到您的位置 ID)；

第 2 步-与我们支持的其他钱包类似，在回调部分添加以下内容:

步骤 3 -添加 CreatePaymentRequest:

通过为我们的开发者和他们服务的卖家增加 Masterpass 支持，买家可以使用他们喜欢的支付方式进行支付。要了解 Square 电子商务 API 的更多功能，请参阅我们关于嵌入支付表单的文档。

**附加阅读**

*   要阅读更多关于我们的电子商务 API，请参见[嵌入支付表单](https://docs.connect.squareup.com/payments/sqpaymentform/sqpaymentform-overview)
*   [发行说明](https://docs.connect.squareup.com/articles/square-api-release-notes-2017-10-30)
*   应用实例: [C#](https://github.com/square/connect-api-examples/tree/master/connect-examples/v2/csharp_payment) ， [Java](https://github.com/square/connect-api-examples/tree/master/connect-examples/v2/java_payment) ， [PHP](https://github.com/square/connect-api-examples/tree/master/connect-examples/v2/php_payment) ， [Python](https://github.com/square/connect-api-examples/tree/master/connect-examples/v2/python_payment) ， [Ruby](https://github.com/square/connect-api-examples/tree/master/connect-examples/v2/rails_payment)