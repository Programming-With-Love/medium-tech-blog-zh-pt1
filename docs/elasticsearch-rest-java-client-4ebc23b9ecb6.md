# Elasticsearch REST Java 客户端

> 原文：<https://medium.com/globant/elasticsearch-rest-java-client-4ebc23b9ecb6?source=collection_archive---------0----------------------->

Elasticsearch 是一个基于文档的数据库和基于 Lucene 库的搜索引擎。它还提供了许多功能，如模糊搜索，聚合，排序，点击等。Elasticsearch 是分布式的，索引可以被分成碎片，每个碎片都可以有副本。

在这篇博客中，我们将讨论以下几点

*   使用 java 连接到 Elasticsearch 的选项
*   与微服务集成
*   记录 API
*   同步和异步调用

**使用 java 连接到 Elasticsearch 的选项**

**1。** **运输客户端:**

在引入用于弹性搜索的 Java 客户端之前，使用传输客户端。这使用 HTTP 上的 REST API。在这种情况下，用户必须付出额外的努力来编组请求和解组响应。

**2。** **休息低级客户端**:

RestLowLevelClient 在内部使用 Apache HTTP 异步客户端。它使用最少数量的依赖项。这还会给用户带来额外的工作来编组和解组请求和响应。这将提供以下功能。

负载平衡

故障切换

持久连接

跟踪日志记录请求和响应

最小依赖性

**3。** **Rest 高级客户端:**

RestHighLevelClient 构建在 RestLowLevelClient 之上。这增加了一些弹性搜索依赖，然后是低级客户端。它公开了负责获取请求对象和返回响应对象的 API 特定方法。高级客户端负责编组请求和解组响应，这使我们的编码更容易。这是弹性搜索的默认客户端。

**与微服务的集成**

1.  **Maven 依赖关系**

第一步是将 maven 依赖项添加到 pom.xml，如下所述。

```
<dependency>
  <groupId>org.elasticsearch.client</groupId>
  <artifactId>elasticsearch-rest-high-level-client</artifactId>
  <version>7.14.0</version>
</dependency>
```

我们需要 java 1.8 和更高版本来支持这种依赖性。参考[此处](https://mvnrepository.com/artifact/org.elasticsearch.client/elasticsearch-rest-high-level-client)了解最新版本的弹性搜索。

**2。初始化 RestHighLevelClient:**

```
RestHighLevelClient client = new RestHighLevelClient(
       RestClient.builder(new HttpHost("localhost", 9200, "http"),
                          new HttpHost("localhost", 9201, "http")));
```

这将创建一个 RestHighLevelClient 实例，该实例将在内部创建一些低级客户端的实例。高级客户端将维护一些低级客户端连接的池。一旦使用完高级客户端，不要忘记使用 client.close()关闭它。这将释放低级客户端连接。使用 close 方法关闭连接。

**记录 API 的**

1.  **索引一个文档**:

我们需要创建一个如下的 IndexRequest。

```
Map<String, Object> jsonMap = new HashMap<>();
jsonMap.put("user", "kimchy");
jsonMap.put("postDate", new Date());
jsonMap.put("message", "trying out Elasticsearch");
IndexRequest indexRequest = new IndexRequest("myIndex")
                             .id("1").source(jsonMap);
```

这里‘myIndex’是索引名，而‘1’是文档的 id。我们也可以传递 json 字符串来代替 map，如下所示。

```
String jsonString = "{" +
   "\"user\":\"kimchy\"," +
   "\"postDate\":\"2013-01-30\"," +
   "\"message\":\"trying out Elasticsearch\"" +
   "}";
request.source(jsonString, XContentType.JSON);
```

按如下方式执行请求。

```
IndexResponse indexResponse = client.index(request,
     RequestOptions.DEFAULT);
```

在执行请求时，程序将暂停，直到弹性搜索没有返回 IndexResponse。

**2)** **获取一个文档:**

如下创建 Get 请求。其中“myIndex”是索引名称，“1”是文档 id。

```
GetRequest getRequest = new GetRequest(  "myIndex",   "1");
```

要执行请求，请使用客户端。

```
GetResponse getResponse = client.get(getRequest,  
    RequestOptions.DEFAULT);
```

您可以使用以下 GetResponse 方法获取该文档

-getresponse . getsourceasstring():-这将以字符串形式返回文档。

-getresponse . getsourceasmap():-这将返回一张<string object="">的地图</string>

-getresponse . getsourceasbytes():-这将返回一个字节数组。

**3)**删除一个文档:

使用“myIndex”作为索引，使用“1”作为文档 id，可以如下构造 DeleteRequest。

```
DeleteRequest request = new DeleteRequest( "myIndex", "1");
```

使用我们的高级客户端来执行请求。

```
DeleteResponse deleteResponse = client.delete(request, 
      RequestOptions.DEFAULT);
```

**4)**更新文档:

UpdateRequest 构建如下。

```
UpdateRequest request = new UpdateRequest( "myIndex", "1");
```

这里“myIndex”是索引,“1”是需要更新的文档的 id。创建一个更新字段的映射，并将其设置在 doc 下，如下所示。

```
Map<String, Object> jsonMap = new HashMap<>();
jsonMap.put("updated", new Date());
jsonMap.put("reason", "daily update");
UpdateRequest request = new UpdateRequest("myIndex", "1")  
   .doc(jsonMap);
```

按如下方式执行请求。

```
UpdateResponse updateResponse = client.update(request, 
     RequestOptions.DEFAULT);
```

**同步和异步调用**

上面提到的所有创建、更新、删除和 get 调用都是异步调用。高级客户端也为它们中的每一个提供同步调用方法。我们只需要向同步调用方法写入后缀异步字，例如:client.update()是同步方法，而 as client.updateAsync()是异步方法。

我们还必须添加一个 ActionListener 来获得响应。执行异步调用的代码片段如下。

```
ActionListener listener = new ActionListener<UpdateResponse>() {
    @Override
    public void onResponse(UpdateResponse updateResponse) {

    }

    @Override
    public void onFailure(Exception e) {

    }
};client.updateAsync(request, RequestOptions.DEFAULT, listener);
```

在这里，我们必须创建一个侦听器，并将引用传递给 updateAsync 调用。每当调用执行完成时，就触发 onResponse 或 onFailure 方法。

**结论**

在本文中，我们看到了如何使用 Java 客户端来执行一些常见的 Elasticsearch 特性。