# 将安全性构建到可购买的 pin 中

> 原文：<https://medium.com/pinterest-engineering/building-security-into-buyable-pins-11b2af80d03b?source=collection_archive---------2----------------------->

Wendy Lu | Pinterest 工程师

本周早些时候，我们宣布了[可购买 pin 码](https://blog.pinterest.com/en/buyable-pins)，这是一种在 Pinterest 上购买你喜欢的产品的简单而安全的方式。在构建可购买的 pin 码时，我们专注于让这项技术在移动设备上使用起来更简单、更有趣，但更重要的是将安全性融入其中。

## 它是如何工作的？

我们将与我们的主要合作伙伴 [Stripe](https://stripe.com/pinterest) 以及 [Braintree](https://www.braintreepayments.com/blog/pinterest-buyable-pins) 合作，这两家行业领先的支付提供商多年来一直在保护人们的信息。我们将与 Stripe 和 Braintree 合作来保管信用卡。

一旦 Pinner 在 Pinterest 应用程序中输入他们的信用卡信息，我们就会通过加密通道将其发送给提供商，提供商会将其存储在一个安全的保险库中。然后，商家通过他们现有的支付处理器从保险库中收取信用卡费用，并让 Pinterest 知道订单成功。

安全和信任对我们来说非常重要。我们选择与这些提供商合作，是因为他们的经验以及他们与支付处理商之间的信任关系，这些支付处理商与世界上最好的商家合作。

这只是开始。有了这项技术和设计，我们将能够与各种规模的商家合作，帮助各地的品酒人发现并购买他们喜欢的产品。

请继续关注 buyable Pins 工程团队的更多帖子。如果您有兴趣在我们推出可购买引脚时应对新的工程挑战，[加入我们的团队](https://about.pinterest.com/en/careers/software-engineering)！

*Wendy Lu 是 iOS 团队的一名工程师。*

*获取 Pinterest 工程新闻和更新，关注我们的工程*[*Pinterest*](https://www.pinterest.com/malorie/pinterest-engineering-news/)*，* [*脸书*](https://www.facebook.com/pinterestengineering) *和* [*推特*](https://twitter.com/PinterestEng) *。有兴趣加入团队吗？查看我们的* [*招聘网站*](https://about.pinterest.com/en/careers/engineering-product) *。*