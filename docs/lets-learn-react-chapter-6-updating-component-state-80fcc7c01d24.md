# 如何学习 React # 6——关于组件状态你需要知道的一件事

> 原文：<https://medium.com/quick-code/lets-learn-react-chapter-6-updating-component-state-80fcc7c01d24?source=collection_archive---------1----------------------->

![](img/169ddc681d5c7de65d9ffe77087409fc.png)

大家好，欢迎来到 React 学习系列的下一章。在[之前的](/quick-code/lets-learn-react-chapter-5-component-state-f9eccc8df2cf)章节中，我们创建了 *MealPlan* 组件，在那里我们开始围绕组件状态做一些工作。我们最终得到了功能齐全的组件。只是为了唤起你的记忆…这个

所以当我们点击按钮添加膳食时，我们希望这段代码执行

```
this.setState({meals: [...this.state.meals, this.state.meal]})
```

我现在的目标是谈一谈 *setState* 函数和调用它时发生的动作链。为了回答这个问题，这个函数在做什么。它更新我们的组件状态。它需要一个参数，即由一个或多个键值对组成的对象。当您向上滚动并查看我们定义组件状态的位置时，您会看到它也只是键、值对的对象。因此 *setState* 只接受我们传递给它的对象，并用给定值替换给定键上的当前组件状态。**这很重要—** 当我们调用 *setState* 时，我们总是要为给定的键指定新的值。它必须是**精确的** **值**。当提供新值时这将导致组件状态的改变。当状态改变时会发生什么？组件被重新呈现。瞧啊。整个流程开始了。**这也很重要—** 更新组件状态的唯一方法是调用 *setState* 。

我在前一章中承诺，我将揭开为什么我们不能只是打电话的秘密

```
this.setState({meals: this.state.meals.push(this.state.meal)})
```

而不是我们真正的电话

```
this.setState({meals: [...this.state.meals, this.state.meal]})
```

如果你已经知道了答案，我要祝贺你，你走在了正确的道路上。当我开始做这个的时候，我花了很长时间才弄明白。所以正如我提到的，改变组件状态的唯一方法是调用 *setState* 。我们不能做`this.state.meals.push(this.state.meal)`，因为在这种情况下我们没有调用 *setState* 。在这种情况下，push 会尝试直接修改我们的膳食，而不调用 *setState* 。而我们也做不到

`this.setState({meals: this.state.meals.push(this.state.meal)})`

因为在这种情况下，我们没有为餐食指定新的价值。是的，那是正确的。数组方法 *push* 没有返回新的更新数组。它只是更新它。所以你可能会问，如果我们这样称呼它，新的状态值会是什么。它将是*餐*数组的长度，也就是数字。所以这不是我们真正想要的。好吧，我希望我们能达成共识。我想澄清的最后一件事是这是什么

```
[...this.state.meals, this.state.meal]
```

有一个变化你以前没有看到这个语法。调用上面的代码，我们告诉它我们正在返回新的数组。有两个要素。第一个是来自*膳食*数组(`…this.state.meals`)的所有元素，第二个是输入中的当前膳食。当我们组合所有旧的膳食值和新的膳食值时，我们得到了所有膳食的新数组，并且我们还满足了对 *setState* 的要求，其中我们应该始终为给定的键提供新的状态值。

好吧，很抱歉在这一章中没有实际编码，但我真的想确保我们在继续之前理解了关于状态的整个循环。是的，我保证我们会在下一部中编码。今天就到这里吧。请继续关注下一章，我们将讨论组件生命周期方法。