# 快速反应提示:使用类属性和箭头函数来避免将“this”绑定到方法上

> 原文：<https://medium.com/quick-code/react-quick-tip-use-class-properties-and-arrow-functions-to-avoid-binding-this-to-methods-29628aca2e25?source=collection_archive---------1----------------------->

![](img/a83a4022f42fd0943ea819cb47d1768a.png)

当您想要访问 React 组件的类方法内部的`this`时，您需要将它绑定到您的方法:

```
class Button extends Component {
  constructor(props) {
    super(props);
    this.state = { clicked: false }; **this.handleClick = this.handleClick.bind(this);**} **handleClick() {
    this.setState({ clicked: true });
  }** render() {
    return <button onClick={this.handleClick}>Click Me!</button>;
  }
}
```

在构造函数中将`this`绑定到`handleClick`允许我们在`handleClick`内部使用来自`Component`的`this.setState`。如果没有这个绑定，`this`的作用域会被`handleClick`重新限定，并且会丢失组件的`setState`方法的上下文。

但这完全没有必要，多余的代码！

你可以通过使用一些新的 ES6+功能来清理这种丑陋。下面是相同的组件，使用类属性和箭头函数重写，以避免将`this`绑定到`handleClick`:

```
class Button extends Component {
  state = { clicked: false }; **handleClick = () => this.setState({ clicked: true });** render() {
    return <button onClick={this.handleClick}>Click Me!</button>;
  }
}
```

*注意:您必须在自己的 Babel config 中启用*[*transform-class-properties*](http://babeljs.io/docs/plugins/transform-class-properties)*才能使用类属性。如果您正在使用 Create React App，这已经为您启用了。*

## 为什么会这样？

这是因为两个原因:

1.  箭头函数本质上不会重定`this`的作用域，所以我们不需要在类构造函数中绑定`this`。
2.  JavaScript 有一级函数，这意味着函数和数据被同等对待。因此，箭头函数可以分配给变量，或者在本例中，分配给类属性。

## 额外小费

注意，在第二个例子中，我也将`state`定义为一个类属性，这样就不需要构造函数了。

请点击👏按钮下面几下，以示支持！⬇⬇

谢谢！别忘了**关注下面的**快速码。

> *在* [*上查找各种编程语言的免费课程*](http://www.quickcode.co/) *。在*[*Messenger*](https://www.messenger.com/t/1493528657352302)*上获取新的更新。*