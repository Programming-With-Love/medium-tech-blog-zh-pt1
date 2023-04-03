# 使用 Visual Builder 的业务对象 REST API —第 2 部分:创建、更新和删除

> 原文：<https://medium.com/oracledevs/working-with-visual-builder-business-object-rest-apis-part-2-create-update-and-delete-84e16e3ee60b?source=collection_archive---------0----------------------->

在[第 1 部分](/oracledevs/working-with-visual-builder-business-object-rest-apis-b0be6aad52f3)中，我们讨论了访问 Visual Builder 业务对象 REST APIs。我们主要关注如何启用访问、在哪里找到端点和资源描述，以及使用 GET 请求读取业务对象。在下一部分中，我们将研究创建、修补(更新)和删除，这需要一些额外的设置。

由于 GET (read)是一个良性操作(意味着不创建或破坏数据)，基本身份验证足以提供访问。不过，一般来说，对于业务对象访问，基本身份验证是一种合理的方法(与 OAuth 相比)，因为这些类型的操作通常用于服务到服务的交互，而不是特定用户通过个人浏览器客户端上的用户界面与业务对象进行交互。这意味着用户名/密码通常也存储在后端，而不是客户端。

但是，由于创建、修补和删除会接触数据并具有潜在的破坏性，因此需要一个额外的要求来确保正确的服务(而不是来自跨站点脚本的劫持)发出请求。为了允许服务器使用这些函数，Visual Builder 需要知道请求的来源。首先，它必须被列入 Visual Builder 管理页面的白名单中，其次，每个请求都必须包含一个带有匹配 URL 或 IP 地址的源头。

如上所述，首先我们需要将希望进行创建、修补和删除调用的服务列入白名单。这可以在 Visual Builder 管理页面中找到，可以通过服务页面右上角的汉堡包图标访问该页面。

![](img/f7dde58fa8a93462aa49f9a07c2af14f.png)

在该管理页面中，您会发现一个允许的源设置窗格，您可以在其中添加新的 IP 或 URL。

![](img/a89b31401b02310b1ee9e112b6d60a59.png)

单击“新来源”按钮添加您的服务器 IP 或基本地址。

![](img/9d30635bb5b3bf082b2313f5ed5e0404.png)

现在，您可以通过向提供匹配 IP 或基址的 call 添加 Origin 头来发出 POST、PATCH 或 DELETE 请求。下面的 CREATE 调用示例 cURL 显示了源 IP。它还显示了将与 POST 和 PATCH 一起发送的 JSON 有效负载的一个新参数。出于安全目的，身份验证令牌用“-”隐藏。

```
curl -i -H "Authorization : Basic anQ-------------------1AQnIw" 
-H "Content-Type: application/vnd.oracle.adf.resourceitem+json" 
-H "Origin:http://my.server.com" 
-d @new_cust.json 
-X POST [https://instance-iddomain.builder.us.oraclecloud.com/design/BOsAtREST/1.0/resources/data/Customer](https://abcsdev-fsma.builder.us.oraclecloud.com/design/TestAppJT/1.0/resources/data/Customer)
```

你可能会注意到这个旋度中的一个新论点。-d 用于指定数据文件。new_cust.json 位于我发出 cURL 请求的本地目录中。json 有效负载包含新客户条目的名称:值对。下面是这个业务对象的 JSON 主体示例。

```
{ "name": "New Company"
     "state": "NY}
```

对于更新，cURL 看起来几乎相同，除了它使用 PATCH 命令而不是 POST，并且只有将要更新的字段应该在 JSON 有效负载中。最后，您必须使用 URL 中的 ID(表示主键)来指定哪个客户。

```
curl -i -H "Authorization : Basic anQ-------------------1AQnIw" 
-H "Content-Type: application/vnd.oracle.adf.resourceitem+json" 
-H "Origin:http://my.server.com" 
-d @new_cust.json 
-X PATCH [https://instance-iddomain.builder.us.oraclecloud.com/design/BOsAtREST/1.0/resources/data/Customer](https://abcsdev-fsma.builder.us.oraclecloud.com/design/TestAppJT/1.0/resources/data/Customer)/1
```

对于 DELETE，不需要有效负载，但是您必须通过在资源端点中包含主键来再次指定将删除哪一行。

```
curl -i -H "Authorization : Basic anQ-------------------1AQnIw" 
-H "Content-Type: application/vnd.oracle.adf.resourceitem+json" 
-H "Origin:http://my.server.com" 
-X DELETE [https://instance-iddomain.builder.us.oraclecloud.com/design/BOsAtREST/1.0/resources/data/Customer](https://abcsdev-fsma.builder.us.oraclecloud.com/design/TestAppJT/1.0/resources/data/Customer)/1
```