# 深入探究授权和延迟捕获。

> 原文：<https://medium.com/square-corner-blog/a-deep-dive-into-authorization-and-delayed-capture-24ce2b9fd5e?source=collection_archive---------4----------------------->

## 在本帖中，我们将深入探讨持有卡中的金额并在以后捕获或取消它的可用选项。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

在实践中，对卡进行暂挂或“预授权”交易与用 API 直接向卡收费非常相似。

## 何时延迟捕获？

选择延迟捕获交易而不是直接收费通常由您的业务类型决定。你可以在某人借设备或租房时持有他的卡。其他常见的用例包括，在客户“试驾”产品之前，需要确认他们有足够的资金购买产品，或者在购买流程中实现小费或可选小费的空间。

收费时唯一的区别是请求中包含的`delay_capture`参数，如下所示:

```
POST /v2/locations/LOCATION_ID/transactions{
  "idempotency_key": "74ae1696-b1e3-4328-af6d-f1e04d947a13",
  "amount_money": {
    "amount": 5000,
    "currency": "USD"
  },
  "card_nonce": "card_nonce_from_square_123",
  **"delay_capture": false**
}
```

然后事务的状态将显示为`AUTHORIZED`，而不是通常的`CAPTURED`，如下所示:

```
{
   "transaction": {
     "id": "KnL67ZIwXCPtzOrqj0HrkxMF",
     "location_id": "18YC4JDH91E1H",
     "created_at": "2016-03-10T22:57:56Z",
     "tenders": [
       {
         "id": "MtZRYYdDrYNQbOvV7nbuBvMF",
         "location_id": "18YC4JDH91E1H",
         "transaction_id": "KnL67ZIwXCPtzOrqj0HrkxMF",
         "created_at": "2016-03-10T22:57:56Z",
         "amount_money": {
           "amount": 5000,
           "currency": "USD"
         },
         "type": "CARD",
         "card_details": {
 **"status": "AUTHORIZED",**           "card": {
             "card_brand": "VISA",
             "last_4": "1111"
           },
           "entry_method": "KEYED"
         }
       }
     ],
     "product": "EXTERNAL_API"
   }
 }
```

如果您的客户帐户中没有足够的资金或信用，此交易将失败。

您现在有一笔已授权的费用，您有六天时间来获取付款或将其作废。

## 获取付款

如果您决定为您的授权捕获付款，您需要使用 CaptureTransaction 端点:

```
POST /v2/locations/{location_id}/transactions/{transaction_id}/capture
```

根据上面的交易示例，我们将使用:

```
POST /v2/locations/18YC4JDH91E1H/transactions/KnL67ZIwXCPtzOrqj0HrkxMF/capture
```

这个端点不提供响应，所以空的 json 对象`{}`意味着捕获成功。您可以在以后通过[检索事务](https://docs.connect.squareup.com/api/connect/v2#endpoint-retrievetransaction)来获得事务细节。

## 使付款无效

授权交易将在 6 天后自动失效，但最佳做法是在您知道您不会获得资金时，立即使付款*失效，这样您的客户就可以重新获得他们的资金或信用。*

作废与捕获非常相似，只是端点的 URL 略有不同。

```
POST /v2/locations/{location_id}/transactions/{transaction_id}/**void**
```

根据上面的交易示例，我们将使用:

```
POST /v2/locations/18YC4JDH91E1H/transactions/KnL67ZIwXCPtzOrqj0HrkxMF/void
```

这个端点也不提供响应，所以一个空的 json 对象`{}`意味着授权交易成功作废。

现在，您可以使用 Square 的电子商务 API 对交易进行授权了。如果你有任何问题，请在评论中告诉我们。下次见！