# JS 彩虹桥——让“这个”保持平静！

> 原文：<https://medium.com/globant/the-js-bifrost-keep-the-this-at-peace-d87011b5a685?source=collection_archive---------1----------------------->

## 揭开令人困惑的关键字`this`以及调用、应用和绑定方法的用法的神秘面纱。

欢迎回到“JS Bifrost ”,这是您通向神级 JavaScript 的坚实基础的道路。这是本系列的下一篇文章。我们这次的重点是— `.call`、`.apply`、`.bind`方法。

![](img/2378b2813eaf8c9ff0374bdcdd372d93.png)

`this`关键字是 JavaScript 中使用最广泛也最令人困惑的关键字之一。在这篇文章中，我将通过各种方法来稳定“这个”。这也是大多数 JS 面试的精髓面试题目。是的，没错，这三种方法，即`call`、`apply`和`bind`在几乎所有的 web 开发人员面试中都被频繁地问到，所以如果你是一名 JavaScript 开发人员，就不能忽视对它们及其用例的理解。

*在我们谈论方法本身之前，让我们先花点时间来理解一下* `this` *关键字在 JS 中是如何工作的，因为理解* `this` *关键字是如何工作的对于理解上述方法至关重要。*

# **本**关键字概述

现在，如果您熟悉 JS 中的`this`关键字的工作方式，请随意跳过这一节，进入下一节。每当在 JavaScript 中创建一个函数时，就会创建`this`关键字，当然是在幕后，它将该函数链接到该函数所属的对象。听起来很困惑？让我们看看代码，以便更好地理解这一点。

```
//declare functionconst yourFunc = function() {console.log(this) // this === window 
                   'strict' mode? this === undefined}
yourFunc();
```

现在让我们看另一个例子，一个函数在一个对象中

```
//Object Literal
const Person = {
 firstName : 'John',
 lastName : 'Doe',
 sayHi(){
  console.log(this)
  console.log(`Hello ${this.firstName} ${this.lastName}`)
 },
}Person.sayHi() // Implicit Binding*Output: {firstName: "John", lastName: "Doe", sayHi: ƒ}* *Hello John Doe*
```

在上面的例子中，`this`的值将指向 Person 对象，因为它是在`Person` `object`的上下文中调用的，我们得到如上所示的输出。以上两个例子都是`implicit binding`的。**隐式绑定**总是查看谁调用了`dot`左侧的函数(上下文)或任何东西，然后将那个`object`的值赋给`this`。在第一个例子中，调用`yourFunc()`与`window.yourFunc()`相同，这就是为什么我们将`window`作为`this`的值。

在 JS 中有不同的方法来绑定`this`关键字，我们已经看到了一个这样的方法，这里是**隐式绑定**，现在让我们来看看**显式绑定**

# 显式绑定(调用、应用、绑定)

`Explicit binding`是当我们明确指定方法/函数必须调用的上下文时。这可以通过`call`、`apply`和`bind`来完成。现在，让我们逐一通过一些示例来看一下其中的每一项。

## 打电话

因为在 JavaScript 中包括函数在内的所有东西都被认为是一个对象，所以它可以有属性。`.call`方法就是这样一种属性，可以在任何函数的原型中找到。让我们考虑另一个例子，我们有一个函数`sayHi`并没有做很多事情，但是这里我们的重点是理解`call`方法做了什么。使用`.call`方法，我们可以调用具有特定上下文的函数。下面是`.call`方法的一般语法。

```
function_name.call(*this*,a1,a2,a3,...);
```

*所以从语法上，我们可以看到* `*.call*` *方法接受* `*object*` *(* `*this*` *)作为第一个参数，然后是一个逗号分隔的所有其他参数的列表。*

```
const Person1 = {
 firstName : 'John',
 lastName : 'Doe',
}
const Person2 = {
 firstName : 'Mark',
 lastName : 'Twain',
}const Hobbies = ['Cricket','Football','Netflix'];
const sayHi = function (h1,h2,h3){
  console.log(this);
  console.log(`My name is ${this.firstName} ${this.lastName} and I  love ${h1},${h2} and ${h3}`);
 }sayHi.call(*Person1*,Hobbies[0],Hobbies[1],Hobbies[2]);//this=Person1
sayHi.call(*Person2*,Hobbies[0],Hobbies[1],Hobbies[2]);//this=Person2
```

![](img/400a57f14ed26d505c5fbc1e15e336b0.png)

output for the above code snippet

# 应用

现在，`.apply`方法类似于`.call`方法，唯一的区别是`.apply`方法接受一个参数数组作为第二个参数，如下面的语法所示

```
function_name.apply(this,[arguments array]);
```

让我们尝试重新创建同一个示例，但这一次，让我们使用 apply 方法调用函数，并使用`Person2`作为上下文。

```
const Person1 = {
 firstName : 'John',
 lastName : 'Doe',
}
const Person2 = {
 firstName : 'Mark',
 lastName : 'Twain',
}const Hobbies = ['Cricket','Football','Netflix']
const sayHi = function (h1,h2,h3){
  console.log(this);
  console.log(`My name is ${this.firstName} ${this.lastName} and I  love ${h1},${h2} and ${h3}`);
 }sayHi.call(*Person1*,Hobbies[0],Hobbies[1],Hobbies[2]);//this=Person1
sayHi.apply(*Person2*,Hobbies);//this=Person2
```

![](img/400a57f14ed26d505c5fbc1e15e336b0.png)

output for the above snippet

*从上面的例子中，我们可以看到 call 和 apply 几乎是一样的，但是根本的区别是* `*.call*` *接受一个逗号分隔的参数列表，而* `*.apply*` *接受一个参数数组。*

# 约束

我们看到`.call`和`.apply`都用期望的上下文和参数调用函数。`.bind`返回一个新函数，它可以存储在一个变量中，然后在以后的任何时间点使用特定的上下文调用。

```
const boundFunction = function.bind(this,arg1,arg2...);
```

返回的函数是一个绑定函数，它是原始函数对象周围的一个对象。调用绑定函数将调用带有初始`.bind`调用期间设置的`context`的包装函数。让我们看看下面的例子来更好地理解它。

```
const Person1 = {
 firstName : 'John',
 lastName : 'Doe',
}
const Hobbies = ['Cricket','Football','Netflix'];
const sayHi = function (h1,h2,h3){
  console.log(this);
  console.log(`My name is ${this.firstName} ${this.lastName} and I  love ${h1},${h2} and ${h3}`);
 }
const boundHi= sayHi.bind(Person1,Hobbies[0],Hobbies[1],Hobbies[2]);
boundHi();
```

![](img/088b94aded608d1b38cd9680105e8d28.png)

output for the above code snippet

所以在上面的例子中,`boundHi`是一个变量，它存储将原始函数与`Person1`对象和参数绑定后返回的绑定函数。当我们调用`boundHi().`时，我们得到期望的输出

`.bind`也用于我们希望保留`this`值的地方，例如在`callbacks`中。让我们再举一个例子来更好地理解这一点。

```
let user = {
  firstName: "John",
  sayHi() {
    alert(`Hello, ${this.firstName}!`);
  }
};
setTimeout(user.sayHi, 1000); *output: Hello, undefined*
```

在上面的例子中，`this`的值在作为`setTimeout`函数中的`callback`传递时丢失，并且`setTimeout`中的`this`将指向`window` `object`，而`window``object`反过来又改变了`this.firstName => window.firstName`，实际上给出了`undefined`。我们如何克服这一点？当然是用`.bind`的方法。

```
let user = {
  firstName: "John",
  sayHi() {
    alert(`Hello, ${this.firstName}!`);
  }
};
const boundHi = user.sayHi.bind(user); //*binds user obj* setTimeout(boundHi, 1000);*output: Hello, John*
```

这里，我们将绑定的函数作为回调传递给 setTimeout 函数，该函数给出了我们想要的输出。*这里主要说的是* `*.bind*` *用一个固定的* `*context*` *回馈同样的功能，可以传递给* `*callbacks*` *或者以后随时使用。*

# 让我们总结一下

`.call`:调用具有特定上下文的函数，并接受逗号分隔的参数列表

`.apply`:做与`.call`相同的事情，但是接受一个参数数组作为第二个参数

`.bind`:返回一个新的绑定函数，它是将`this`变量绑定到指定的`object`的原始函数。

就这样了，伙计们。我希望这能对`.call`、`.apply`和`.bind`方法的使用有所启发。谢谢:)

更多敬请关注我们下面的'***JS 的*** '！！

[](/globant/the-js-bifrost-currying-functions-in-javascript-e03a216b4b59) [## JS Bifrost——Javascript 中的 Currying 函数

### JS 的世界

medium.com](/globant/the-js-bifrost-currying-functions-in-javascript-e03a216b4b59) [](/globant/the-js-bifrost-shallow-or-deep-copy-22144e6787d6) [## JS 彩虹糖——浅拷贝还是深拷贝？

### 复制数据都是关于值、引用和内存分配的

medium.com](/globant/the-js-bifrost-shallow-or-deep-copy-22144e6787d6) [](/globant/cleaner-code-with-javascript-functions-d08d3bb37836) [## 带有 JavaScript 函数的更干净的代码

### 了解纯函数和高阶函数来编写最先进的代码！

medium.com](/globant/cleaner-code-with-javascript-functions-d08d3bb37836) [](/globant/the-js-bifrost-nullish-coalescing-operator-6ac55e59f61f) [## JS 双花聚结(？？)运算符

### what-why-how 无效合并运算符以及链接和逻辑运算

medium.com](/globant/the-js-bifrost-nullish-coalescing-operator-6ac55e59f61f) [](/globant/the-js-bifrost-understanding-the-coding-pattern-called-iife-794b46006550) [## JS 彩虹桥——理解称为(IIFE)的编码模式

### 最受欢迎的函数表达式习语

medium.com](/globant/the-js-bifrost-understanding-the-coding-pattern-called-iife-794b46006550) [](/globant/the-js-bifrost-memoization-it-is-65f890f14308) [## JS 彩虹糖——就是它了！

### 使迭代或递归函数更加优化的编程实践

medium.com](/globant/the-js-bifrost-memoization-it-is-65f890f14308) [](/globant/the-js-bifrost-incredible-javascript-features-587b78865e67) [## JS Bifrost——不可思议的 JavaScript 特性

### 您应该在项目中开始使用的 7 个 Javascript 特性

medium.com](/globant/the-js-bifrost-incredible-javascript-features-587b78865e67) [](/globant/the-js-bifrost-callback-hell-4c699e1954b8) [## JS 彩虹桥——回调地狱

### 你需要知道如何应对这个地狱，如何超越陈词滥调！

medium.com](/globant/the-js-bifrost-callback-hell-4c699e1954b8) [](/globant/the-js-bifrost-all-that-we-need-to-know-about-promises-2c7b087b56f3) [## JS 彩虹桥——关于承诺，我们需要知道的一切

### 理解承诺及其解决实际应用问题陈述的方法

medium.com](/globant/the-js-bifrost-all-that-we-need-to-know-about-promises-2c7b087b56f3) [](/globant/the-js-bifrost-inheritance-in-js-prototype-and-class-inheritance-84ec4c60b1a2) [## JS 中的继承——原型和类继承

### 欢迎来到 JS Bifrost，这是您通向神级 JavaScript 坚实基础的道路。这是下一篇文章…

medium.com](/globant/the-js-bifrost-inheritance-in-js-prototype-and-class-inheritance-84ec4c60b1a2)