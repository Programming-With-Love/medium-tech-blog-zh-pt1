# 使用节点。作为 Web 服务器的 JS

> 原文：<https://medium.easyread.co/http-server-with-node-js-5e0d8df901b4?source=collection_archive---------0----------------------->

![](img/9d5f8a85ff31ed641e36301e0adeee66.png)

Photo by [Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 节点中带有核心 HTTP 的 HTTP 服务器。射流研究…

> 在本文中，我们将讨论如何使用节点。JS API 来处理基本的服务器功能，比如 GET 和 POST。编写服务器代码的一个更简单的方法是通过一个叫做 **express** 的框架(更新:我在这里使用了[express framework*)。当我们掌握了处理基本 web 服务器功能的核心思想后，我将在接下来的文章中写相关的教程*](https://medium.com/easyread/introduction-to-express-js-246191ec05f2)

要在 Node JS 引擎中创建一个带有 HTTP 模块的 web 服务器，我们需要使用下面的方法:

```
**http.createServer(callback)**
```

> 这里，*回调*将接受两个参数；一个**请求**和一个**响应。****请求**参数将数据从客户端发送到服务器，以便数据可以被处理，之后**响应**从服务器发送到客户端。

最基本的例子可以是:

[Source](https://gist.github.com/HussainArif12/14abf0cf1b31fb8edb214b6e3c3a531b)

代码甚至可以进一步缩短。可以将 *res.write()和 res.end()* 替换为:

```
**res.end('Hello World')** //Write response AND then end it 
```

通过命令行运行代码。如果你没有在终端上打印任何字，那是正常的。您应该可以在浏览器中输入:`**localhost:3000**`来得到结果

屏幕上会打印出“你好，世界”字样

# 指定标题

response.writeHead()向请求发送头响应。或者用于设置状态代码和创建 HTTP 头。

如果您想指定要在 HTML 中显示的服务器响应。你需要告诉头部**内容类型应该是 HTML**

```
http.createServer((req,res)=> {
**res.writeHead(200,{'Content-Type':'text/html',
'Content-Length':body.length})**
..
```

*res.writeHead()* 的第一个参数是**响应码**。可以是 **2xx** 、 **3xx** 、 **4xx、**或 **5xx。**

第二个参数是**选项。**在这里，我们指定内容类型为 HTML，即我们的响应将以 HTML 格式显示，并且内容的长度将等于您的正文长度。

您甚至可以使用*响应参数*属性来更改标题:

```
**response.writeHead = 400**
```

# HTTP 服务器请求

> *http.createServer* 方法的 *req* 参数具有关于传入请求的所有信息，例如请求有效负载、头、URL 等。

`***req***`参数具有以下属性

*   `***request.headers***`:关于请求头的信息，如连接、主机、授权等。
*   `***request.method***`:关于传入请求方法的信息，如 GET、POST、PUT、DELETE、OPTIONS、HEAD 等。
*   `***request.url***`:传入请求 URL 的信息，如`**/accounts, /users, /messages**`等

## 例子

[Source](https://gist.github.com/HussainArif12/0906ff8ef80dd77d0018833590595326)

# 在服务器中处理传入的请求正文

假设客户端发送了数据，我们如何处理它？

## 例子

[Source](https://gist.github.com/HussainArif12/1c8664cb262d0e3569aa00cf98aa7b89)

执行这段代码需要一种稍微不同的方式:

如果你使用的是 Windows，首先你需要安装一个名为 [cURL](https://curl.haxx.se/windows/) 的工具，然后通过命令行运行代码。

要发送数据，请打开另一个命令行窗口，并写入:

```
**curl -x POST -d “key-value” localhost:3000**
```

cURL 的另一个替代品是 [Postman](https://www.postman.com/product/api-client)

在之前的命令行窗口中，您会发现通过 cURL 发送的数据。在这种情况下，是`**key-value**`

# 更多资源

*   [如何启动节点服务器](https://stackabuse.com/how-to-start-a-node-server-examples-with-the-most-popular-frameworks/)
*   [创建 Node.js Web 服务器](https://www.tutorialsteacher.com/nodejs/create-nodejs-web-server)

在下一篇文章中，我们将使用 Express 来使我们的请求处理变得更加容易，并且在这个过程中有更短的代码行。

今天到此为止。谢谢你坚持到最后！

呆在家里，注意安全！