# 交互式画布的一些 API 已经更改

> 原文：<https://medium.com/google-developer-experts/some-apis-for-interactive-canvas-have-been-changed-8b099bb98eb2?source=collection_archive---------3----------------------->

在 Google I/O 2019 上，在一些关于 Google Assistant 的话题中，Google Assistant 支持智能显示被视为一个重大宣布。此外，为了使用 Smart Display 的一项功能“屏幕”构建操作，引入了交互式画布功能。通过使用该功能，操作可以为 Smart Display 提供一个 web 应用程序。

有关交互式画布的更多详细信息，请参考以下文档。

[交互式画布——对谷歌参考文档的操作](https://developers.google.com/actions/interactivecanvas/)

目前，它是一个开发者预览版，您还不能使用交互式画布发布您的操作。但是，您现在可以使用交互式画布来构建动作。我认为许多开发人员已经在挑战用交互式画布来构建动作。

我猜许多开发人员可能认识到一些 API 的命名没有一致性(我也是其中之一)。在几天前，为了使命名更加一致，一些 API 被修改了。

我在这里介绍这些变化。

# 将 ImmersiveResponse 更改为 HtmlResponse

为了从您的实现中发送对交互式画布的响应，到目前为止，必须使用沉浸式响应类。但是，我们需要使用 [HtmlResponse](https://actions-on-google.github.io/actions-on-google-nodejs/2.9.1-preview.1/classes/_service_actionssdk_conversation_response_html_.htmlresponse.html) 类来代替。用法与 ImmersiveResponse 类相同，创建一个 HtmlResponse 类的对象并将其传递给 conv.ask()方法。

```
const {
  dialogflow,
  HtmlResponse,
  ...
} = require('actions-on-google');const app = dialogflow();app.intent('Default Welcome Intent', conv => {
  conv.ask('...');
  conv.ask(new HtmlResponse({
    url: '...'
  }));
});
```

# 将状态更改为数据

在更改 HtmlResponse 类的同时，属性名从“state”更改为“ [data](https://actions-on-google.github.io/actions-on-google-nodejs/2.9.1-preview.1/classes/_service_actionssdk_conversation_response_html_.htmlresponse.html#data) ”。此属性用于将信息从您的实现传递到您的 Web 应用程序。

```
conv.ask(new HtmlResponse({
  data: {
    ...
  }
}));
```

# 更改 JavaScript SDK 文件 URL

JavaScript SDK 在 Web app 端是必要的，以支持交互式画布。JavaScript SDK 的 URL 已更改。

*   [https://www . gstatic . com/assistant/interactive canvas/API/interactive _ canvas . min . js](https://www.gstatic.com/assistant/interactivecanvas/api/interactive_canvas.min.js)

您需要将其加载到您的 HTML 文件中。

```
<!DOCTYPE html>
<html>
  <head>
    ...
    <script type="text/javascript" src="[https://www.gstatic.com/assistant/interactivecanvas/api/interactive_canvas.min.js](https://www.gstatic.com/assistant/interactivecanvas/api/interactive_canvas.min.js)"></script>
    ...
  </head>
  <body>
    ...
  </body>
</html>
```

# 将 assistantCanvas 改为 interactiveCanvas

对应 JavaScript SDK 变化，需要修改代码。提供了“assistantCanvas”对象，但是名称被更改为“ [interactiveCanvas](https://developers.google.com/actions/interactivecanvas/reference/interactivecanvas) ”。

```
interactiveCanvas.ready({
  onUpdate(data) {
    ...
  }
});interactiveCanvas.sendTextQuery(...);
```

由履行方将“状态”改为“数据”。同样，您最好将传递给 interactiveCanvas 对象的 ready()方法的回调函数的参数名称从“state”更改为“data”。

# 弃用 CSS 文件

在交互式画布的 web 应用程序中，为了显示动作名称，在屏幕的客户区顶部有一个标题区。标题区域的高度因设备而异。也就是说，动作可以使用的高度是整个客户区的高度减去标题区的高度后的剩余高度。并且，可用高度将动态变化。

到目前为止，CSS 文件被提供来将标题高度设置为 body 元素的 padding-top 样式属性值。但是，这个 CSS 文件已被弃用。

# 添加一个新的 API 来检索页眉高度

不使用 CSS 文件，而是提供了一个新的 API 来动态检索标题高度。开发人员可以通过调用 interactiveCanvas 对象的 [getHeaderHeightPx()](https://developers.google.com/actions/interactivecanvas/reference/interactivecanvas#getheaderheightpx) 方法来检索页眉高度。

```
const headerHeight = await interactiveCanvas.getHeaderHeightPx();
```

注意，getHeaderHeightPx()方法返回值的类型是 Promise <number>。您将需要使用 await 或 then()来调用 API。</number>

# 如何应用更改

为了反映现有动作的这些变化，有必要更新 Google 客户端库上的动作版本。

*   NodeJS —执行`npm install actions-on-google@preview`

执行上面的命令后，可以使用 HtmlResponse 这样的 API。

不幸的是，在我写这篇文章的时候，Google Client Library for Java 上的动作不支持交互式画布。