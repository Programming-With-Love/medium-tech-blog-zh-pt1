# 如何学习 React #12:为什么数组不好，替代方案是什么

> 原文：<https://medium.com/quick-code/how-to-learn-react-12-why-arrays-are-bad-and-what-is-the-alternative-7edf82bd3ecd?source=collection_archive---------1----------------------->

![](img/f5517691bdab277fef6b001121452732.png)

Photo by [Daniel Cheung](https://unsplash.com/photos/cPF2nlWcMY4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/another-way?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

你好，欢迎来到本系列的下一章，它将帮助你理解 React 是什么，以及如何使用它来编写令人敬畏的 web 应用程序。正如我在[上一章](/quick-code/how-to-learn-react-11-the-simple-formula-to-understand-react-form-ca5e0869910b)中承诺的，我们今天将为我们的应用添加新功能。我们将为用户添加点击食物的能力。在他点击之后，这顿饭会被删除，用户会看到他已经吃了点击的饭。似乎是个简单的工作。但是为了做到这一点，我们必须改变我们存储数据的方式。所以让我们开始吧。首先我要谈谈我们的数据会是什么样子。

先说上面的代码。首先是我们现在使用的膳食数据结构。到目前为止，当我们只需要存储餐名时，这就足够了。但是现在我们还需要存储用餐是否完成的信息。所以这种方法对我们不再适用。

现在我们可以用对象数组代替字符串数组。但是这种方法的问题是，每次我们想要修改数组时，我们都必须对它进行迭代。第三种方法是使用 object 存储带有键、值对的膳食，其中键是膳食的名称，值是关于膳食的信息。我喜欢这种方法的原因是，当我们想要更改数据时，我们不必进行迭代。如果你仍然犹豫不决，不要担心。您将在实施过程中看到好处。我们将改变我们的数据结构，这意味着我们必须做一些重构。首先…让我们改变`MealPlanItem`

这里有几个变化:

*   将`props.meal`改名为`props.title`，用`<span>`包裹。它必须被包装，所以我们可以添加`onClick`方法，只有当我们点击文本时才会被触发。我们不能把`onClick`加到`<li>`上，因为它本身就有`<button>`。我希望你明白我的意思
*   增加了`props.handleMealClick`。现在还不用担心，但是它会以和`props.handleDelete`一样的方式工作
*   增加了 JSX，只有当`props.completed`为假时才显示删除按钮。我看不出删除已经吃过的饭有什么意义。

好了，这就是`MealPlanItem`组件。现在真正的工作开始了。在`MealPlan`中，我们必须做很多改变，所以让我们一个一个来。首先我们要改变`componentWillMount`方法。我们用这个用虚拟数据填充组件。因此，显然我们必须改变它，以我们想要的格式为我们提供虚拟数据。

现在要换`renderMeals`。我们将使用 lodash 库的`map`函数。我们用它来检查我们的对象`meals`。它从对象中获取`key`和`value`。键将是饭菜的名称，值将是具有 1 个属性`completed`的对象，该属性可以是真或假。`renderMeals`的最终形式将类似于下面的代码。我们还添加了`handleMealClick`属性。我们还没有实现`completeMeal`函数，所以我们将马上实现它。这就是`renderMeals`的样子

现在我们来思考一下`completeMeal`函数应该做什么。它应该检查这顿饭是否还没有完成。如果用餐未完成，则将它的`completed`属性更改为`true`。当然还有更新状态。它看起来会像这样

现在我们需要改变`removeMeal`功能。在这里，我们只需要改变数据处理的方式。使用以前的数据结构，我们必须过滤数组，然后返回新的数组。现在，我们只需从对象中删除饭菜，就像下面的代码一样简单。我感觉你已经喜欢我们在数据结构上的改变了…

现在我们只需要做很少的改变。当我们初始化状态时，我们告诉`state.meals`是数组，但我们希望它是对象。状态初始化将如下所示

```
state = {
  meal: '',
  meals: {},
  showMessage: ''
};
```

接下来…我们有`componentDidUpdate`函数，我们正在检查那里的状态是否改变了。让我们继续删除它。我们不会再那样做了。相反，当我们提交食物时，我们将调用`showMessage`。最后一个变化将发生在`handleSubmit`。我们正在检查这顿饭是否已经在单子上了。因为我们改变了数据结构，所以我们只需要改变执行检查的方式。所以现在我们只需检查`meals`中的对象是否是名为`this.state.meal`的财产。如果不是，我们就创造它。现在我们修改了`meals`对象，我们只需要改变这个新对象的状态。我们还将称之为`showMessage`添加了新餐。

应该就是这样了。现在整个`MealPlan`将看起来像这样

好吧，我们今天做了很多工作。我们仍然没有达到我们想要的。当餐标为已完成时，它应该有一行。别担心，我们将在下一章讨论这个问题。但是现在我们能够追踪食物是否完成。

不要害羞…为自己鼓掌，如果你喜欢这个故事，你也可以为这个故事鼓掌，让世界知道你的成就。如果你没有读过这个系列之前的故事，一定要在这里查看一下。如果你有什么意见，欢迎在评论区提问。我很乐意回答你的任何问题。请继续关注下一个故事，我们将结束今天开始的内容，讨论如何在 react 应用程序中使用 css。那里见！