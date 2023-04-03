# 如何学习 React #14:最后一章和下一步的方向

> 原文：<https://medium.com/quick-code/how-to-learn-react-14-the-last-chapter-and-where-to-go-next-12b1649b32a5?source=collection_archive---------0----------------------->

![](img/cc9f847ea2784be100dfa7b7ffe68632.png)

Photo by [Kelen Loewen](https://unsplash.com/photos/r1jLPh5rrnY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/last?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

欢迎来到本系列的最后一章。我们走了相当长的一段路。我们学习了如何使用 React 创建简单的应用程序。在这一章中，我们将学习如何使用外部 CSS 库。并为我们的应用程序增添一些亮点。我们将使用 [materialize-css](http://materializecss.com/) 。所以让我们开始吧

在项目目录中运行

`npm install --save materialize-css`

这将下载库。现在我们需要将它链接到我们的应用程序。在`App.js` 文件的开头添加 2 个导入。我们需要导入 CSS 文件

`import “materialize-css/dist/css/materialize.min.css”`

和 JS 文件

`import “materialize-css/dist/js/materialize.min.js”`

现在剩下的就是遵循物化的文档。寻找我们想要使用的组件，并更改我们的应用程序。我不会重复我想象的所有变化，因为大部分只是复制粘贴。相反，我将提供到[示例](https://github.com/codewithbernard/medium-example-app)的链接。有我们 app 的全部代码。随便看看吧。请随意下载并在本地运行它。所有的说明都在项目`README`文件中。

这就是这个系列的基本内容。我知道…你可能会问。就这样？答案当然不是。还有很多东西要学。我会写更多关于它的教程。但是结构会有一点变化。每篇帖子都会比较复杂。这将从零开始。它将以一个工作示例应用程序结束。内容也会改变。当然，重点仍然是反应。但我将展示如何将它与其他库集成，如 Redux、Firebase、后端 API 等

所以请继续关注接下来的教程。这个系列结束了。光明的未来正等待着我们。下次见。