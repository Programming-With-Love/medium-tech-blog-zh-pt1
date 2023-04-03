# Swift 通用协议类型擦除的替代方案

> 原文：<https://medium.com/capital-one-tech/an-alternative-to-type-erasure-for-generic-protocols-a9a48e96618a?source=collection_archive---------0----------------------->

![](img/edd406d0fcfba79ef565b71bdb401850.png)

*我非常感谢*[*Aqeel*](https://www.linkedin.com/in/aqeel-gunja-8297647)*[*gun ja*](https://twitter.com/aqgunja)*为本贴提供了许多观点。**

# *介绍*

*在旗舰产品 Capital One iOS 应用程序中，我们处理许多不同类型的账户——卡、银行、投资等。有些情况下，我们希望能够收集客户账户的所有信息。一个典型的例子是计算总净值。*

*为了完成类似上面的任务，我一直在使用*通用协议*(也称为*协议和相关类型*)。在我使用通用协议的不同场景中，有一个共同点，那就是需要迭代符合相同通用协议的多个对象。不幸的是，由于感兴趣的协议有一个关联的类型，任务并不像我希望的那样简单。*

*这种情况并非银行类 app 独有。在任何时候，当你想通过一个通用的抽象(协议)来检索数据，而同时又需要通过强类型来对这样的数据实施类型安全时，你可能会在任何类型的应用上遇到同样的问题。因此，我想我会把我在 Capital One 的工作中学到的东西展示出来，这样其他开发者就可以在他们的应用中利用它们。*

# *用例场景:一个旅游预订应用*

*为了通过一个简单的用例场景来说明我遇到的问题，让我们假设我们正在构建一个旅行预订应用程序，允许用户预订航班、酒店和汽车租赁。这种探索的起点是具有相关类型的协议:*

*上面的`Fetchable`协议抽象了我们如何检索数据(网络响应、ManagedObject 集合等。).关联类型`DataType`定义了将由`fetch`方法返回的泛型类型。*

*下一步是创建一个协议来抽象预订的公共属性:*

*上述协议定义了完整定义预订所需的最少信息:*

*   *`identifier`:预订的唯一标识符。*
*   *`startDate`:预订开始日期。*
*   *`endDate`:预订结束日期。*

*现在，在为每种不同类型的预订创建特定结构时，我们应该遵循`Bookable`:*

*对于每种预订类型，我们将定义一个适当的*获取器*，负责检索所需的数据。具体来说，每个提取器定义其负责的特定预订类型(`FlightBooking`、`HotelBooking`、`RentalBooking`)所需的`DataType`。*

*由于它与下面的讨论无关，我将跳过数据检索代码，让每个提取器只返回一些模拟数据。*

***注意**:为了简洁起见，在`Date`上提供了`bookingDate`扩展方法，以确保我们总是返回一个非零值。在生产代码中，你应该根据你的应用程序的具体要求来处理一个可能为零的值。*

*现在我们拥有了创建负责检索所有预订信息的`BookingCoordinator`所需的所有元素:*

# *具有相关类型的协议支持现成的迭代吗？*

*你认为检索所有预订信息的好方法是什么？现在，为了这篇文章，我将假装我从来没有处理过 Swift 中与相关类型的复杂协议。相反，我将从适用于任何高级编程语言的角度来完成上述任务。*

*我个人认为，执行所需任务的一个好方法如下:*

*   *创建每个预订获取器的实例。*
*   *将每个实例添加到数组中。*
*   *迭代每个数组项以调用通用的`fetch`方法。*

*这将使代码清晰易懂。*

## *尝试#1*

*通过如上所述构建我们的代码，我们可能会得到类似这样的结果:*

*现在，编译器抛出以下错误:*

```
*Heterogeneous collection literal could only be inferred to '[Any]'; add explicit type annotation if this is intentional*
```

## *尝试#2*

*好吧，我猜编译器没有识别出数组中的所有项都符合`Fetchable`协议。让我们指定数组的类型:*

*经过上述更改后，编译器现在抛出:*

```
*Protocol 'Fetchable' can only be used as a generic constraint because it has Self or associated type requirements*
```

*许多 Swift 开发人员可能对这个错误很熟悉，他们使用了带有相关类型的[协议。无需深究细节，在这种特殊情况下，该错误意味着 Swift 编译器无法管理符合相同协议且具有相关类型的项目数组。由于数组的元素属于不同的类型，即使它们中的每一个都符合具有相关类型的相同协议，编译器也不能保证我们将调用泛型`fetch`方法的正确实现。](http://www.russbishop.net/swift-associated-types)*

## *尝试#3*

*由于我们不能指定一个满足编译器的类型，让我们试着通过使用`Any`类型来解决这个问题，它对于任何 Swift 类或结构都是有效的类型:*

*在某种程度上，这类似于简单版本的*类型擦除*，编译器不再抱怨`fetcher`项的数组。但是这并没有带我们走很远，因为编译器现在无法找到`fetch`方法的定义:*

```
*Value of type 'Any' has no member 'fetch'*
```

## *尝试#4*

*哎呀！我们删除了关于类型的信息，现在编译器找不到`fetch`方法的定义。我们可以指示编译器将每个数组项强制转换为`Fetchable`协议，使其能够找到`fetch`方法的定义:*

*但是这并没有让我们走得更远，因为编译器仍然不能保证我们将调用泛型`fetch`方法的正确实现:*

```
*Member 'fetch' cannot be used on value of protocol type 'Fetchable'; use a generic constraint instead

Protocol 'Fetchable' can only be used as a generic constraint because it has Self or associated type requirements*
```

## *尝试 5*

*我们现在可以试着从`Array`切换到`Dictionary`，看看会发生什么:*

*不幸的是，这种尝试对我们没有太大帮助，因为编译器现在抛出了我们之前在尝试使用一组`Fetchable`项时看到的相同错误:*

```
*Protocol 'Fetchable' can only be used as a generic constraint because it has Self or associated type requirements*
```

## *不，具有关联类型的协议不支持现成的迭代*

*此时，获取所有预订的唯一可行方法似乎是单独调用每个获取器:*

*这是可行的，但是:*

*   *扩展性不好。*
*   *这需要大量的代码复制。*

# *打字擦除有帮助吗？*

*一种解决与具有关联类型的协议相关的问题的常见方法是 [*类型擦除*](https://www.bignerdranch.com/blog/breaking-down-type-erasures-in-swift/) 。我不打算在这里解释这种技术，因为这个主题值得单独发布。我也不打算为这个特定的场景说明我的类型擦除实验的细节。我*正要*说的是，我试图利用类型擦除来实现我的最初目标(即:*迭代一个符合与类型*相关的相同协议的项目数组)并不成功。*

# *支持强类型协议迭代的另一种方法:类型包装*

*在与我的一些同事讨论了上述问题之后，我们找到了一种方法来解决与迭代相关的关联类型协议的局限性。在这篇文章的剩余部分，我将说明我们是如何修改原始代码来支持迭代的。*

*我将首先声明，我们提出的解决方案不是灵丹妙药，但对于我们的特定目的来说非常有效。建议的解决方案不使用具有关联类型的协议；相反，它关注于通过使用我们称之为`Type Wrapping`的方法将协议与其相关类型分离来获得类似的结果。这种方法仍然能够提供如下内容:*

*   *对类型关联的保证。*
*   *通过强类型实现类型安全。*

*让我们开始检查我们方法的构件:*

*如您所见，我们的主协议(`Fetchable`)已经没有关联的类型了。相反，`fetch`方法现在已经变得通用，需要一个完成块来接收一个符合新`FetchableType`协议的类型的实例。在这个上下文中，`FetchableType`被用作类型的占位符。*

*一般来说，`FetchableType`可以是我们希望通过`fetch`方法返回的任何类型。在这个特定的场景中，`FetchableType`将基本上包装我们希望为每种预订类型返回的商品数组。*

*`FlightBooking`、`HotelBooking`、`RentalBooking`类不变。*“魔法”*发生在 fetcher 结构中。首先，对于每个特定的提取器，我们将创建一个符合`FetchableType`的包装器结构。这样做的唯一目的是包装我们想要返回的项目数组。下面是我们将用于每个特定提取器的包装器:*

*在上面的包装器中，我们将包装后的数组命名为`bookings`以提供统一的名称。这简化了抽象，即使它不是严格需要的(我们可以用任何我们喜欢的方式命名包装的数组)。现在，每个提取器都可以安全地返回包装在适当结构中的特定预订信息:*

*在通过`FetchableType`协议包装了每种预订类型之后，我们的努力就成功了。我们最终能够迭代预订获取器条目，这些条目被添加到一个数组`Fetchable`中，并根据需要调用泛型`fetch`方法:*

# *结论*

*在这篇文章中，我描述了我个人对通用协议的一些局限性的体验。特别是，我把重点放在了迭代多个对象的问题上，这些对象符合具有关联类型的相同协议。然后，我举例说明了我们在 Capital One 成功应用的一项技术，以解决这些限制。我们称这种技术为`Type Wrapping`,希望它能对任何经历过同样问题的人有用。*

*你可以在 [GitHub](https://github.com/andrea-prearo/SwiftExamples/tree/master/GenericProtocols/TypeWrapping) 上找到说明本文中讨论的问题和解决方案的代码示例。*

## *有关系的*

*   *[Swift 中的参考和值类型](/capital-one-developers/reference-and-value-types-in-swift-de792db330b2)*
*   *[在 UITableView 和 UICollectionView 中平滑滚动](/capital-one-developers/smooth-scrolling-in-uitableview-and-uicollectionview-a012045d77f)*
*   *[用 iOS 10 预取 API 提升平滑滚动](/capital-one-developers/boost-smooth-scrolling-with-ios-10-pre-fetching-api-818c25cd9c5d)*
*   *[Swift 中的通用数据源](/capital-one-developers/generic-data-sources-in-swift-c6fbb531520e)*

**披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018**