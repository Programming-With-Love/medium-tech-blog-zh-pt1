# 如何学习反应 2——关于 JSX 你需要知道的一件事

> 原文：<https://medium.com/quick-code/lets-learn-react-chapter-2-what-the-hell-is-jsx-and-where-can-i-buy-it-5303a7db5117?source=collection_archive---------2----------------------->

![](img/169ddc681d5c7de65d9ffe77087409fc.png)

在前一章中，我们做了一些设置和一点编码。在本章中，我们将更深入地了解我们实际做了什么以及在哪里做的。事不宜迟，让我们先来揭开 JSX 这个术语的神秘面纱。我认为 JSX 是 HTML，但有两个关键能力。第一，能够创建自己的标签，然后使用它们。第二，在我的 HTML 中编写 javascript 代码。是的，我知道…相当愚蠢的定义，但它对我有效，相信我，当我们在本章中进一步研究它时，它对你也有效。

## 看一看渲染方法

```
import React, { Component } from 'react';class App extends Component {
  render() {
    return (
      <div>Hello World</div>
    );
  }
}export default App;
```

在上面的代码中，我们有

```
class App extends Component
```

通过这段代码，我们定义了新的 React 组件。你问的反应元件是什么？它代表您的 web 应用程序的一部分。假设您的 web 应用程序有页眉、页脚和内容。在 React 中，我们可以说我们有 3 个组件。页眉、页脚和内容组件。我们将定义多少组件完全取决于我们自己。我可以在这里向您介绍组件的官方文档，但是我相信您已经明白了。如果没有…你会明白的。

每个组件都必须实现*渲染*方法，在这个方法中我们告诉组件应该如何渲染。在我们的例子中，我们只有一个组件。它叫做 *App* ，代表了我们的整个应用。我们希望我们的应用程序说“Hello World ”,所以我们在 *render* 方法中定义了它。这是一个非常虚拟的组件，这次它真的没做什么。让我们把它提高一个档次。比方说，如果今天是工作日，我们希望我们的应用程序说“你今天应该去工作”，如果是周末，它会说“你今天可以整天看电视”。我继续改变我们的组件，添加注释，需要做什么，使它像我们想要的那样工作。很明显，我们实际上将在 *render* 方法中做一些逻辑，并基于此决定返回什么。

```
import React, { Component } from 'react';class App extends Component {
  render() {
    // Get current date
    // If it is weekday
    return <div>You should go to work today</div>;
    // If it is weekend
    return <div>You can watch TV all day today</div>;
  }
}export default App;
```

让我们添加支持逻辑，好吗？首先…我们应该得到一个当前的日期。这不是一个困难的任务，简单地说`let today = new Date().getDay();`就可以了。 *getDay()* 将返回代表星期几的数字。0 =星期日—6 =星期六。然后我们将使用简单的 if 语句检查今天是否等于 0 或 6，如果是，我们将返回我们的消息。对于其他情况，我们需要其他信息。我们的代码应该是这样的。

```
import React, { Component } from 'react';class App extends Component {
  render() {
    // Get current date
    let today = new Date().getDay();
    // If it is weekday
    if (today === 6 || today === 0)
      return <div>You can watch TV all day today</div>;
    // If it is weeken
    return <div>You should go to work today</div>;
  }
}export default App;
```

好的，很可爱。现在*渲染*毕竟没那么假了。我希望你现在已经知道渲染方法的目的是什么了。我上次可以说…处理一些输入/数据，并基于此提供输出。现在继续……假设我们也希望我们的组件返回实际日期。我们希望它说“今天是星期一，你今天应该去工作”。我们现在能做吗？不。有可能吗？是的，让我们开始吧。我们需要两样东西。我们需要我们的组件能够将数字转换为当天的名称。0 =星期日—6 =星期六。我们可以用渲染方法，但是我建议用别的方法。我们将定义一个新函数，并将其命名为 getDayName()。在 React 组件中，我们可以随意创建任意数量的助手函数。我们希望我们的函数接受一个数字，并将其转换为表示每日名称的字符串。这样做有很多可能性。这是我的方法

```
getDayName(number) {
    let days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday',    'Thursday', 'Friday', 'Saturday'];
    return days[number];
  }
```

而整个 *App.js* 就会是这个样子

```
import React, { Component } from 'react';class App extends Component {
  getDayName(number) {
    let days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
    return days[number];
  } render() {
    // Get current date
    let today = new Date().getDay();
    // If it is weekday
    if (today === 6 || today === 0)
      return <div>You can watch TV all day today</div>;
    // If it is weeken
    return <div>You should go to work today</div>;
  }
}export default App;
```

我们完成了第一部分。现在我们只需要在 render 中使用我们的函数。还记得我说过我认为 JSX 是 HTML，但是有两个关键能力第二，能够在我的 HTML 中编写 javascript 代码吗？这是我们实际使用它的部分。当我们在 render 方法内部编写 *return* 时，它期望我们编写 JSX 代码。这意味着我们可以返回 HTML 标签或 React 组件。但是我们也可以在那里写 javascript 代码。我们需要做的就是在{}括号内编写 javascript 代码。渲染将看起来像这样

```
render() {
    // Get current date
    let today = new Date().getDay();
    // If it is weekday
    if (today === 6 || today === 0)
      return <div>Today is {this.getDayName(today)} - You can watch TV all day today</div>;
    // If it is weeken
    return <div>Today is {this.getDayName(today)} - You should go to work today</div>;
  }
```

《it 最聪明》中的 JSX……我们使用了括号，并在括号内执行 javascript 代码。为什么在 *getDayName(today)* 之前有*这个*？我们将 *getDayName* 函数定义为 *App* 组件的一部分，通过调用 *this* ，我们首先引用包含 *getDayName 函数的 *App* 。*

看起来我们已经完成了这一章的所有内容。我希望到目前为止你玩得开心，还有更多的事情要做。敬请关注，保持谦逊，继续仰望星空，下一章再见，我们将更好地利用 React 组件:)干杯！

请点击👏按钮下面几下，以示支持！⬇⬇谢谢！别忘了**关注下面的**快速码。

> 在[快速代码](http://www.quickcode.co/)上找到各种编程语言的免费课程。获取 [Messenger](https://www.messenger.com/t/1493528657352302) 的新更新。