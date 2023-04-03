# 如何在 Postgres 中用 Golang 以 4 种常见方式分页

> 原文：<https://medium.easyread.co/how-to-do-pagination-in-postgres-with-golang-in-4-common-ways-12365b9fb528?source=collection_archive---------0----------------------->

## Postgres 上分页的几个例子，带 Benchmark，用 Golang 写的

![](img/60c96cb75b13616e54c0caccfb0379a1.png)

Photo by [Ergita Sela](https://unsplash.com/@gitsela?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

*大家好*，我已经很久没有发表文章了。有很多事情正在发生，像疫情和更多的东西。这个疫情在精神上影响了我，这种自我隔离让我疲惫不堪，压力重重。我希望这部《新冠肺炎·疫情》能在今年圣诞节前结束。😭

在这个难得的场合，在战胜了无聊和懒惰之后，我找到了完成这篇文章的精神。从我开始，当我在当前的工作中构建我们的新应用程序时，我对一些事情很好奇，在这一部分，它是关于分页的。比如如何根据我的理解更好的处理分页，LOL。* *我提出的想法可能不是最好的，所以如果你们有更好的方法，比我丰富的经验，把你们的评论放在阿雅下面！！*

TBH，在我以前的工作中，我从来没有关心过更多的细节，因为我们都有相同的观点，我们只喜欢在我以前的公司有 10 名工程师，所以我们可以有相同的观点。但现在我关心这个问题，因为我现在的工作中有很多工程师，每个人都有不同的观点。

所以，我很好奇，在应用程序之上的 Postgres 上构建分页的更好方法是什么，就我的情况而言，我在应用程序中使用 Golang。

实际上，有两种著名的分页方式:

*   基于光标的分页
*   基于偏移量的分页

在本文中，我将只介绍后端工程师通常使用的四种不同的分页方式，或者至少是我目前所知道的，因为我知道如何编码。

*   用页码做分页，挺常见的；用户只发页码，我们内部处理；我在数据库级别使用偏移。
*   使用偏移和限制进行分页，这在 **RDBMS** 特性中很常见。用户将从查询参数中直接发送偏移量。
*   使用一个简单的查询进行分页，将自动增量 ID 作为 PK，这在数据库的自动增量 ID 中很常见。即 ID 被视为光标。
*   使用 UUID 作为主键，结合创建的时间戳进行分页，也称为 seek-pagination 方法或 keyset 分页方法。并且该组合键将被散列成光标串。

所以我在这里要做的是，我将创建这四个分页实现，并从代码中做一个小的基准测试；我用 Golang 基准。这篇文章的目标只是为了满足我的好奇心 LOL。我知道我可以看别人的文章，但我想用我的版本来做。

TL；速度三角形定位法(dead reckoning)

*   这里使用的所有代码都已经被推送到我的 Github 库，[github.com/bxcodec/go-postgres-pagination-example](https://github.com/bxcodec/go-postgres-pagination-example)
*   结论可以在这篇文章的底部看到

# REST API 上的分页

为了给你一些上下文，如果你不知道分页是用于什么的话，请使用*。分页是用来给你的回复分页的，LOL。我不知道如何更好地表达。*

我将创建一个例子；假设我在 REST API 中有这个端点。

```
**GET /payments**
```

这个端点将从 API 获取所有的支付。我们知道，在拥有大量数据集的大规模应用中，这些支付可能有数千或数百万个数据行。作为用户，我想获取我的付款清单。

从数据库的角度来看，查询所有记录将花费大量时间。我可以想象，如果我们有一百万条记录并获取所有数据，这将需要多长时间。因此，在这种情况下，人们引入了他们所谓的分页。它就像书本上的书页，每一页都包含一串单词。

但是对于这个端点，每个页面将包含一个支付细节列表，所以我们仍然可以更快地获取支付，但是可能会被截断成多个页面，直到我们可以获取所有的支付记录。

```
GET /payments?**page=1** // to fetch payments in page 1
GET /payments?**page=2** // to fetch payments in page 2
GET /payments?**page=3** // to fetch payments in page 3
... etc
```

您可能在任何端点中都见过这种风格，或者类似这样的风格。

```
GET /payments?**limit=10** // initial request for fetch payment
GET /payments?**limit=10&cursor=randomCursorString** // with cursor
GET /payments?**limit=10&cursor=newrandomCursorString** // for next page
GET /payments?**limit=10&cursor=anotherNewrandomCursorStrin**g
... etc
```

还有更多，这就是我们所说的分页。我们将数据列表截断成几个片段并发送给客户端，因此我们仍然保持应用程序的性能，客户端在获取数据时不会丢失跟踪。

## 1.带页码的分页

```
GET /payments?**page=1** // to fetch payments in page 1
GET /payments?**page=2** // to fetch payments in page 2
GET /payments?**page=3** // to fetch payments in page 3
... etc
```

你见过上面这样的分页吗？TBH，我从未见过这样的分页，如果我没记错的话，在公共 API 中没有。但是，大约 4 年前，在我毕业后的第一次工作测试中，我曾经创建过这种风格的分页。

因此，后端的逻辑相当复杂，但从用户体验来看，它会简化，

*   首先，我将设置默认限制，比如 10。*每页是 10 项* s
*   并且每个页码将被乘以默认限制
*   然后我会用它作为数据库的偏移量。
*   并且，用户可以根据所请求的页码获取项目。

然后，我尝试为这种方法构建一个简单的应用程序。用 **10 万行数据**，我试着对它进行基准测试。

**基准测试结果**

Benchmark Result using Go for PageNumber pagination

**这种分页方法的缺点是**

*   就性能而言，不建议这样做。数据集越大，资源消耗越大。

但是使用这种方法的好处是，用户感觉就像打开一本书，他们只需要传递页码。

## 2.带偏移量和限制的分页

带有偏移量和限制的分页对于工程师来说很常见。这是因为 RDBMS 支持偏移和查询限制的特性。

从应用程序级别来看，没有额外的逻辑，只是将偏移量和限制传递给数据库，并让数据库进行分页。

通常是什么样子，

```
GET /payments?**limit=10** // initial 
GET /payments?**limit=10&offset=10** //fetch the next 10 items
GET /payments?**limit=10&offset=20** //fetch the next 10 items again
... etc
```

在客户端，他们只需要添加偏移量参数，API 将根据给定的偏移量返回项目。

从数据库层面来看，RDBMS，看起来像下面这样，

```
**SELECT**
    *
**FROM**
    payments
**ORDER** **BY** created_time
**LIMIT** 10
**OFFSET** 20;
```

**基准测试结果**

Benchmark Result using Go for LimitOffset pagination

**这种分页方式的缺点**

*   就性能而言，不建议这样做。数据集越大，资源消耗越大。

**这种分页方法的好处**

*   非常容易实现；不需要在服务器上做复杂的逻辑工作

## 3.使用 ID 的自动增量主键分页

这种分页方法也很常见。我们将表设置为自动递增，将其用作页面标识符/光标。

如何在休息时使用

```
GET /payments?**limit=10**
GET /payments?**limit=10&cursor=last_id_from_previous_fetch**
GET /payments?**limit=10&cursor=last_id_from_previous_fetch**
... etc
```

它在数据库查询级别看起来如何

```
**SELECT**
    *
**FROM**
    payments
**WHERE**
  Id > 10
**LIMIT** 20 
```

或者下降

```
**SELECT**
    *
**FROM**
    payments
**WHERE**
  Id < 100
**ORDER** **BY** Id **DESC**
**LIMIT** 20
```

**基准测试结果**

Benchmark Result using Go for AutoIncrement pagination

**这种分页方法的缺点**

*   这种分页方法的唯一缺点是，当使用自动递增的 id 时，在微服务和分布式系统中会有问题。
    带有`**20**`的相似 id 可以存在于服务支付和服务用户中。它在相同的应用程序上下文中是唯一的。如果每个 ID 都使用 UUID，那就不一样了，它是“*实际上唯一的”*(这意味着重复生成 UUID 的可能性非常小)。所以有些人试图用 UUID 来代替 PK。点击阅读关于 UUID 和自动增量键[的更多详情](https://medium.com/@Mareks_082/auto-increment-keys-vs-uuid-a74d81f7476a)

**这种分页方法的好处**

*   易于实现，不需要在服务器中做复杂的逻辑事情。
*   据我所知，最好的分页方式是使用自动增量 ID，这样性能更好。

## 4.结合创建时间戳的 UUID 分页

我不确定这是否很常见，但是我看到一些文章做了这种分页。上下文是该表没有使用自动增量 id，而是使用 UUID。但是，人们想知道如何进行分页，添加一个自动递增编号的新列是一种资源浪费。所以对我自己来说，我所做的是，使用我的行的创建的时间戳，并将其与 PK 相结合，这就是 UUID。

这是数据库模式

payment.sql schema with UUID and timestamp

为了更快的查询，我用多个表做了一个索引，这是 PK 和创建的时间戳；从上面的模式可以看出，我做了一个名为`idx_payment_pagination`的索引。

所以逻辑是，

*   我将使用 UUID，它是我的主键，并将它与`create` 时间戳结合起来
*   把这两个组合成一个字符串，然后我把它编码成一个 base64 字符串
*   并将编码后字符串作为下一页的光标返回，这样用户就可以使用它来获取他们请求的下一页。

我如何在应用程序级别上制作光标的示例

这是它在 REST 端点中的样子。

```
GET /payments?**limit=10**
GET /payments?**limit=10&cursor=base64_string_from_previous_result**
GET /payments?**limit=10&cursor=base64_string_from_previous_result**
... etc
```

但是在数据库中，查询看起来像这样，

```
**SELECT** *
**FROM** payments
**WHERE** created_time <= '2020-05-16 03:15:06'  // created timestamp
**AND** id < '2a1aa856-ad26-4760-9bd9-b2fe1c1ca5aa'  // this is UUID
**ORDER BY** created_time **DESC**, id **DESC**
**LIMIT** 2
```

**基准测试结果**

Benchmark Result using Go for Keyset pagination

**这种分页方法的缺点**

*   性能可能不会像使用自动增量 id 那样最佳。但是即使我们有数百万的数据，这也是一致的
*   非常复杂和高级，我们需要理解索引，因为如果我们不添加索引，这个查询在一个大数据集上会非常耗时。此外，我们在处理时间戳时需要小心。即使这样，我在查询时间戳时仍然会遇到一些问题。

**这种分页方法的好处**

*   ID 是 UUID，因此它在组织的微服务中几乎是全球唯一的。
*   从开始到查询数据的最后一页，性能是一致的

# 结论

好吧，在做了所有的基准测试后，我得出了一些结论。

## 1.性能:从快到慢

从基准测试结果来看(使用 Golang 基准测试工具)，速度更快的是使用 autoincrement PK。见下图；以纳秒为单位的每个操作所需的平均时间的图表越小越快。这个图表可能不是一个很好的代表，如果我把它放在第 95、97 等百分位，应该会更好，但我是从基准测试结果中得到这个值的。所以我认为这已经足够好了。

带有自动增量 ID 的分页速度更快，其次是 UUID/创建时间、页码和 LimitOffset。这只是 10 万行数据。随着数据的增长，它也会变得更大。因此，在只有 100K 数据的情况下，即使仍不到 1 秒，与极限偏移相比，使用自动增量时的差异已经很大。

## 2.发展:从快到慢

实施难度由易到难

*   **使用 Offset** ，因为我们只是将 Offset 和 limit 直接传递给数据库。
*   **使用页码**，这是自以为是；有些人可能有不同的逻辑，但对于我来说，我把这个放在前两个。
*   **使用自动增量 ID**
*   **使用带有创建时间的 UUID**

## 代码工件

对于代码，我已经把它推到了我的 GitHub 库，可以在这里找到，[https://github.com/bxcodec/go-postgres-pagination-example](https://github.com/bxcodec/go-postgres-pagination-example)

## 我在做这件事时面临的问题是

在做所有这些事情的时候，显然我会面临一些问题，但是我已经解决了它们，我也了解了它们。我写了我在这里学到的东西，也写在了本文中: [TIL:小心 Postgres 查询，for Less Or Equal on Timestamp](https://medium.com/easyread/til-becareful-on-postgres-query-for-less-than-or-equal-on-timestamp-9e486b657fc)

## 作者建议

作为一名软件工程师，同时也是这篇文章的作者，我建议在做分页的时候使用 autoincrement ID，但是如果你的系统或者你不想使用 autoincrement ID 作为 PK，你可以考虑使用 keyset 分页；在我的例子中，使用 UUID +创建时间时间戳。

# 参考

1.  大量的 Stackoverflow 答案，我忘了是哪一个，但是所有的答案都与 Postgres 的分页相关。
2.  【jOOQ 使用 Seek 方法实现更快的 SQL 分页
3.  [REST API 设计:过滤、排序、分页](https://www.moesif.com/blog/technical/api-design/REST-API-Design-Filtering-Sorting-and-Pagination/)