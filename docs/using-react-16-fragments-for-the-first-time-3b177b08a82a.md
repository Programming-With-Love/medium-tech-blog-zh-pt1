# 第一次使用 React 16 片段

> 原文：<https://medium.com/quick-code/using-react-16-fragments-for-the-first-time-3b177b08a82a?source=collection_archive---------5----------------------->

React 16 为 React 开发人员带来了一系列新工具，包括门户、错误边界、构造函数的弃用以及片段、字符串和数组而不是 div。在这篇文章中，我将向你展示如何使用 React 片段和数组，并告诉你为什么它们比 div 更有效。

![](img/209460c486c392e0cf8763c362c93758.png)

为了开始，你首先需要确保你更新到 React 和 Babel 的最新版本。这可以通过在终端中输入以下命令来实现:

```
yarn add react@^16.2.0 react-dom@^16.2.0yarn upgrade @babel/core @babel/plugin-transform-react-jsx
```

一旦你将你的 React 版本升级到 16.2，将 Babel 升级到 7，你就可以开始了。

在想要使用片段或数组的组件中，需要在文件的顶部要求 Fragment from React。这是按如下方式完成的:

```
import React, { Component, Fragment} from ‘react’;
```

最后，您只需移除包装 div 并用 <fragment></fragment> 替换它。

```
class Answers extends Component { render () { let answersCard = nullquestion ? 
sortedAnswers = ( question.answers.sort(function(a,b) { return  (a.count < b.count) ? 1 : ((b.count > a.count) ? -1 : 0)}).map(answer => <Answer key={answer.id} answer={answer} questionId={question.id} /> )) 
: sortedAnswers = ( <p>Loading...</p> )return (
      <Fragment>
         {sortedAnswers}
      </Fragment>
     )
   }}
```

类似地，您可以用数组完全替换片段和包含的括号。如果数组中有多个元素，就需要用逗号分隔这些元素，就像下面这个例子中的后面的逗号一样:

```
return [ <form onSubmit={this.handleOnSubmit}>
         <div>
           <textarea
           name="content"
           value={this.state.content}
           onChange={this.handleOnChange}>
           </textarea>
        </div>
        <div>
          <button type="submit">Add Answer</button>
        </div>
      </form>,
      <Answers question={this.props.question} />]
   }
```

虽然片段是目前推荐的封闭语法，但很快就有可能只使用空字符串来代替数组或片段<> >，但目前并不是所有设备都支持这一点，因此 React 文档建议您暂时继续使用片段。

如果你和我一样，第一次看到这个的时候，你会问自己，为什么我要关心添加一个 div 作为片段的工作量是不是一样的呢？

好吧，丹·阿布拉莫夫本人在这篇堆栈溢出[文章](https://stackoverflow.com/questions/47761894/why-are-fragments-in-react-16-better-than-container-divs)中也谈到了这个问题。本质上，1)碎片比 div 略快，占用内存少；2)片段对于 Flexbox、CSS Grid 等一些 CSS 操作更有效，div 对布局有影响；3)使用片段时，DOM 不那么混乱。

我希望这篇文章能帮助你更新你的反应技能！