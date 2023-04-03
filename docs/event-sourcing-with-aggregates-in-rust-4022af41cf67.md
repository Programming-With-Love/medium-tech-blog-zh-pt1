# 使用 Rust 中的骨料进行事件采购

> 原文：<https://medium.com/capital-one-tech/event-sourcing-with-aggregates-in-rust-4022af41cf67?source=collection_archive---------0----------------------->

![](img/eda276a924cfb6585a1c3f4b67b36e6a.png)

每个开发人员都喜欢*事件源*，直到他们必须实现它的那一刻。在那一刻，当你试图将这种模式映射到你的业务领域时，所有从会议出席中收集的精彩白板绘图和灵感都嘎然而止。

无可否认，我曾在那艘沉船上。我是不可变数据流、事件源、命令和查询分离以及将状态视为事件流的函数的好处的大力倡导者。然而，考虑到所有这些，我只能实现一些“纯粹的”事件源系统。黑客出现了——他们从敞开的伤口流血，他们像啮齿动物一样从你墙上的咬洞爬进来，他们破坏一切。

如果您对这个概念不熟悉，*事件源*是指您的共享状态在适当的位置是不可变的。相反，它是连续应用不可变事件的结果，这些事件代表过去发生的事情。正如我在书中所说，[云原生 Go](https://www.amazon.com/Cloud-Native-Applications-Microservices-Developers/dp/0672337797/ref=sr_1_2?ie=UTF8&qid=1519135681&sr=8-2&keywords=cloud+native+go) 和[微服务采用 ASP.NET 核心](https://www.amazon.com/Building-Microservices-ASP-NET-Core-Cross-Platform/dp/1491961732/ref=sr_1_4?s=books&ie=UTF8&qid=1519135704&sr=1-4&keywords=microservices+with+asp.net+core)，*现实是事件源*。你的大脑消耗多种输入流，并根据头脑中存储的历史、感官输入等来计算状态(我们对“现实”的概念)。一旦你开始把问题想象成事件源问题，就很难以其他方式看待它们。

*集合*是一个领域驱动设计(DDD)的概念，非常适合事件采购。简而言之:您将一个 ***命令*** 应用于一个*集合，然后该集合产生一个或多个 ***事件*** 。通过顺序应用事件流，聚集可以填充(重新水合)其状态。*

*关于这个话题的更多信息，请随意去谷歌*活动采购*的兔子洞。在这篇文章的剩余部分，我想讨论我们如何实现类似于*事件源*和*聚合*在 **Rust** 中的东西，而不让黑客从缝隙中钻进来。*

*第一个问题是——你如何表现一个事件？在我的示例中，我正在处理一个领域，在这个领域中，我们的产品(由一个 **SKU** 惟一标识)在仓库中有一个可测量的现有量。产品可能发生的事件包括被保留(未发货订单“预订”了一些数量)、被发布(订单被取消)或被发货(之前保留的数量实际上已经离开仓库)。*

*我的第一反应是创建一些结构:*

*   *产品装运*
*   *产品保留*
*   *产品发布*

*这些结构中的每一个都有一些有效载荷数据，比如时间戳等。然后，我可以将这些序列化到队列中，以便进行下游处理。表面上看，这对我来说是个好主意。*

*直到我设想了“切换”语句，在那里我必须打开*类型*，而不是*内容*。这对我来说就像是一个警告信号，表明我走错了方向。使用 Rust，我可以很容易地对内容进行模式匹配，所以我决定使用带有 struct 变量的枚举:*

```
***struct** ProductEventData {
    quantity: **usize**,
    timestamp: **usize**,
    sku: String,
}

**enum** ProductEvent {
    *Reserved*(ProductEventData),
    *Released*(ProductEventData),
    *Shipped*(ProductEventData),
}*
```

*这里我有一个结构，如果我愿意，我可以为它派生序列化，但有趣的是，现在我有了代表产品可能发生的每种事件类型的`ProductEvent::*`枚举，并且我可以*将所有产品事件作为单一类型*。当我想要构建一个聚合时，这非常方便。*

*如前所述，聚合表示计算状态。然而，它也是执行*命令*的对象。一个命令是一个*命令*,用于聚合验证。如果命令成功，那么聚合将返回一个准备发出的事件列表。一些 ES 实现喜欢让聚合直接发出事件，但是我喜欢将事件发出作为它自己的关注点，允许我直接测试我的业务逻辑，而不必模拟队列客户端。*

*例如，产品聚合可能有一个名为 ***ship(…)*** 的命令。如果成功，我可能会得到`ProductEvent::Shipped(...)` enum 作为响应。我可以得到多个事件来响应一个命令，这在很多领域都是如此。*

*那么我们如何定义一个集合呢？所有聚合必须能够将事件按顺序应用到它们的状态。从数学上来说，看起来是这样的:*

```
*f(state`1 + event) = state`2*
```

*将一个事件应用于聚合的结果是产生具有新状态的聚合。这是一个非常*功能化的*看待它的方式，稍后我会解释更多关于我对不变性的喜爱。*

*接下来，让我们构建一个描述事件集合的特征:*

```
***trait** Aggregate {
    **type** Item;

    **fn** version(&**self**) -> **u64**;
    **fn** apply(&**self**, evt: &**Self**::Item) -> **Self where Self**:Sized;
}*
```

*这里我们说所有的聚合必须有一个版本，并且它们都必须有一个 *apply* 方法，该方法接受一个`Item`类型的事件并产生一个`Self`类型的新东西(实现特征的东西的真实类型，而不是特征类型本身)。要求`Self:Sized`仅仅意味着我们需要一个可预测内存占用的东西，而不是一个装箱的特征对象。*

*接下来，因为枚举在 Rust 中是如此强大和灵活，所以让我们添加一些方便的方法，这样我们就可以创建新的枚举以及它们的内部结构有效载荷:*

```
***impl** ProductEvent {
    **fn *reserved***(sku: &**str**, qty: **usize**) -> ProductEvent {
        ProductEvent::*Reserved*(ProductEventData {
            quantity: qty,
            sku: sku.to_owned(),
            timestamp: 1,
        })
    }
    **fn *shipped***(sku: &**str**, qty: **usize**) -> ProductEvent {
        ProductEvent::*Shipped*(ProductEventData {
            quantity: qty,
            sku: sku.to_owned(),
            timestamp: 1,
        })
    }
    **fn *released***(sku: &**str**, qty: **usize**) -> ProductEvent {
        ProductEvent::*Released*(ProductEventData {
            quantity: qty,
            sku: sku.to_owned(),
            timestamp: 1,
        })
    }
}*
```

*现在我们已经准备好开始实现一个特定于领域的聚合，`ProductAggregate`，它代表了一个产品的计算状态。这也是我认为经常被忽视的重要一点——聚合是对单个实体的计算，而不是对应用程序的整个状态的计算。进一步说，*聚集体是短命的*。它们的寿命足以计算状态和验证命令，就这样*。如果您正在构建一个无状态的服务，它将在请求结束时处理这个集合。**

**这里是我们的`ProductAggregate`:**

```
**#[derive(Debug)]
**struct** ProductAggregate {
    version: **usize**,
    qty_on_hand: **usize**,
    sku: String,
}**
```

**我们正在维护版本，这对于任何复杂性的事件源实现都是必不可少的。这允许我们处理重放情况(重放到版本“x”)，并且当多个聚集同时持久化时，可能解决“合并冲突”……但是那是另一篇博文的内容了😀**

**让我们将一些命令放在我们的集合中:**

```
****impl** ProductAggregate {
    **fn *new***(sku: &**str**, initial_quantity: **usize**) -> ProductAggregate {
        ProductAggregate {
            version: 1,
            qty_on_hand: initial_quantity,
            sku: sku.to_owned(),
        }
    }

    **fn reserve_quantity**(&**self**, qty: **usize**) -> Result<Vec<ProductEvent>, String> {
        **if** qty > **self**.qty_on_hand {
            **let** msg = format!(
                "Cannot reserve more than on hand quantity ({})",
                **self**.qty_on_hand
            );
            *Err*(msg)
        } **else if self**.version == 0 {
            *Err*(
                "Cannot apply a command to an un-initialized aggregate. Did you forget something?"
                    .to_owned(),
            )
        } **else** {
            *Ok*(vec![ProductEvent::*reserved*(&**self**.sku, qty)])
        }
    }

    **fn release_quantity**(&**self**, qty: **usize**) -> Result<Vec<ProductEvent>, String> {
        *Ok*(vec![ProductEvent::*released*(&**self**.sku, qty)])
    }

    **fn ship_quantity**(&**self**, qty: **usize**) -> Result<Vec<ProductEvent>, String> {
        *Ok*(vec![ProductEvent::*shipped*(&**self**.sku, qty)])
    }

    **fn** quantity(&**self**) -> **usize** {
        **self**.qty_on_hand
    }
}**
```

**我试图保持领域逻辑简单，这样我们就可以关注重要的部分(命令模式)。在`reserve_quantity`的情况下，如果我们试图保留比我们手头更多的库存，我们将返回一个`Err`。在真实世界的应用程序中，可能会有更多的验证步骤，但是这里的`Result`类型工作得很好——成功时我们得到一个`Vec`事件，否则我们得到一个包含字符串的`Err`。同样值得注意的是，返回的事件*并不应用于聚合*。我非常明确地保持命令操作*没有副作用*并且尽可能地遵守功能原则。**

**如果您想在调用命令后将返回的事件应用到集合*，那是您的选择，但是至少那些阅读您代码的人会清楚地知道发生了什么以及(希望)为什么。***

**正如您可能已经猜到的那样，聚合验证传入命令的能力依赖于它已经计算出状态这一事实。为了计算状态，我们通过*应用*方法抽取一个事件流。很多人喜欢这里的**可变**聚合，他们在同一个聚合上反复调用*应用*。**

**出于个人原因，由于可变聚合在生产中造成的长期和传奇的、难以诊断的问题，我想采用一种更函数化的方法，让我的 *apply* 方法返回一个*全新的*聚合，其计算状态为:**

```
****impl** Aggregate **for** ProductAggregate {
    **type** Item = ProductEvent;

    **fn** version(&**self**) -> **u64** {
        **self**.version
    }

    **fn** apply(&**self**, evt: &ProductEvent) -> ProductAggregate {
      ProductAggregate {
        version: **self**.version + 1,
        qty_on_hand: **match** evt {
           &ProductEvent::*Released*(
            ProductEventData { quantity: qty, .. }) => {
                  **self**.qty_on_hand + qty
           },
           &ProductEvent::*Reserved*(
            ProductEventData { quantity: qty, .. }) => {
                    **self**.qty_on_hand - qty
           },
            _ => **self**.qty_on_hand,
          },
        sku: **self**.sku.clone(),
       }
   }
}**
```

**在前面的代码中发生了大量非常酷的错误。首先，你会注意到我使用了一个*关联类型*来表示这个聚合流程的类型`Item`是`ProductEvent`变体。然后我可以在我的模式匹配中使用*析构*来取出*只是`ProductEventData`结构的`qty`字段，以便产生新计算的集合。***

**有人向我指出，有一种更好的方式来做`apply`。在我对*易变性*的偏执反抗中，我忽略了 Rust 对*移动*的概念。在 Rust 中，当你赋值的时候，默认的行为是*将*的值从一个地方移动到另一个地方(让之前的位置作为变量不可用)。在这一过程中，进行变异是安全的(也是最受欢迎的),因为*你可以保证在那一刻没有其他代码引用你正在变异的东西*。这是在编译时强制执行的*，同时也是一个令人沮丧和解放的特性。***

**接受移动并在`apply`内改变聚合避免了堆栈上的额外分配，并避免了在 SKU 字符串上对`clone()`的调用:**

```
****fn** **apply**(mut **self**, evt: &ProductEvent) -> ProductAggregate {
  **self**.version += 1;
  **self**.qty_on_hand = **match** evt {
    &ProductEvent::Released(
       ProductEventData{quantity:qty,..}) => 
         **self**.qty_on_hand.**checked_add**(qty).unwrap_or(0),
    &ProductEvent::Reserved(
       ProductEventData{quantity:qty,..}) => 
         **self**.qty_on_hand.**checked_sub**(qty).unwrap_or(0),
    _ => **self**.qty_on_hand,
  };
  **self**
}**
```

**这个替代版本更高效、更易读，并且它还强调了一个微妙但关键的点:有对`checked_sub`或`checked_add`的调用，这些调用将一个可能引起恐慌的数学运算转换成一个`Option`类型。这里我调用`unwrap_or(0)`来使用值 0，如果真实的数量会产生溢出。**

**这让我想到了另一个我们经常忽略的事件源领域:*聚合的损坏*。这里真正应该发生的是，如果`checked_sub`返回`None`，我们应该将`self.over_reserved`(或一些类似的故障指示器)设置为 true。如果我们真的很勤奋，我们可能还想保存对产生腐败的事件的引用。**

**理想情况下，不应该有任何东西进入持久事件存储库，否则会破坏集合，但是我们应该始终警惕这样的事情。*命令*验证器补丁中的一段坏代码可能会让一个事件通过，这可能会搞乱我们的聚合，*尤其是*如果命令和查询服务被隔离的话。这种逻辑会产生连锁故障。如果您的代码基于静默抑制聚合的不良事件序列做出决策，它可能会在下游创建进一步的损坏事件。*永远不要让一件坏事产生一个好的总和*。**

**最后，现在我们已经实现了聚合的命令和应用方面，我们可以调用 apply 来计算状态:**

```
****let** soap = ProductAggregate::*new*("SOAP", 100);
**let** u = soap.apply(&ProductEvent::*reserved*("SOAP", 10));
**let** u = u.apply(&ProductEvent::*reserved*("SOAP", 30));**
```

**这里的另一个微妙之处是， *apply* 方法接受一个对事件的*引用*。这明确地告诉聚合的使用者，它没有声明事件的所有权。如果我们使用的是效率更高的`apply`版本，那么如果我们在第一次调用`apply`后试图引用`soap`，我们就会与 Rust 臭名昭著的借项检查器发生冲突(错误消息会显示产生错误的确切行，并类似于*“移动后此处使用的值”*)。**

**前面的代码是*好的*，但是既然我们使用了 Rust，我想我们可以做得更好。前面我提到过，想要一个更函数化的方法来计算状态可能是别有用心的。那些已经在函数式语言中工作了一段时间的人可能会认识到，重复地将一个事物重新赋值给该事物的新版本正是一个 ***fold*** 所做的事情。**

**我们可以将一个事件向量转换成一个迭代器，并通过一个**文件夹**将它们传送出去，如下所示:**

```
****let** agg = v.iter()
    .fold(ProductAggregate::*new*("CEREAL", 100), |a, **ref** b| {
        a.apply(b)
    });**
```

**如果我们已经有了最初的产品，我们可以写得更简单:**

```
****let** agg = v.iter().fold(init, |a, **ref** b| a.apply(b));**
```

**现在*这个*就是事情开始有回报的地方。那里有很多力量在发生。在 Rust 中，可变性是由消费者选择的，而不是由结构决定的。因此，fold 的`init`参数，即我的“第一个”值，是一个初始现有量为 100 的*不可变集合*。然后我可以调用 fold，在 fold 的每次迭代中，我只需返回`a.apply(b)`。在更高效的`apply`版本中，这将聚合移入返回值，而不是分配一个新值。现在我们开始看到这种重构的一些好处:事件向量越大，`apply`的“复制和分配”版本的性能就越差。**

**我不是在整个文件夹中传递一个可变值。相反，我在每一步之后都返回一个新的、不变的值。最后，`agg`变量是不可变的，它将包含我所有的计算状态。因为我已经让所有的命令方法*返回*事件，而不是改变集合本身，所以我可以直接对`agg`变量发出命令，而不必担心改变可变性或与 Rust 的借用检查器冲突。**

**总之，这只是一个事件采购从业者的观点和示例实现。我在这里的目标不是向大家展示有史以来最好的 ES 实现，而是提醒大家，我们仍然可以拥有 Rust 的所有低级性能和安全性，同时仍然可以为高级模式(如事件源和 CQRS)生成简单而优雅的代码。**

***披露声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。***