# 用 Symfony 3 中的 Fetch API 发出 JS 请求

> 原文：<https://medium.com/quick-code/making-a-js-request-with-the-fetch-api-in-symfony-3-21122a525f52?source=collection_archive---------3----------------------->

![](img/a1cdaf55c53f4d368a22de3482c60e0c.png)

Photo by [Ilya Pavlov](https://unsplash.com/photos/OqtafYT5kTw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在我正在做的一个学校项目中，我必须使用 JavaScript 的 Fetch API 在 Symfony 3 应用程序后端提供的一个文件中做一些 AJAX 请求。这不容易，但我赢了！

# 该项目

首先，一点点的背景。在我受教育期间，我必须在两年内与 6 名学生(包括我)一起从零开始创建一个项目。我们可以自由选择主题，但必须在选定的陪审团面前通过一系列陈述来验证，以检查该项目是否有潜力导致创业。

我们的项目叫做 **Playficient** 。多亏了统计数据，我们发现**平均损失了 40%的工作时间**。其中一个主要原因是从一种工具切换到另一种工具。为了**减少**工作中使用的工具数量，做了很多软件和工具(用 API 同步，用“脸书登录”，用 IFTTT，或者在 Slack 或者 Github 上创建 WebHoks)。

我们想为小公司和非常小的公司创造一个类似的工具，这些公司与计算机有很强的联系。但是我们想更进一步，用游戏化的原则来激励员工，同时用人工智能的聪明建议让用户在日常工作过程中变得更好。为了让这个变得更复杂一点，我们希望这个项目是可定制的，有一个模块化的系统，就像 Wordpress 或 Firefox 中的插件一样(潜意识文本:去 Firefox 57！)

(链接到一个展示网站，如果你想看一看，不要犹豫，给我们一些反馈，这将是令人敬畏的，如此亲切！=D)

# 嘿，你不是说了一篇技术文章吗？

的确，的确，是我谈论它的时候了。为了轻松管理模块化系统，我们在 PHP 中使用了一个名为 [Symfony 3](http://symfony.com) 的服务器端框架，它与 **Bundles** 一起工作，目前有 2 个人在这个后端工作。前端部分由 Symfony 通过**模板**和**资产**进行管理:

*   一个**模板**是一个带有[分支](https://twig.symfony.com/)语法的 HTML 页面，它是可重用的，并且可以通过变量替换和一些其他特性如循环和条件来定制。
*   一个**资产**是一个为模板提供内容的文件，比如 JavaScript 代码、图像、CSS 等等。

所以我们使用一个模板来创建**可重用的**页面，或者创建一个**特定的**页面到一个[路由](https://symfony.com/doc/current/routing.html)。但是除了 **HTML 代码**和 **Twig** 标签，我们在模板中找不到任何东西(如果你写了难看的代码，也找不到其他代码)。所以我们如果想写 JavaScript 代码，其实并不在乎模板。

另一方面，资产要有趣得多，因为多亏了它们，我们才能够为客户端提供 JavaScript 文件。但是这里我们有一个问题:我们**没有**从模板中分支语法来使用 **JavaScript** 中的服务器端变量。

当我们需要获取一个**路径的 URL** 时，这就成问题了。

# FOSJSBundle 来救我们了

如我所说，Symfony 使用捆绑包，就像 JavaScript 中的 npm 包或 Ruby 中的 Gems 一样。这些包被用作一个项目的依赖项，由于巨大的 Symfony 社区，我们可以找到数百个已经存在的包。

FOSJSBundle 是一个包，它给了我们一个 JavaScript 库，叫做`Routing`来获取一条路线的 URL。

下面是它的使用方法:

```
const url = Routing.generate('route_name', {your parameters}, absoluteUrlBool);
```

这个函数返回**对应于第一个参数中给出的路径的 URL** ，这正是我们所需要的！

现在，我们需要创建所谓的 AJAX 请求。让我们看一个用例(随机地，我今天正在做的)。我们是用户 X，**连接**到 Playficient 并拥有**任务管理器**模块。在这里我们有一个盒子列表，每个盒子都是一个**任务**，可以包含任意数量的**子任务**。我们想做一些子任务的“拖放”来改变它所属的盒子。我们这样做，我们需要告诉服务器，我们希望**将**任务从其先前的位置移除，而**将**任务添加到其新的位置。

为此，我们使用 som JavaScript 并创建一个 **AJAX** 请求。有多种方法可以解决这个问题，最新的方法是使用[获取 API](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API/Using_Fetch) 。Fetch API 使用两个对象和一个方法:

*   [请求](https://developer.mozilla.org/fr/docs/Web/API/Request):是定义请求的对象(简单吧？).我们在其中定义了 URL、参数、方法等。
*   [响应](https://developer.mozilla.org/fr/docs/Web/API/Response):这个对象定义了服务器的响应。当一个获取承诺被解决时，我们得到这个对象。
*   [fetch()](https://developer.mozilla.org/fr/docs/Web/API/GlobalFetch/fetch) :该方法将请求作为第一个参数。这个方法返回一个解析响应对象的[承诺](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Promise)。

现在您对它有了更多的了解，让我们来看一下与从原来的盒子中删除子任务的请求相对应的代码:

```
const url = Routing.generate('rask_remove', {
  taskId: 1,
  parentId: 2,`
});
const request = new Request(url, { method: 'GET' });fetch(request)
  .then(reponse => {
    console.log('We removed this task!');
  })
  .catch(error => {
    console.error(`Error when removing: ${error}`);
  });
```

好了，东西都齐了吗？让我们运行服务器并进行操作…下面是 Firefox 的控制台给我们提供的内容:

```
GET   http://localhost/task/2/1/remove        302 Found
GET   http://localhost/login                  200 Ok
'We removed this taks!'
```

当我们重新加载页面时……任务**仍然在这里！**

很快，我们可以看到有两个请求**完成了:一个是删除任务，另一个是登录页面…这很奇怪。我不会再让悬念持续下去了，也不会解释为什么会这样。**

我们仍然是用户 X(不是教授),并且已经连接，服务器需要在**中知道我们正在做这个请求的**的名字。这就是为什么在 Symfony 中使用 [cookies](https://en.wikipedia.org/wiki/HTTP_cookie) 的原因。从一个页面到另一个页面，用户带着这个 cookies**到处走，这个 cookies** 允许他/她保持连接，直到他/她点击“注销”。

因此，我们在请求中需要做的是**获取这个 cookie** 并发送它，但是如何做呢？事实上，这对于 Fetch API 和 Symfony 来说非常简单，因为这是一个简单的参数**放入 Fetch 方法**，以发送“凭证”:

```
fetch(request, { credentials: 'same-origin' })
```

让我们再次运行我们的代码:

```
GET   http://localhost/task/2/1/remove      200 OK
'We removed this task!'
```

我们的要求来了！

使用 JavaScript 和 Symfony 的 Fetch API，做所有这些事情是相当容易的，但前提是我们知道如何做。(我花了 3 天时间寻找如何从 Symfony 获得 JavaScript 中的路线，并理解这个凭证错误，尽管它实际上很简单！)

如果你愿意，这篇文章也有法语版本。我打算利用我在魁北克的逗留时间和我的毕业设计写更多关于技术学生的文章。顺便说一下，我正在开发一个来自 [CoralProject](https://coralproject.net/) 的 [Talk](https://coralproject.net/products/talk.html) 插件，当这个插件完成时，我可能会写一些关于它的东西；)

在那之前，祝大家愉快！

*本文首发于*[*【https://ilphrin.com】*](https://ilphrin.com) *如果你喜欢，请拍手告诉我你是否喜欢那种文章；)*