# 今天我学习了:使用 Golang 在 MongoDB 上进行文本搜索

> 原文：<https://medium.easyread.co/today-i-learn-text-search-on-mongodb-6b87cd8497c9?source=collection_archive---------2----------------------->

![](img/bf5fb825e87f4236ad99a9b18c763be2.png)

“person holding folders” by [Annie Theby](https://unsplash.com/@annietheby?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

当搜索一组**文章**时，我们通常会使用许多过滤器，如标题、日期、类别等。但是这些是找到它的最基本最简单的方法。

如何找到一篇带有*关键词*过滤器的文章，该过滤器基于文章**的标题、内容和其他成分加入**？

是的，上面有一个关于我们想做什么的方法，叫做**文本搜索**。

和大多数人一样，当我第一次遇到这个问题时，我开始通过在谷歌上搜索来寻找解决问题的方法。

![](img/ec9d83734281a8d37e7c2d4c5cc0f36f.png)

what is text search, google?

到目前为止，有 2 个著名的应用程序解决了我的问题。分别是[**elastic search**](https://www.elastic.co/)和 [**MongoDB**](https://www.mongodb.com/) 。在谷歌了一遍之后，我发现 Elasticsearch 在文本搜索方面比 MongoDB 更好，因为它是一个搜索引擎，你可以有很多配置来优化你的文本搜索。Elasticsearch 非常强大，它非常适合我的问题。但是对于我目前的项目，我认为使用 Elasticsearch 比技术更难。所以我决定先使用 MongoDB，直到我需要改进我的搜索功能。

我们去代码区吧！

![](img/52363793c2f1b1e9c3704a95ed4d93bc.png)

Gopher from Google

对于代码部分，我使用 Golang 作为编程语言。如果你不熟悉它，我建议你试试这种神奇的编程语言。我使用 Go 版本 1.10.3 和 [Dep](https://github.com/golang/dep) 作为依赖管理器，使用[https://github.com/globalsign/mgo](https://github.com/globalsign/mgo)作为 Golang 上的 MongoDB 客户端。你可能会问我为什么不使用 Golang 的官方 MongoDB 客户端。我选择使用这个包，因为这个包是 mgo 的一个分支(遗憾的是已经没有维护过了)，mgo 被证明是可靠的 MongoDB 客户端。除此之外，官方的 MongoDB 驱动程序仍然处于 alpha 状态。

要在 MongoDB 上启用文本搜索特性，您必须创建一个文本索引并使用$text 操作符。在 MongoDB 上创建文本索引非常简单，您可以在 Mongo CLI 上这样做。

```
db.article.createIndex( { title: “text” } )
```

在这段代码中，您创建了一个索引，它将`title`字段标记为文本索引。您还可以将多个字段定义为文本索引，如下所示。

```
db.article.createIndex(
   {
     title: "text",
     content: "text"
   }
 )
```

现在每个领域已经变得可搜索，你可以根据这些领域的一些关键字搜索一些文章，但如果你要优先考虑一个领域，你可以应用权重。当应用过滤器时，该权重对评分过程很重要。要定义每个字段的权重，可以这样写。

```
db.article.createIndex(
   {
     title: "text",
     content: "text"
   },
   {
     weights: {
       title: 9,
       content: 3
     }
   }
)
```

就这一点而言，在得分方面，`title`比`content`重要 3 倍。示例中有两篇文章，第一篇是这样的:

```
Title: Kurio is Moving to New OfficeContent: Kurio, one of the best news aggregator application in Indonesia is having a new office. Now they have their own playground to make their app even better!
```

第二个:

```
Title: A Big News Startup is Moving to New OfficeContent: Kurio, one of the best news aggregator application in Indonesia is having a new office. Now they have their own playground to make their app even better!
```

如果您应用这些权重选项，第一篇文章将比第二篇文章得分高。如果你进行查询，你会得到第一篇文章的结果，然后是第二篇文章。

MongoDB 将自动命名您的索引，但是自己命名也不错，对吧？

```
db.article.createIndex(
   {
     title: "text",
     content: "text"
   },
   {
     weights: {
       title: 9,
       content: 3
     },
     name: "textIndex"
   }
)
```

对于 Golang 实现，可以这样写:

```
c := session.DB("article_database").C("article")index := mgo.Index{
    Key: []string{"$text:title", "$text:content"},
    Weights: map[string]int{
          "title":   9,
          "content": 3,
    },
    Name: "textIndex",
}c.EnsureIndex(index)
```

创建文本索引后，可以使用$text 操作符在集合中直接搜索或应用过滤器。在 MongoDB CLI 中，您可以这样写:

```
db.article.find( { $text: { $search: "Pemilu Indonesia 2019" } } )
```

这是 Go 的实现:

```
articles := []Article{}query := bson.M{
    "$text": bson.M{
    "$search": "Pemilu Indonesia 2019",
   },
}err := sess.DB("article_database").C("article").Find(query).All(&articles)if err != nil {
   return err
}
```

默认情况下，MongoDB 不会对查询结果进行排序。但是文本搜索查询已经基于数据与那些查询的匹配程度来计算相关性分数。所以，我应用了基于分数的排序。下面是如何在 MongoDB CLI 上实现这一点:

```
db.article.find(
   { $text: { $search: "Pemilu Indonesia 2019" } },
   { score: { $meta: "textScore" } }
).sort( { score: { $meta: "textScore" } } )
```

Go 实现也不复杂:

```
articles := []Article{}query := bson.M{
    "$text": bson.M{
    "$search": "Pemilu Indonesia 2019",
   },
}err := sess.DB("article_database").C("article").Find(query).Sort(bson.M{
  "score": bson.M{
     "$meta": "textScore",
  },
}).All(&articles)if err != nil {
   return err
}
```

就是这样！现在你有了一个简单而有效的**文本搜索**用于你的应用。