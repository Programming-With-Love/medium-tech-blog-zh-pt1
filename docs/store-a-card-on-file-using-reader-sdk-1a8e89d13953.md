# 使用 Reader SDK 将卡存储在文件中

> 原文：<https://medium.com/square-corner-blog/store-a-card-on-file-using-reader-sdk-1a8e89d13953?source=collection_archive---------2----------------------->

## Square Reader SDK 中的新功能

![](img/50335bee718b20c5cb2cd847c4e33a6a.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

去年，我们宣布为 iOS 和 Android 发布了 [Square Reader SDK](/p/introducing-square-reader-sdk-939a9ec2d197) ，以及用于 [React Native](/p/square-reader-sdk-for-react-native-a1b6fc19c5c2) 和 [Flutter](/p/square-flutter-beautiful-fast-mobile-payment-apps-539995d2e23a) 的插件。虽然 Reader SDK 使使用 Square 硬件在您自己的应用程序中进行当面支付变得很容易，但您可能还希望安全地存储客户卡信息以供将来使用(例如，创建重复计费计划)。

为了帮助您为回头客创造无缝体验并实现定期支付，Reader SDK 现在支持使用 Square 硬件将卡存储在文件中。这个新功能与[客户 API](https://docs.connect.squareup.com/more-apis/customers/overview) 和[交易 API](https://docs.connect.squareup.com/payments/transactions/overview) 协同工作，允许您创建客户档案；刷卡、轻触或蘸取卡片以安全存放。然后在任何时候为将来的购买从卡中充值。

它是这样工作的:

# 创建客户

使用[客户 API](https://docs.connect.squareup.com/more-apis/customers/overview) 创建客户档案，包括姓名、地址、电子邮件和电话号码等联系信息。

# 将卡片存档

使用 Reader SDK 来刷卡、点击或蘸取客户的卡，并将其作为卡存储在文件中。Reader SDK 安全地处理所有支付信息，并为您创建客户卡对象，因此您不必担心处理原始卡细节或处理 PCI 合规性。

## 机器人

创建 CustomerCardManager:

编写一个回调来处理结果:

添加代码以启动商店客户卡流程:

## ios

实现 StoreCustomerCardControllerDelegate 方法:

添加代码以启动商店客户卡流程:

# 在文件上记下信用卡的费用

一旦你为一个给定的客户存储了一张卡，你就可以使用[交易 API](https://docs.connect.squareup.com/payments/transactions/overview) 为将来的支付向卡收费。

# 无论买家身在何处，都要与他们见面

借助 Square 的全渠道开发平台和 Reader SDK 中的 Card on File，您现在可以将卡片添加到客户档案中，无论客户关系始于何处:[在线](https://squareup.com/us/en/developers/online-payment-apis)、[店内](https://squareup.com/us/en/developers/reader-sdk)或[应用内](https://squareup.com/us/en/developers/in-app-payments)。我们很高兴看到你将建立什么！

你可以在我们的[文档](https://docs.connect.squareup.com/payments/readersdk/what-it-does)中读到更多关于 Reader SDK 的内容。请务必阅读并遵循文档中概述的获得客户同意和披露条款和条件的要求。

如果您想了解我们的最新内容，请务必关注这个[博客](https://medium.com/square-corner-blog)，关注我们的 [Twitter](https://twitter.com/SquareDev) 账户，并注册我们的[开发者简讯](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog)！我们还有一个 Slack 社区，用于与其他实现 Square APIs 的开发者联系和交流。