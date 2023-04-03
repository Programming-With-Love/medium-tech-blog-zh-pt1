# 如何学习 React #4 —发现函数组件和类组件之间的差异

> 原文：<https://medium.com/quick-code/lets-learn-react-chapter-4-functional-x-class-based-component-227f89ea2192?source=collection_archive---------1----------------------->

![](img/169ddc681d5c7de65d9ffe77087409fc.png)

欢迎光临！首先，我希望标题是 2 种类型的组件，我有一种感觉，2 成为这个系列的某种神奇数字。然而，我改变了它，但我不得不在继续我们的学习进程之前分享这种艰难。在[上一章](/quick-code/lets-learn-react-chapter-3-components-components-components-3492f771d623)中，我们创建了新的组件并使用了它们。在这一章中，我将介绍编写 React 组件的新方法，并对它们进行比较。在这一章中我们不会做太多的编码，但是我保证在下一章中你会满意的。现在你可以玩滚石了——满意了。我是认真的…以下是给你的链接【https://www.youtube.com/watch?v=nrIPxlFzDi0 

## 基于类的组件

到目前为止，当我们创建新的组件，我们做了两个简单的步骤。首先我们写了第`import React, { Component } from 'react'`行，然后我们创建了类扩展组件，就像这个`class Header extends Component`。当我们创建这样的组件时，我们创建的是基于类的组件。

## 功能成分

还记得我们的 *Header.js* 组件吗？它看起来就像这样

```
import React, { Component } from 'react';class Header extends Component {
  render() {
    return <h2>This app will tel you if you should work today</h2>;
  }
}export default Header;
```

现在我要把它改写成功能组件…好了，我完成了。瞧啊。！

```
import React from 'react';const Header = () => {
  return <h2>This app will tel you if you should work today</h2>;
}export default Header;
```

注意两件事。首先—导入语句已更改，我们不再导入组件。第二，我不是在创建类，而是在创建函数。这两个代码块看起来不同，但呈现的组件看起来完全一样。你会问它们之间有什么区别？我们不能在功能组件中使用生命周期方法，功能组件是无状态的。这两件事现在可能还没有引起注意，但是我们很快就会谈到生命周期方法和状态。

如果我使用基于类的函数组件，会有什么不同吗？嗯……不尽然。为什么我会把你搞糊涂了？很高兴知道有两种类型的组件。现在我们将只使用基于类的，但我很确定如果你对 React 更感兴趣，你最终会接触到函数组件，我不想让你抓狂。我知道…我很负责任。

好的，既然我在这一章提到了我们要做编码，我觉得我也不应该用我的话来烦你。让我们结束这一章，下一章我们开始处理组件状态时再见。请继续关注，保持谦逊，注意失球。干杯！