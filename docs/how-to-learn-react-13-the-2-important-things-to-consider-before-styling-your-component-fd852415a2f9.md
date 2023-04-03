# 如何学习 React #13:在设计组件样式之前需要考虑的两件重要事情

> 原文：<https://medium.com/quick-code/how-to-learn-react-13-the-2-important-things-to-consider-before-styling-your-component-fd852415a2f9?source=collection_archive---------0----------------------->

![](img/9ff80db0f5c134072e2c8d6637792948.png)

Photo by [Greg Rakozy](https://unsplash.com/photos/vw3Ahg4x1tY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/css?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

你好，欢迎来到本系列的下一章，它将帮助你理解 React 是什么，以及如何使用它来编写令人敬畏的 web 应用程序。正如我在[前一章](/quick-code/how-to-learn-react-12-why-arrays-are-bad-and-what-is-the-alternative-7edf82bd3ecd)中所承诺的，我们将会完成餐点标题。我们将通过 css 属性`text-decoration: line-through`来实现这一点。好的，首先让我们来谈谈如何在我们的应用程序中添加样式。第一种方法是使用内嵌样式

## 使用内联样式设置组件样式

正如我们所知，每个 html 元素都有`style`属性，我们可以在其中指定 css 属性来使元素看起来更好。现在棘手的是，在 React 中，我们需要将 Javascript 对象传递给样式属性。这个对象需要是`key`，`value`对对象，其中`key`是写在 **camel case** 中的 css 属性，`value`是 css 属性的值。现在，假设您想要将这两种样式添加到元素中:

1.  *页边距-底部:14px*
2.  *顶部填充:32px*

我知道我知道…你的第一个想法是为什么我会做那样可怕的事情。这只是一个例子。您的 javascript 对象将如下所示

```
{
  marginBottom: "14px",
  paddingTop: "32px"
}
```

或者这样……当值是数字时，你只需把它作为数字传递

```
{
  marginBottom: 14,
  paddingTop: 32
}
```

好了，我们已经定义了我们的风格。我们可以将它存储到变量中，然后按照下面的方式将变量传递给元素。

或者我们根本不用存储它，就这样传递它

这是一种方法。这种方法的优点是您不必创建单独的样式表。缺点是它看起来不太好，而且在很多情况下你需要使用更复杂的样式。这可能会导致你的文件因为样式而变大。同样，在这种方法中，你不能使用像`span:hover`这样的伪元素。现在让我们尝试使用 CSS 模块的不同方法。

## 带有 CSS 模块的样式组件

我猜你已经预见到了。是的，我们可以创建单独的文件，并将其导入我们的组件。这真的很简单，所以让我们开始做吧。我们需要创建新文件。让我们称它与组件相同，但带有扩展名 ***。css***

这只是普通的 CSS 文件。我将我们之前使用的属性分配给了类`meal-title`。现在我们将这个类添加到我们想要的元素中。首先，我们需要将 CSS 文件导入到组件中。他们分配类，我们的代码看起来像这样

注意，我们没有使用`class`属性，而是使用了`className`。我不想打扰你为什么会这样，但我只是告诉你，它最终会成为`attribute`类，当你在浏览器上查看时，你会看到的。这种方法的优点是你可以在 CSS 模块中分离样式。这样每个组件都可以有自己的 CSS 模块。你也可以使用伪元素。

## 现在终于完成我们的应用程序

好了，我们知道如何使用 css，我们终于可以完成我们的应用程序的功能。在`meal-striked`的`MealPlanItem.css`中添加值为`line-through`的`text-decoration`属性。

在`MealPlanItem.js`中，我们将告诉这个…当用餐结束时，我们想使用这个`meal-striked` `className`作为`<span>`元素，它最终保存用餐标题。如果不是，就不要使用这个类。它看起来会像这样

再次提醒一下下面的语法是如何工作的

`props.completed && ‘meal-striked’`

如果`&&`之前的表达式被求值为`true`，则返回`&&`之后的内容。应该就是这样了。让我们继续使用这个应用程序。现在它如我们所愿地工作了。我们做到了…

不要害羞…为自己鼓掌，如果你喜欢这个故事，你也可以为这个故事鼓掌，让世界知道你的成就。如果你没有读过这个系列之前的故事，一定要在这里查看一下。如果你有什么意见，欢迎在评论区提问。我很乐意回答你的任何问题。请继续关注下一个故事，我们将把外部 CSS 库导入到我们的应用程序中，并给它添加一些“外观”。在那之前，祝你愉快，享受…干杯！