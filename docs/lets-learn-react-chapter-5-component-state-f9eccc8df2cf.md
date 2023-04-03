# 如何学习 React #5 —组件状态快速介绍

> 原文：<https://medium.com/quick-code/lets-learn-react-chapter-5-component-state-f9eccc8df2cf?source=collection_archive---------0----------------------->

![](img/169ddc681d5c7de65d9ffe77087409fc.png)

亲爱的读者你好！如果你阅读了本系列的前几章，你应该已经对组件非常熟悉了。如果没有，请继续阅读！我保证你不会后悔的。如果你给我发邮件，我会补偿你浪费的时间。顾名思义，本章我们将讨论组件状态。我知道我应该从一开始就描述它是什么，但首先让我们为自己创造一些挑战。到目前为止，我们的应用程序告诉我们今天是哪一天，以及我们今天是否应该工作。我不知道你怎么想，但我认为它可以做得更好。假设我们想给我们的应用程序添加新功能。在我们的应用程序中，我们希望能够添加我今天打算吃的食物。就这么简单。它会是什么样子。我们将有单独的组件，这个组件将包含 1 个文本输入，1 个按钮和一个项目列表。当我们点击按钮时，来自文本输入的项目将被添加到项目列表中。好了，让我们翻到编辑器，创建这个组件。

在*组件*目录中，我将创建名为 *MealPlan.js* 的新文件，我还将添加基本组件结构。它看起来会像这样

```
import React, { Component } from 'react';class MealPlan extends Component {
  render() {
  }
}export default MealPlan;
```

目前我还不知道什么新奇的东西。我们现在继续添加文本输入按钮和列表，我已经开始添加了。我还为组件添加了标题，但这不是必需的。我们的 *MealPlan.js* 看起来会像这样

```
import React, { Component } from 'react';class MealPlan extends Component {
  render() {
    return(
      <div>
        <h2>Today you should eat this</h2>
        <input type="text" />
        <button>Add meal</button>
        <ul>
          <li>Spaghetti</li>
          <li>Ice cream</li>
        </ul>
      </div>
    );
  }
}export default MealPlan;
```

好的，这里有文本输入，这里有按钮，还有餐单。到目前为止，列表中的值是硬编码的，但不要担心，我们会改变这一点。你可能在想。好的，我们需要在某个地方存放餐食清单。还是想知道？我希望你…相信我，这是个好兆头。为了回答这个问题，我们将以组件状态存储用餐列表。所以我们要做两件事，不，让我们做三件事。首先，我要解释什么是组件状态。让我们现在做那件事。

react 中的每个组件都有自己的状态，你可以用它来描述你的组件。我们的组件将在它的状态列表中存储食物。我希望这没什么难懂的。我们可以操作组件状态，我们可以添加、删除或修改它们。然而重要的是，每次状态改变时，组件被重新呈现。如果这些都不能让你想起什么，我很确定当我们使用它的时候会的。所以不要再用无聊的理论浪费时间了，让我们更进一步。

第二，我们要定义组件状态。将`state = {meals: []};`添加到 *MealPlan* 类的正下方，以确保它应该在这里

```
class MealPlan extends Component {
  state = {meals: []};
```

就这么简单。我们告诉我们的组件将在它的状态下存储膳食，并且膳食在开始时是空数组。继续第三件事。现在我们在渲染方法中有硬编码的食物，你会猜到我们会改变它。在 render 方法中，我们希望迭代一组饭菜，并将每一份都写入饭菜列表。为此，我们将使用名为 lodash 的库—[https://lodash.com](https://lodash.com)。打开终端和内部应用程序目录运行`npm install — save lodash`安装好所有东西后，我们需要退出我们的开发服务器并重新启动它。好了，现在让我们开始实现饭菜的渲染。在 MealPlan.js 中，我们将添加一个导入语句`import _ from 'lodash';`,然后我们将添加一个名为 *renderMeals* 的新函数，它看起来像这样

```
renderMeals() {
    return _.map(this.state.meals, meal => <li>{meal}</li>);
}
```

这里我使用了 lodash 库的 *map* 函数。基本上这个函数有两个参数。第一个要迭代的集合，第二个是告诉我要对每一项做什么的函数。我在函数中说，对于每顿饭，我想返回新的`<li>`元素，其中包含饭菜的名称。现在我们可以在 render 中调用这个函数，我们的组件看起来会像这样

```
import React, { Component } from 'react';
import _ from 'lodash';class MealPlan extends Component {
  state = {meals: []};renderMeals() {
    return _.map(this.state.meals, meal => <li>{meal}</li>);
  }render() {
    return(
      <div>
        <h2>Today you should eat this</h2>
        <input type="text" />
        <button>Add meal</button>
        <ul>
          {this.renderMeals()}
        </ul>
      </div>
    );
  }
}export default MealPlan;
```

好的…目前为止我们看起来不错。但是我们有一个问题。我们不知道文本输入的价值。我们不能说用户真正输入了什么。正如您所猜测的，我们将把这些信息存储到我们的州。让我们继续扩展我们的状态，如下所示

```
state = {
    meal: '',
    meals: []
};
```

现在，我们需要确保当输入改变时，状态被正确地更新，并且输入与我们的状态一致。我们要这样做，通过改变我们的输入看起来像这样

```
<input onChange={e => this.setState({meal: e.target.value})} value={this.state.meal} type="text" />
```

如你所见，我添加了 *value* 属性，我是说这个输入的值应该等于该州的 meal。而且我加了 *onChange。每次输入改变时都会调用 OnChange* 。每次我提供的函数都会被调用。所以当你检查这个函数时

```
e => this.setState({meal: e.target.value})
```

它只从输入中取值并存储到状态中。现在，当我们单击按钮时，我们希望从 state 中获取膳食值，并将其添加到 out state 中的膳食列表中。每个按钮元素都有方法 *onClick* ，当按钮被点击时调用该方法。我们将在这个 *onClick* 方法中实现我们的逻辑。我们可以将按钮修改成这样，我们完成的组件 *MealPlan.js* 将会是这样

```
import React, { Component } from 'react';
import _ from 'lodash';class MealPlan extends Component {
  state = {
    meal: '',
    meals: []
  };renderMeals() {
    return _.map(this.state.meals, meal => <li>{meal}</li>);
  }render() {
    return(
      <div>
        <h2>Today you should eat this</h2>
        <input 
          onChange={e => this.setState({meal: e.target.value})} 
          value={this.state.meal} 
          type="text" 
        />
        <button 
          onClick={() => this.setState({meals: [...this.state.meals, this.state.meal]})}>
          Add meal</button>
        <ul>
          {this.renderMeals()}
        </ul>
      </div>
    );
  }
}export default MealPlan;
```

所以我们给 *onClick* 增加了函数。在那里我们称之为 *setState* 。正如你所猜测的，它改变了状态，当状态改变时，组件被重新渲染。这基本上就是我们想要的。

```
[...this.state.meals, this.state.meal]
```

你可能会问为什么要使用这种奇怪的语法，这是个很好的问题。老实说，这一章太长了，所以我会在下一章回答。感谢阅读，并希望你有一个伟大的一天。干杯！