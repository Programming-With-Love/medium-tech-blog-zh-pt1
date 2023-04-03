# 和你的单轨铁路分手

> 原文：<https://medium.com/square-corner-blog/breaking-up-with-your-monorail-780927542097?source=collection_archive---------1----------------------->

## 计划从一个完整的 Rails 应用程序中提取。

*由* [撰写*扎克瑞*](https://medium.com/u/a29cf9bf448c?source=post_page-----780927542097--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

在 Square，我在我们的支付可扩展性团队工作。2015 年，我们的任务是通过从 Square/Web 中提取支付基础设施来实现支付基础设施的现代化，Square/Web 是我们由单个 MySQL 实例支持的单片 Rails 应用程序。自 2009 年公司成立以来，Square/Web 一直是处理交易的核心组件。

认真的规划始于 2014 年底，当时我们已经开始触及运营管理的极限。

我们面临三种选择:

# 纵向扩展:购买更多磁盘

这需要花钱购买昂贵的企业闪存卡。纵向扩展是一种短期解决方案，随着时间的推移，跟上增长的成本会越来越高。从操作上来说，重建和备份大型数据库要困难得多。我们放弃了这个长期的选择。

# 横向扩展:Shard Square/Web 的 MySQL 数据库

除了管理单个 MySQL 实例，我们还可以对其进行分片以帮助水平扩展。我们评估了一些 gem，如 [Octopus](https://github.com/tchandy/octopus) 和 [db_charmer](https://github.com/kovyrin/db-charmer) ，并构建了一个概念验证分片系统。我们决定改装分片将是更多的工作，并没有让我们远离简化整体；事实上，这让事情变得更加复杂。

# 打破僵局:从 Square/Web 上榨取全部报酬

这最终成为我们的首选方法。我们决定将 Square/Web 中的支付功能和数据提取到一个单独的服务中。这也符合 Square 避免使用独石的整体建筑哲学。

# 搭建舞台

虽然 Square/Web 已经多年没有处理收费支付，但 Register 应用程序的其他部分依赖它来处理交易数据。为了减少破坏任何东西的机会，我们需要把工作分成小块。下面，我将介绍我们如何在 active record(Rail 的 ORM)之上构建一个中间 API，然后逐步构建功能以脱离直接 SQL 调用。

在构建中介 API 之前，我们需要弄清楚支付是如何被访问的。不幸的是，Rails 并没有让审计查询变得非常容易，我们的 monolith 包含了大约 100，000 行 Ruby 代码，不包括 200，000 行测试。因此，我们创建了一个新的 gem，[active _ record-sql _ analyzer](https://github.com/square/active_record-sql_analyzer)，它可以对来自 Ruby 的 SQL 查询和标记调用站点进行重复删除，以获得访问模式的概念。

我们最初的运行给了我们一个 500 个不同 SQL 查询的列表，我们用它产生了 7 个 protobuf APIs。这些最终成为我们新的支付搜索服务 Spot 的基础。我们选择用 MySQL 来支持它，用商户密钥来分片，因为我们知道我们的大多数访问模式都局限于单个商户。

# 对数据库进行抽象

当我们编写 SQL 查询时，ActiveRecord 的灵活性很好；当我们试图将它转换成 protobuf API 时，就没那么多了。我们还需要一个地方来传递电话，逐步推出新服务。

我们想出了一个简单的抽象层，就像这样:

```
*# This is a dramatic simplification from our actual code, but it’s a demonstration of concept.*
**module** PaymentApi
  **class** **NotFoundError** **<** StandardError; **end** **def** **self.lookup**(payment_token: **nil**, includes: **nil**)
    criteria **=** Payment**.**where(token: payment_token)
    criteria **=** criteria**.**includes(includes)
    *# with_tag is provided by active_record-sql_analyzer*
    criteria**.**with_tag(:api)**.**first
  **end** **def** **self.lookup!**(payment_token: **nil**, includes: **nil**)
    payment **=** lookup(payment_token: payment_token, includes: includes)
    **raise** NotFoundError, "Could not find payment #{payment_token}" **unless** payment payment
  **end**
**end**
```

然后，我们可以开始将 ActiveRecord 调用移植到 PaymentApi 类，并将迁移调用站点的工作分配给多个工程师。

# 提取活动记录

Spot 通过 RPC API 返回 protobufs，我们需要一种在 protobufs 支持的数据和 MySQL 支持的数据之间切换的方法，而不必重写每个调用站点。

我们决定编写一个转换器，将 ActiveRecord 模型转换成 Spot 使用的 protobuf，然后编写一个类，将 ActiveRecord 模型转换成 proto buf。这不是最有效的，但是它给了我们 Spot 和 ActiveRecord 之间的一致性。

扩展我们的 *PaymentApi* 类，我们添加了一个 *PaymentApi::Converter* 和 *PaymentApi::Wrapper* :

```
*# This is a dramatic simplification from our actual code, but it’s a demonstration of concept.*
**class** **PaymentApi**
  **class** **Converter**
    **def** **to_proto**(payment)
      Proto**::**Spot**::**Payment**.**new(
        card: {
          auth: {
            amount: payment**.**card_authorization**.**amount,
            authed_at: payment**.**card_authorization**.**created_at
          },
          captures: **[**
            {
              success: **true**,
              amount: payment**.**card_capture**.**amount
              captured_at: payment**.**card_capture**.**amount
            }
          **]**
        }
      )
    **end**
  **end** **class** **Wrapper**
    **def** **self.wrap**(proto)
      **new**(proto)
    **end** **def** **initialize**(proto)
      @proto **=** proto
    **end** **def** **auth_created_at**
      @proto**.**try(:card)**.**try(:auth)**.**try(:authed_at)
    **end** **def** **auth_amount**
      @proto**.**try(:card)**.**try(:auth)**.**try(:amount)
    **end** **def** **successful_capture**
      **if** @proto**.**captures
        @proto**.**captures**.**select { **|**capture**|** capture**[**:success**]** }**.**first
      **end**
    **end**
  **end**
**end**
```

现在我们有了一个转换器，我们可以扩展我们的*查找*调用:

```
*# This is a dramatic simplification from our actual code, but it’s a demonstration of concept.*
**class** **PaymentApi**
  **def** **self.lookup**(payment_token: **nil**, includes: **nil**, proto: **false**)
    criteria **=** Payment**.**where(token: payment_token)
    criteria **=** criteria**.**includes(includes)
    payment **=** criteria**.**with_tag(:api)**.**first **if** proto **&&** payment
      Wrapper**.**wrap(Converter**.**to_proto(payment))
    **else**
     payment
    **end**
  **end**
**end**
```

虽然 Spot 还没有准备好，但我们已经完成了我们的原型 API，可以开始迁移代码，而不必担心底层 API 的不断变化。最终完成原型 API 也有助于推广的顺利进行，因为如果我们在生产中发现了一个 bug，我们可以把这个标志关掉。

此时，我们也开始双运行我们的整个测试套件，打开和关闭 proto 包装器标志。这防止了任何其他工程团队发布破坏我们提取工作的代码，并确保我们知道旧的代码路径仍然有效。

# 现场审计与广场/网络审计

Square/Web 的数据库模式可以追溯到 2010 年，而 Spot 的是 2013 年，基于 protobufs。由于显著的差异，我们确认我们的包装器以 Square/Web 预期的方式返回数据:

```
*# This is a dramatic simplification from our actual code, but it’s a demonstration of concept.*
COMPARE_FIELDS **=** **%**i(auth_created_at auth_amount capture_amount currency_code)
Payment**.**order("id DESC")**.**limit(1000)**.**each **do** **|**payment**|**
   wrapped_payment **=** PaymentApi**::**Wrapper**.**wrap(Converter**.**to_proto(payment))
   spot_payment **=** HTTP**::**Rpc**::**Spot**.**load_payment(payment**.**token) puts "#{payment**.**token}"
   COMPARE_FIELDS**.**each **do** **|**field**|**
     spot_value **=** spot_payment**.**send(field)
     wrapped_value **=** wrapped_payment**.**send(field) **unless** spot_value **==** wrapped_value
       puts "- Mismatch, #{field}, found #{spot_value}, expected #{wrapped_value}"
     **end**
  **end**
**end**
```

我们手动运行它，直到我们修复了它发现的所有差异，这实际上捕获了许多微妙的错误。

# 呼叫点

现在，是时候把它们绑在一起了！我们对包装器有足够的信心，可以开始通过 Spot 发送请求。这只需要另一个标志和几行额外的代码。

```
*# This is a dramatic simplification from our actual code, but it’s a demonstration of concept.*
**class** **PaymentApi**
  **def** **lookup**(payment_token: **nil**, includes: **nil**, proto: **false**, via_spot: **nil**)
    **if** via_spot
      **return** Wrapper**.**wrap(HTTP**::**Rpc**::**Spot**.**load_payment(payment_token))
    **end** criteria **=** Payment**.**where(token: payment_token)
    criteria **=** criteria**.**includes(includes)
    payment **=** criteria**.**first **if** proto **&&** payment
      Wrapper**.**wrap(Converter**.**to_proto(payment))
    **else**
      payment
    **end**
  **end**
**end**
```

我们完了！我们开始将流量转移到 Spot，重新运行 SQL 分析器，观察查询数量的减少。

为了简单起见，我省略了如何将搜索移植到 Spot 中。我上面概述的是我们搜索遵循的相同过程。主要的区别是它需要对搜索参数进行更多的验证。

# 本可以变得更好的事情

在迁移任何东西之前，我们对与支付相关的代码进行了审计，以找到我们可以删除的任何东西。在提取过程中，我们总共提取了大约 56，000 行代码。

为了给我们的商家提供最好的体验，Square 尽可能避免贬低注册客户。我们在 Android 和 iOS 上都有两年前的活跃版本。每当我们删除返回到 Register 应用程序的字段时，我们最终不得不审核六个不同的代码库(iOS 和 Android 上的三个版本)。

我们有一些 API 使用了 [ActiveResource](https://github.com/rails/activeresource) 模式，这使得审计和迁移变得很困难。因为它提供了一个 HTTP - > SQL 层，所以很容易引入新的调用，使迁移成为一个移动的目标。

服务之间缺乏可靠的 API 契约，这使得很难确定使用了哪些数据，以及它们依赖于哪些 Square/Web 特有的特性。

# 我们会有什么不同的做法？

我们现有的 Square/Web 中其他服务的 API 主要使用 JSON。用 protobufs 之类的东西定义一个清晰的契约会更容易准确地理解需要哪些列和数据。

虽然我们不能对现有的 Register 客户机做任何事情，但是从一开始就使用 protobufs 会提供一个更清晰的审计线索，说明哪个版本需要什么数据。在构建基于各种支付状态的复杂层次结构时，无法审计 JSON 字段。

最后，我们犯了一个事后看来很明显的错误，那就是使用了随机推出，而不是对商家稳定。我们最终发现了一个回归错误，这个错误只在同时使用 Register 的六个月前的版本和最新版本时出现。在随机推出的情况下，这一点并不明显，因为在较低的百分比下，商家更有可能只是刷新一个页面，然后看到它工作。

两者都不是好的用户体验，但稳定的推出减轻了 Register 应用程序对少数商家的持续破坏，而不是对更多商家的持续破坏。

# 结束语

最终结果是在 2016 年 1 月成功提取，在关闭本地写入之前，所有读取将持续大约六周。如果有必要的话，我们可以更快地关闭写入，但我们认为没有必要在假期前对基础架构进行重大更改。

我要感谢我的同事们:[艾丽莎·波哈乌](https://twitter.com/arpohahau)、[安德鲁·拉扎勒斯](https://twitter.com/nerdrew)、[加布里埃尔·吉尔德](https://twitter.com/_gjg_)、[约翰·彭萨加潘](https://www.linkedin.com/in/johnpongsajapan)、[凯西·斯普拉德林](https://github.com/embark)和[马尼克·苏尔塔尼](https://twitter.com/maniksurtani)，他们都对计划和提取工作做出了贡献。有太多的人也为 Square/Web 的数据库和其他部分的稳定性做出了早期分析。这最终成为公司范围内的努力，直接涉及大多数产品工程团队和非工程团队。

[](https://twitter.com/zachanker) [## 扎卡里·安克尔(@zachanker) |推特

### Zachary Anker 的最新推文(@zachanker)。广场的工程师。旧金山

twitter.com](https://twitter.com/zachanker)