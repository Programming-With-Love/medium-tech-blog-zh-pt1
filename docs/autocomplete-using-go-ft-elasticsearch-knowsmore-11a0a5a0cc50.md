# 使用 Go ft 自动完成。Elasticsearch，Knowsmore！

> 原文：<https://medium.easyread.co/autocomplete-using-go-ft-elasticsearch-knowsmore-11a0a5a0cc50?source=collection_archive---------3----------------------->

![](img/5b592ab35b393736c15413937aa7ec98.png)

Photo by [Javier Allegue Barros](https://unsplash.com/@soymeraki?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> 上周末，我一下午都在看[**拉尔夫断网**](https://www.imdb.com/title/tt5848272/) *。这是部好电影，我已经看了两遍了。在那部电影里，有一个配角叫[**knows more**](https://disney.fandom.com/wiki/KnowsMore)**。就像现实世界中的谷歌一样，他可以搜索任何东西，甚至可以通过他的自动完成功能提供建议。这就是我建这个项目的原因。***

好了，我们开始吧。

我们想要构建 [*自动完成*](https://www.computerhope.com/jargon/a/autocomp.htm) 引擎，使用 go 作为我们的编程语言，使用 ElasticSearch 作为搜索引擎。

**环境**

*   Ubuntu 16.04
*   转到 10.0.1 ( [安装](https://www.admfactory.com/how-to-install-golang-1-10-on-ubuntu/))
*   弹性搜索 5.6(安装在[本地](https://www.elastic.co/guide/en/beats/libbeat/5.6/elasticsearch-installation.html)或[对接器](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/docker.html)上)

**第一步:在 Elasticsearch 中创建映射**

首先，我们需要在 ElasticSearch 中创建我们的 [*映射*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html) 。映射是定义如何存储和索引文档及其包含的字段的过程。

为了从我们的数据中自动完成，我们需要在我们的字段中有`completion`数据类型。

```
PUT member
{
    "mappings": {
        "_doc" : {
            "properties" : {
                "suggest" : {
                    "type" : "completion"
                },
                "name" : {
                    "type": "keyword",
                    "copy_to": "suggest"
                },
                "url" : {
                    "type": "keyword"
                }
            }
        }
    }
}
```

在这种情况下，我们有一个名为`member`的索引。我们有三个字段，`name`和`url`使用 [*关键字*](https://www.elastic.co/guide/en/elasticsearch/reference/current/keyword.html) 类型，`suggest`使用 [*完成*](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html) 类型。

**第二步:将数据推送到您的新索引成员**

现在，我们的 ElasticSearch 节点中有了`member`索引。我们需要用数据来填充它。我已经在 JSON 中为你做了一些虚拟数据。

```
{
  "member": [
    {
      "name": "Ruben Panjaitan",
      "url": "[https://www.facebook.com/search/str/ruben](https://www.facebook.com/search/str/ruben) panjaitan/keywords_search",
      "suggest": "Ruben Panjaitan"
    },
    {
      "name": "Roberto Tambunan",
      "url": "[https://www.facebook.com/search/str/roberto](https://www.facebook.com/search/str/roberto) tambunan/keywords_search",
      "suggest": "Roberto Tambunan"
    },
    {
      "name": "Ivandra Oktovan",
      "url": "[https://www.facebook.com/search/str/ivandra](https://www.facebook.com/search/str/ivandra) oktovan/keywords_search",
      "suggest": "Ivandra Oktovan"
    },
    {
      "name": "Iman Situmorang",
      "url": "[https://www.facebook.com/search/str/iman](https://www.facebook.com/search/str/iman) situmorang/keywords_search",
      "suggest": "Iman Situmorang"
    },
    {
      "name": "Septian Siahaan",
      "url": "[https://www.facebook.com/search/str/septian](https://www.facebook.com/search/str/septian) siahaan/keywords_search",
      "suggest": "Septian Siahaan"
    },
    {
      "name": "Setia Simaremare",
      "url": "[https://www.facebook.com/search/str/setia](https://www.facebook.com/search/str/setia) simaremare/keywords_search",
      "suggest": "Setia Simaremare"
    },
    {
      "name": "Raymond Sitepu",
      "url": "[https://www.facebook.com/search/str/raymond](https://www.facebook.com/search/str/raymond) sitepu/keywords_search",
      "suggest": "Raymond Sitepu"
    },
    {
      "name": "Witri Manurung",
      "url": "[https://www.facebook.com/search/str/witri](https://www.facebook.com/search/str/witri) manurung/rykeywords_search",
      "suggest": "Witri Manurung"
    }
  ]
}
```

上面的数据是和我一起住在宿舍的朋友的名单。沿着这个[链接](http://queirozf.com/entries/elasticsearch-bulk-inserting-examples)将批量数据推送到 ElasticSearch。

**步骤 3:使用 Elasticsearch 查询尝试自动完成**

在将数据推送到我们的索引中之后，我们希望数据已经在那里，并准备好进行查询。尝试这个查询以确保它在那里。

```
POST member/_search
{
    "suggest": {
        "member-suggest" : {
            "prefix" : "r", 
            "completion" : { 
                "field" : "suggest" 
            }
        }
    }
}
```

上面的查询试图建议带有前缀`r`的成员。

你会得到结果的。

```
{
  "took": 16,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 0,
    "max_score": 0,
    "hits": [

    ]
  },
  "suggest": {
    "member-suggest": [
      {
        "text": "r",
        "offset": 0,
        "length": 1,
        "options": [
          {
            "text": "Ruben Panjaitan",
            "_index": "member",
            "_type": "_doc",
            "_id": "_CQObGgBdY5EcoMWna_8",
            "_score": 1,
            "_source": {
              "name": "Ruben Panjaitan",
              "url": "[https://www.facebook.com/search/str/ruben](https://www.facebook.com/search/str/ruben) panjaitan/keywords_search",
              "suggest": "Ruben Panjaitan"
            }
          },
          {
            "text": "Roberto Tambunan",
            "_index": "member",
            "_type": "_doc",
            "_id": "_SQObGgBdY5EcoMWna_8",
            "_score": 1,
            "_source": {
              "name": "Roberto Tambunan",
              "url": "[https://www.facebook.com/search/str/roberto](https://www.facebook.com/search/str/roberto) tambunan/keywords_search",
              "suggest": "Roberto Tambunan"
            }
          },
          {
            "text": "Raymond Sitepu",
            "_index": "member",
            "_type": "_doc",
            "_id": "AiQObGgBdY5EcoMWnbD8",
            "_score": 1,
            "_source": {
              "name": "Raymond Sitepu",
              "url": "[https://www.facebook.com/search/str/raymond](https://www.facebook.com/search/str/raymond) sitepu/keywords_search",
              "suggest": "Raymond Sitepu"
            }
          }
        ]
      }
    ]
  }
}
```

我们将简化结果。所以从上面的结果，我们得到三个成员，他们是:

*   鲁本·潘杰坦
*   罗伯托·坦布南
*   雷蒙德·斯特普

是的，结果是对的。我们的列表中有三个前缀为`r`的成员。你可以看看我们的 JSON 数据。您可以使用另一个前缀尝试另一个查询。仅供参考，ElasticSearch 为我们提供了一些更高级的查询来改进我们的建议结果，如模糊性、换位等。你可以在这里 阅读更多详细 [*。*](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html)

**第四步:开始编码**

打开你的编辑器，新建一个 golang 项目，准备好！我使用[*olivere V5*](https://olivere.github.io/elastic/)*作为我的 ElasticSearch 库。你也可以在这里阅读 godoc[](https://godoc.org/github.com/olivere/elastic)**。***

****步骤 5:打开从你的 Go 应用程序到 Elasticsearch** 的连接(创建客户端)**

**首先，我们在 go 应用程序中需要一个 ElasticSearch 客户端。**

**这是一个我们如何查询索引并获得自动完成结果的例子。**

**让我一个一个地破解代码:**

```
**searchSuggester := elastic.NewCompletionSuggester("data").Text(keyword).Field("suggest").Size(size)**
```

**这段代码将提供我们将在搜索查询中使用的完成建议。**

*   **字符串`data`是函数的名称**
*   **`Text(keyword)`我们要根据关键字作为前缀来查找哪个成员**
*   **`Field(“suggest”)`是具有完成类型的字段**
*   **`Size (size)`是我们希望获得的最大数据量**

```
**searchSource := elastic.NewSearchSource().Suggester(searchSuggester)** 
```

**这段代码将创建新的搜索源并使用我们的`searchSuggester.`**

```
**searchResult, err := client.Search().Index(indexName).Type(typeName).SearchSource(searchSource).Do(ctx)**
```

**这段代码将使用我们初始化的 ElasticSearch 客户端从我们的索引、类型和搜索源进行搜索。**

```
**for _, ops := range searchResult.Suggest["data"] {  
   for _, op := range ops.Options {   
      if op.Source == nil {
         continue   
      }   
      var member Member   
      err := json.Unmarshal(*op.Source, &member)   
      if err != nil {    
         log.Println(err)
         continue   
      }   
      members = append(members, member)  
   } 
}**
```

**这段代码用于从搜索结果中获取数据，并将结果添加到成员数组中。**

**我已经在我自己的 github 库([https://github.com/robertotambunan/knowsmore](https://github.com/robertotambunan/knowsmore))上完成了这个项目。在这个 repo 中，我已经创建了一个服务来处理来自 api 的自动完成请求。**

****结论****

**我想就这样了，伙计们。**

**Elasticsearch 是一个非常强大的搜索引擎，有很多功能，尤其是自动完成提示器，使我们能够轻松开发这个自动完成功能。**

**谢谢你的阅读，这是我第一次写作，所以不要犹豫，给任何投入。**

**祝您愉快！**

****参考****

*   **[https://www . elastic . co/guide/en/elastic search/reference/current/search-suggesters-completion . html](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters-completion.html)**
*   **[https://medium . com/@ adityarshnn/using-elastic search-autocomplete-with-go-client-completion-suggest er-eef 626167 aa 7](https://medium.com/@adityakrshnn/using-elasticsearch-autocomplete-with-go-client-completion-suggester-eef626167aa7)**