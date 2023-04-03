# 反应本机:功能和类组件

> 原文：<https://medium.com/globant/react-native-functional-and-class-components-9ee9c0d9a03e?source=collection_archive---------2----------------------->

![](img/df80a828e327bc7f05460c2903991710.png)

你在屏幕上看到的所有东西都是 React Native 中的 T2 组件。

**T** 这里有**React Native 中两种**主要类型的组件——**功能类**和**类**组件。

# 功能组件

> 顾名思义，功能组件是简单的 JavaScript 函数，可以是**箭头** **函数**或使用**函数**关键字编写。

功能组件也被称为无状态组件，因为它们不能自己管理自己的状态或使用生命周期方法 T21。很明显，功能组件没有 **render()** 方法。

纯功能组件关注的是用户界面的呈现，而不是行为。他们只是接受道具并返回有效的 JSX 元素。

```
import React from "react";
import { Text, View, StyleSheet } from "react-native";const FunctionalComponent = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.headerText}>Functional Component</Text>
    </View>
  );
};const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
    backgroundColor: "#806B33",
  },
  headerText: {
    color: "#ffffff",
    fontSize: 20,
  },
});export default FunctionalComponent;
```

# 类别组件

> **类** **组件**是 JavaScript ES6 类，由一个名为 **React 的基类扩展而来。组件**。

**类** **组件**被称为**有状态**，因为它们可以管理状态，并且拥有生命周期方法，如 [constructor()](https://reactjs.org/docs/react-component.html#constructor) 、 [render()](https://reactjs.org/docs/react-component.html#render) 、 [componentDidMount()](https://reactjs.org/docs/react-component.html#componentdidmount) 等。

当编写一个类组件时，我们需要添加的唯一方法是 [render()](https://reactjs.org/docs/react-component.html#render) ，因为其他生命周期方法是可选的。类组件充当一个容器，可以将子组件包装在其中。

```
import React from "react";
import { View, Text, StyleSheet } from "react-native";class ClassComponent extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.headerText}>Class Component</Text>
      </View>
    );
  }
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
    backgroundColor: "#806B33",
  },headerText: {
    color: "#ffffff",
    fontSize: 20,
  },
});export default ClassComponent;
```

我们将在本文中看到另一个 React 特性，它非常有用并且与功能组件相关。下面就来快速介绍一下**挂钩**！

# 钩住

> 钩子是允许你从功能组件中把**钩子**到 React 状态和生命周期特性的函数。钩子不能和类组件一起工作。

***钩子*** 是 React 16.8 中引入的。它们允许我们像生命周期方法一样使用状态和反应特性，而无需修改我们现有的功能组件。

使用钩子时有两条基本规则要遵循:

*   它们应该总是在组件的顶层被调用，即在条件、循环或嵌套函数中的*而不是*。
*   正如我前面提到的，钩子只能从功能组件中调用。

# 结论

希望这篇文章能帮助您对 React native 中的功能和类组件有一个大致的了解，并了解在开发应用程序时何时使用哪个组件。

谢谢&阅读愉快！