# JavaScript“这个”是什么？

> 原文：<https://medium.com/walmartglobaltech/what-is-javascript-this-3d2194cb7a6c?source=collection_archive---------7----------------------->

![](img/f18e907b4a0d2abc1995bbc736cd5870.png)

Image taken from Unsplash [https://unsplash.com/photos/vpOeXr5wmR4](https://unsplash.com/photos/vpOeXr5wmR4)

`this`是一个被很多开发者误解的 JavaScript 关键字，这篇文章旨在弥补这个差距。虽然这篇文章是为初学者写的，但即使是有经验的开发人员也可以把它作为复习资料。`this` 是一个特殊的标识符关键字，在每个函数的范围内自动定义，但它实际指的是真实的交易。这篇文章分为三个部分，在第一部分，我们将谈论什么是`this`，然后我们将谈论一些关于`this`的常见误解，最后，我们将看到与`this`关键字相关的 JS 的一些特征。

# 先决条件

我相信学习就像健身，如果你不做好热身运动，你可能会伤害到自己。因此，在讨论`this`之前，让我们先来讨论一些 JavaScript 概念，对于本文来说，这些概念你应该很熟悉。

**1)** ***在全局范围内声明为*** `*var a = 2;*` ***的变量不过是同名的全局对象属性。*** 是的，他们不是彼此的复制品而是彼此。为了更清楚地说明这一点，请看下面的例子，我们如何访问作为全局对象`window`的键的`x`的值。此外，这里必须注意的一点是，这只适用于使用`var`的声明，而不是`let/const`。

**2)调用位置:**调用函数的位置称为*调用位置*。通常寻找*调用点*只包括定位调用函数的代码行，但是这可能会产生误导，因为函数可能在代码中的多个地方被调用。因此，找到*调用点*的最准确方法是找到*调用栈*，然后查看从哪里调用该函数，或者换句话说，在*调用栈*中的前一个函数是什么。我们用一个例子来理解这个。

# 什么是`this`？

是为每个函数调用创建的绑定，其值取决于调用函数的方式和位置。为`this`做的绑定通常与使用`var`声明的变量和运行在[非严格](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)模式的 JavaScript 相关。进一步在*调用点*的基础上，我们可以将这样的绑定分为如下四个部分:

*   默认绑定
*   隐式结合
*   显式绑定
*   *新*装订

D 函数内部的这个被绑定到全局作用域。是的，你可以在这样的函数中使用*这个*来访问所有的全局变量。这里需要注意的一点是，它只适用于使用`var`定义的`non-strict`模式和变量。

Default Binding

*严格模式*:此时`this`的值未定义。

*let/const* :用`let/const`定义的变量不绑定。

I**implicit Binding**:当函数的调用点有一个包含对象(上下文对象)时，如下所示。该上下文对象被绑定到该函数内的 *this* 。在下面的例子中，`obj`是上下文对象，*这个*被绑定到这个对象，所有的键都可以在函数内部使用*这个*来访问。

Implicit Binding

*严格模式*:这将与`non-strict`模式一样工作。

*let/const* :不适用

透明绑定:这种绑定给了我们赋予任何东西的权力。我们可以使用`call(..)`和`apply(..)`方法实现这一点。是的，我们可以将`this`设置为一个对象、变量、函数或任何东西。但是有一个问题，`null`和`undefined`被替换为全局对象，原语被转换为等价对象。无论我们将传递什么作为第一个参数，它将被函数调用用作*这个*绑定。

Explicit Binding

*严格模式* : `null`和`undefined`不会被全局对象替换，图元也不会被转换成对象。

*let/const* :当`this`绑定到全局对象时不支持(直接或者从`null`或`undefined`替换后)，但是用`let/const`定义的变量可以直接作为`this`传递，它会被绑定。

N 在这种绑定中，如果我们用关键字`new`调用函数，并将其赋给一个变量，那么`this`就会绑定到该变量。看下面的例子就明白了。这里我们将`new doSomething()`的值赋给`bar`，因此在函数内部，*这个*被绑定到`bar`。

new Binding

*严格模式*:无效果

*let/const* :定义的变量可以都是`let/const`，观察到相同的行为。

# 这个不是什么**？**

让我们看看许多开发人员对`this`最常见的误解，并深入探究每一个。人们认为`this`中的功能是指:

1.  它自己
2.  其范围

我 ***tself*** 很多开发者认为*这个*指的是调用它的函数本身哪个是错的。下面是一个简单的例子来证明它。

确切地说，跟踪调用了多少次`increase`的正确方法应该是实际使用`call`方法来显式地将`this`关键字定义为`increase`对象(自身)。下面给出的代码示例是它的一个工作示例。

Corrent way

I ***ts 作用域*** 开发人员的另一个著名的误解是，他们认为它指的是函数的作用域。这是一个棘手的问题，因为在全局作用域中，`this`指的是全局作用域本身，但这是一个非常误导的陈述，因为我们在前面的部分中已经看到这是不正确的。

# 关于``this``的更多 JavaScript 特性

***bind()* 函数:** ES5 引入了这个函数，帮助显式绑定`this`的值。`bind`返回一个函数，将`this`设置为`bind`的第一个参数。这与`call`和`apply`有些相似，但是不同之处在于一旦我们使用了`bind`，函数会保留它的上下文，这样它就可以被重用。

***箭头*函数:**在这些函数中，`this`保留封闭词法上下文的`this`的值。在全局代码中，它将被设置为全局对象。下面我们来看几个例子:

`**this**` **在类中:**`this`在类和函数中的行为是相同的，因为类只是幕后的函数。尽管如此，我还是建议阅读[这里的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this#class_context)有一些边角案例。一个常见的约定是覆盖`this`行为，这样类中的`this`总是引用类实例。这在 React 类组件中很常见。

`**this**` **作为 DOM 处理程序:**当一个函数被用作事件处理程序时，它的`this`被设置为监听器所在的元素。下面给出的代码将打印 DOM 元素本身。

# 参考文献和推荐读物:

1.  [你不知道 JS:这个和对象原型](https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/this%20&%20object%20prototypes/README.md#you-dont-know-js-this--object-prototypes)
2.  [本:MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)