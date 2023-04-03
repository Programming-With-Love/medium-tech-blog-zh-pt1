# 有例子的 Javascript 函数是什么

> 原文：<https://medium.com/duomly-blockchain-online-courses/what-are-javascript-functions-with-examples-913fe43649c6?source=collection_archive---------6----------------------->

![](img/7ee988065a73b9d6850f5ee745f5fc2e.png)

[Duomly — programming online courses](https://www.duomly.com)

本文最初发布于:[https://www . blog . duomly . com/essential-knowledge-about-JavaScript-functions-with-examples/](https://www.blog.duomly.com/essential-knowledge-about-javascript-functions-with-examples/)

函数是编程中最基本的元素之一。它是一组执行某些活动以获得结果的语句。在许多情况下，使用作为输入提供的数据来执行操作。每次调用函数时，都会执行函数中的语句。

函数用于避免重复相同的代码。这个想法是将执行多次的任务聚集到一个函数中，然后在任何你想运行代码的地方调用这个函数。

考虑到函数在 Javascript 中是如此重要的一个概念，我将介绍:

*   定义一个函数，
*   调用一个函数，
*   返回声明，
*   参数和自变量，
*   箭头功能，
*   自调用函数。

**要检查代码执行情况，请在浏览器中打开控制台并尝试执行代码(如果您使用的是谷歌浏览器，请在页面上右键单击并选择调查)*

# 定义函数

我们可以用两种不同的方式定义函数。

将函数定义为 ***函数声明*** 总是以 function 关键字开头。然后，我们设置函数的名称，后面是括号中的参数，如果不需要参数，则为空括号。接下来，该语句以花括号({})结束。让我们来看一个代码示例:

```
function sayHi(name) {
    return'Hi'+name;
}
```

在上面的函数示例中，名称为 sayHi *，*，参数为(*名称)。*同样值得一提的是，由声明定义的函数可以在定义之前使用，因为它是**提升的。**

另一种定义函数的方法被称为**函数**表达式。这样，也可以定义一个命名和匿名函数。还有，这种情况下提升是不行的，必须先定义函数，然后才能使用。用这种方法创建的大多数函数都被赋给了一个变量。让我们看一下代码示例:

```
var sayHi = function (name) {
    return 'Hi' + name;
}
```

在上面的例子中，函数被赋值给变量 *sayHi，*，但是函数本身没有名字，所以我们可以称这个函数为匿名函数。

# 调用函数

现在我们知道了如何用两种方法在 Javascript 中定义一个函数，让我们看看如何执行这个函数。我们可以说 ***调用*** 函数，而不是 ***调用*** 函数，这是执行过程的术语。

那么，如何调用或调用函数呢？

要调用上一个示例中的函数，我们必须从函数名开始，后跟带参数的括号:

```
function sayHi(name) {
    return 'Hi' + name;
}
sayHi('Peter');
```

在上面的代码中，我们可以看到函数名 *sayHi* 后面跟随着预期的参数( *Peter)。*现在该函数应该启动并返回*嗨彼得*字符串。

# 返回

在上面的例子中，我们的函数返回了一个带参数的字符串。每个函数都需要返回一个结果，如果没有定义任何返回语句，函数将返回 undefined。让我们看一个例子:

```
// With return
function calc(a, b) {
    return a + b;
}
calc(1, 4) // returns 5// Without return
function calc(a, b) {
  a + b;
}
calc(1, 4) // returns undefined
```

在上面的例子中，第一个函数返回数学运算的结果，另一个函数没有 *return* 语句，这意味着它将返回 *undefined* 。

# 参数和自变量

参数和实参经常交替使用，但这两者是有区别的。**参数**是我们在定义函数时放入括号中的名称，例如:

```
function name(param1, param2, param3) {
    // statement
}
```

在上面的示例中，参数是 param1、param2 和 param3。在这种情况下，还没有争论。

**参数**是由 params 带入函数的值。它是我们在调用时放入函数的内容。让我们看看这个例子:

```
name('Mark', 'Peter', 'Kate');
```

在上面的例子中，前一个例子中的函数是用参数 now 调用的，我们的参数是 param1 = Mark，param2 = Peter，param3 = Kate ***。***

如果我们谈到参数和自变量主题，还有一件事值得一说。有时候我们不确定要传递给函数多少个参数。然后我们可以使用参数对象，然后传递尽可能多的参数。让我们看看它在真实例子中是如何工作的:

```
// Define a function with one param
function calc(num) {
    return 2 * num;
}// Invoke the function with more params
calc(10, 5, 2);
```

在上面的例子中，我们用一个参数 num 定义了一个函数，并用三个以上的参数调用它。现在，该函数将 num 识别为第一个传递的参数，但是我们可以将 param 视为类似数组的对象:

```
// Define a function with one param assuming it's an object
function calc(num) {
    return 2 * num[0] * num[1];
}// Invoke the function with more params
calc(10, 5, 2);
```

在这种情况下，我们定义了一个带参数的函数，它将是一个对象，现在我们可以使用所有传递的参数。该函数将根据上面的示例 2*10*5 进行以下计算，采用第一个和第二个参数。

# 箭头功能

在 ES6 **中引入了箭头功能(= > )** 。箭头函数只不过是声明函数表达式的较短语法。让我们看一下代码示例:

```
sayHi = (name) => { 
    // statement
}
```

在上面的代码示例中，我们用 name 参数定义了一个箭头函数 sayHi，但没有使用 function 关键字。事实上，如果只有一个参数，可以跳过括号。

# 自调用函数

Javascript 中还有一种类型的函数，即**自调用函数。**这些是匿名函数，在定义完成后立即调用。自调用函数放在一个额外的括号内，并且在末尾有一对额外的括号。让我们看一下代码:

```
(function (num1, num2) {
    return num1 + num2;
})();
```

在上面的例子中，你可以看到自调用函数是一个普通的匿名函数，增加了两对括号。

# 结论

在本文中，我讲述了函数的一些基本内容，比如使用两种不同的方法定义函数和调用函数。我还解释了参数和自变量之间的区别，并描述了 arguments 对象的用法。此外，我还研究了箭头函数和自调用函数。

希望这篇文章对你有用。尝试创建自己的函数，并尝试不同的定义和调用方法。

享受编码的乐趣！

![](img/93f37fe763ace8d7cd181798b57c8a30.png)

[Duomly — programming online courses](https://www.duomly.com)

感谢您的阅读！

本文由我们的队友安娜提供。