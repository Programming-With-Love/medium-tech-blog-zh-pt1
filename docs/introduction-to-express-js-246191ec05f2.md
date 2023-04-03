# 使用快递。作为 Web 服务器的 JS

> 原文：<https://medium.easyread.co/introduction-to-express-js-246191ec05f2?source=collection_archive---------0----------------------->

![](img/60fa27f48594d6c0774b6634110cd325.png)

> 在本文中，我们将了解 Express。JS 用于处理 POST 和 GET 请求，以及为什么它比节点的`**http**`核心模块更合适。JS 引擎。

# Express 是什么，是什么让它如此伟大？

快车是最受欢迎的节点。JS 框架来处理特定 URL 上的多个不同的 HTTP 请求。此外，它是最小的、开源的、灵活的，这有助于开发者投入更少的精力和时间来开发更好的网站和应用。

像优步和推特这样的科技巨头都在使用它，这肯定是一个令人惊奇的工具。它增加了非常简单的路由，并从本质上缩短了处理 web 请求所需的代码行。

## 让我举一个例子:

重用我上一篇文章中的代码:[使用 Node。JS 作为 Web 服务器](https://medium.com/easyread/http-server-with-node-js-5e0d8df901b4)，这里有一个在 NodeJS 中使用`**http**`模块的例子

现在，让我们使用 Express API 重写这段代码:

Code for ‘Hello world’ in Express. Explained in the next part

# 入门指南

通过 NPM 安装。这是一个简单的步骤。为了获得更多关于 NPM 的帮助，我在这里写了一个[指南](https://medium.com/@hussainarifkl/the-basics-of-npm-a32ee1d79901)

```
**npm i express**
```

你现在可以走了！

# 简单的例子

让我们在 Express 中编写一个 GET 方法来显示单词“`**Hello World**`”。重用上面示例中的代码:

通过命令行`**node express-basic-hello-world**`运行代码，然后在另一个命令行上键入:

```
**curl localhost:3000**
```

`**app.get()**`的第一个参数是*路线。*
第二个参数是回调，正如前面标题
`**app.listen**`中解释的，告诉我们它将响应的端口号。

> 路由是什么？ ***路由*** 指的是应用程序的端点(URIs)如何响应客户端请求

**如果**客户端位于 URL 或路径`**‘/’**`(换句话说，根主页)，**然后**执行回调中的代码块。相当容易！

# 处理其他路线

你甚至可以这样处理其他路线:

假设客户端在根主页(`**/**`)，那么单词`**`at root`**`将被打印出来。否则，如果客户端位于名为`**hussain**`的 URL，那么文字`**at URL named: Hussain**`将被打印出来。

用命令`**node express-routing-example**`运行代码

然后在另一个命令行上写下:
`**curl localhost:3000**`这里，文字`**`at root`**`将被打印
`**curl localhost:3000/hussain**`这里，文字‘at URL named:Hussain’将被打印。`**‘/’**`后的文字是自变量

# 发布请求

您还可以处理其他请求，如 POST、PUT 等。为此，只需用参数完全相同的`**app.post**`或`**app.put**`替换`**app.get**`

要处理发布请求:

> 注意:`**app.use()**`是一个中间件函数，将在下一篇文章中讨论。

通过命令行执行代码，然后，通过 cURL 发送数据，打开另一个终端窗口写入:

`**curl -d 'Hello World' localhost:3000/ -H 'Content-Type: text/plain'**`

```
curl -d **'Hello World at hussain'** localhost:3000/**hussain** -H 'Content-Type:text/plain' 
```

回到您的服务器终端(之前没有使用 cURL 函数的终端),注意您发送的数据已经打印在窗口中。

> 注意:`**-H**`用于指定头部，告诉 Express 我们发送的数据将是纯文本。Express 对格式非常挑剔，这就是为什么我们必须在发送数据之前指定报头

# 配置快速

您可以随时更改 Express 中的设置。有很多配置。其中一些是

```
**app.set(‘port’, process.env.PORT || 3000)** //change default port
**app.set(‘views’, ‘templates’)** // The directory the templates
**app.set(‘view engine’, ‘jade’)** //set view engine to JADE
```

# 快速应用生成器

`**express-generator**`模块用于为您的网站获取模板。这样做是为了让你——开发者——可以花更少的时间来开发应用程序的设计，因为你已经有了网站的“框架”。
您可以开始快速开发，因为生成器将为不同的模板引擎和库创建文件和文件夹。

这样，您可以花更多的时间来适当地开发后端逻辑。

## 入门指南

运行命令安装模块:

```
**npx express-generator**
```

## 创建项目

既然我们已经安装了模块

运行命令:

```
**express myProject** #create a project with name of myProject
**cd myProject** #change working directory to myProject
**npm install** #install all required dependencies
**npm start**
```

我们完了！

在您的 Express 项目目录中工作，您可能已经注意到了更多的子目录和文件。这里有一个解释

*   主文件，包含嵌入式服务器和应用程序逻辑
*   `**/public**`:包含由嵌入式服务器提供服务的静态文件
*   `**/routes**`:为 REST API 服务器提供定制路由(web 应用程序不需要)
*   `**/routes/users.js**`:用户等资源的端点/路由
*   `**/views**`:包含模板引擎可以处理的模板(REST APIs 不需要)
*   `**/package.json**`:项目清单
*   `**/www**`:启动脚本文件夹

> 注意:web 应用程序是一个传统的 web 应用程序(胖服务器),具有 100%的服务器端渲染，而 REST API 是一个纯数据服务器(渲染发生在客户端，UI 托管在客户端)

# 摘要

## 要安装 express

```
npm i express
```

## 要处理请求:

```
app.METHOD(route, handler)
```

在哪里

*   `METHOD`可以是`put`、`get`、`post`或`delete`
*   `route`将是你的道路
*   `handler`将是处理该路由时执行的回调函数。

## 要配置:

```
app.set(key,itsValueToChange)
```

# 外部资源

*   [创建一个框架网站——Mozilla](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website)
*   [快递基本路线](https://expressjs.com/en/starter/basic-routing.html)
*   [快速入门。JS](https://www.creativebloq.com/how-to/get-started-with-expressjs)

在接下来的文章中，我们将更深入地去表达和它的工作方式。今天到此为止。非常感谢您的阅读！

呆在家里，注意安全！