# Square 电子商务 API 的 ActiveMerchantSquare

> 原文：<https://medium.com/square-corner-blog/activemerchantsquare-for-squares-e-commerce-api-111ea63e530a?source=collection_archive---------7----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们为那些想将 Square 的[电子商务 API](https://docs.connect.squareup.com/articles/paymentform-overview) 与 ActiveMerchant rubygem 一起使用的开发者发布了[activemerchantssquare](https://rubygems.org/gems/active_merchant_square)。如果你是一个 ruby 开发者，你可以使用流行的 ActiveMerchant gem 作为一个公共抽象层(从 2006 年开始在生产中使用),与许多支付网关协同工作。

ActiveMerchantSquare 依赖并增强了 ActiveMerchant gem，因此您可以将它与 ActiveMerchant 的现有用法结合使用。使用支付[表单](https://docs.connect.squareup.com/articles/adding-payment-form)中的 card_nonce 而不是原始卡号来调用标准的 ActiveMerchant API 方法。Square 的电子商务 API 将您从 PCI 范围中移除，因为您的服务器永远不会看到原始支付卡号。

其他特性(在我们的 V1 和 V2 API 中)比如新发布的[目录 API](/square-corner-blog/introducing-the-new-square-catalog-api-3e2ebf254967) 只在我们的 [connect-ruby-sdk](https://github.com/square/connect-ruby-sdk) 中可用。如果你愿意，你可以同时使用这两种宝石。正如我们所有的[客户端库](https://docs.connect.squareup.com/articles/client-libraries)一样，我们希望帮助您减少与 Square 集成所需的开发时间。

# 尝试一下

您可以通过将 ActiveMerchantSquare 添加到您的 Gemfile 来使用它:

```
gem ‘active_merchant_square’, ‘~> 1.0’
```

这个简单的例子演示了如何在获得一个[卡随机数](https://docs.connect.squareup.com/articles/processing-payment-rest#chargingcardnonce)后进行购买。

除了调用 purchase，还可以调用这些方法，和在 ActiveMerchant 中完全一样。(详情见 [rubydoc](http://www.rubydoc.info/gems/active_merchant_square/1.0.0/ActiveMerchant/Billing/SquareGateway) )。

```
 def purchase(money, card_nonce, options={})
      def authorize(money, card_nonce, options={})
      def capture(ignored_money, txn_id, ignored_options={})
      def refund(money, txn_id, options={})
      def void(txn_id, options={})
      def verify(card_nonce, options={})
      def store(card_nonce, options = {})
      def update(customer_id, card_id, options = {})
      def update_customer(customer_id, options = {})
      def unstore(card_id, options = {}, deprecated_options = {})
```

你可以在这里看看我们的工作示例应用[。请告诉我们在将您的应用程序与 Square 集成时，您使用 ActiveMerchantSquare 节省开发时间的其他方式。](https://github.com/square/active_merchant_square/tree/master/examples/sinatra)