# 让我们来理解 JavaScript 中的提升到底是什么

> 原文：<https://medium.com/quick-code/lets-understand-what-is-hoisting-all-about-in-javascript-dc4014e713a4?source=collection_archive---------9----------------------->

![](img/442ca78959d5325179f8d3e3f1b39d89.png)

在学习 JavaScript 时，我总是想知道浏览器是如何执行 JavaScript 代码的，所以我开始挖掘 V8 引擎并阅读关于幕后执行过程的文章。

虽然我以前听说过起重这个术语，但我并不清楚它背后的概念。

所以，让我们今天学习什么是提升。

# 什么是吊装？🤔

> 提升的英文意思是举起或举起某物的动作

在 JavaScript 中，提升是在代码执行之前将所有声明移动到作用域顶部的默认行为。基本上，它给了我们一个优势，无论函数和变量在哪里声明，它们都被移动到它们作用域的顶部，无论它们的作用域是全局的还是局部的。它允许我们在编写代码之前就调用函数。

让我们看看当使用`var, let and const`声明变量时，JavaScript 的行为有何不同。

**JavaScript 分两个阶段处理代码:**

**1。申报阶段**

**2。执行阶段**

吊装在*申报阶段*刚刚搞定。

# var 的吊装

当解释器第一次逐行运行代码时。在声明阶段，`var`变量已经初始化了`undefined`的值。

```
console.log(name)var name = "Bhavishya"
```

在上面的几行中，`var name`将被提升，变量将被赋值为`undefined`，所有这些都在执行过程之前完成。

# 出租和建造的吊装

像 var 一样，让变量被提升到它的作用域的顶部。然而，与 var 不同的是，在声明和赋值 let 变量之前调用它会抛出一个错误。

因此，let 和 const 变量被提升，但是*没有被初始化*。

这只是意味着 let 和 const 变量没有被赋予值`undefined`。

# 功能提升

像变量一样，函数也在声明阶段被提升。

主要区别在于，函数存储为函数声明，而不是将其值定义为未定义或给出初始化错误。

```
myName();function myName(){ console.log('Bhavishya');}
// Bhavishya
```

然而，如果函数被声明为表达式。

```
myName();var myName = function myName(){ console.log('Bhavishya');}
//uncaught TypeError:myName is not a function
```

输出返回一个错误，因为函数被声明为表达式，所以 JavaScript 赋值为`undefined`。

所以，是的，这就是我所知道的提升，我已经用这篇短文把它发表了。

让我知道你是否喜欢这篇文章，最重要的是你学到了一些新东西。

把它分享给你的朋友和同事，如果你喜欢就在下面鼓掌。

# 谢谢大家！！

> 不断学习和成长😎😎😎