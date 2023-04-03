# ECMAScript 2019 (ES2019)的新功能

> 原文：<https://medium.com/duomly-blockchain-online-courses/new-features-of-ecmascript-2019-es2019-1777dfe804d4?source=collection_archive---------7----------------------->

![](img/1fd338bd3d7a4a73d9099fc153204f40.png)

[Duomly — programming online courses](https://www.duomly.com)

本文原载:[https://www . blog . duomly . com/whats-new-in-ecmascript-2019-es 2019/](https://www.blog.duomly.com/whats-new-in-ecmascript-2019-es2019/)

自 2015 年以来，每年都会发布新的 ECMAScript 功能。创建一个新的 ECMAScript 标准有四个阶段，从阶段 0 到阶段 3 主要是规划和起草，阶段 4 是最终阶段。可以使用谷歌 Chrome 版本 72 的最新功能。让我们看看 ES2019 到底给我们带来了什么:

*   Object.fromEntries()，
*   trimStart()和 trimEnd()，
*   平()，
*   flatMap()，
*   符号对象描述属性，
*   捕捉随意的狂欢，
*   格式良好的 JSON.stringify，

让我们一个接一个地检查我们如何使用这个出色的新特性。

# 1.object . from entries()；

在 Javascript 中转换数据是非常常见的，几乎 ECMAScript 的每次更新都带来了新的简单方法。在 ES2017 中，creators 引入了一个新方法 Object.entries()，用于将对象转换为数组。但是没有相反的方法，用一个简单的方法将键值对数组转换成一个对象。

在这里，ES2019 为我们带来了新功能 Object.fromEntries()，它允许将键值对数组转换为对象。

让我们看看如何使用 ES2019 新功能 Object.fromEntries()恢复 Object.entries 方法:

```
var car = {
make: 'Volvo',
seats: 4,
year: 2018,
}console.log(Object.entries(car)); // [['make', 'Volvo], ['seats', 4], ['year', 2018]];var carArr = [['make', 'Volvo], ['seats', 4], ['year', 2018]];
console.log(Object.fromEntries(carArr)); // { make: 'Volvo', seats: 4, year: 2018 };
```

从上面的代码中，我们可以看到，现在反转 Object.entries 结果是非常容易的。

# 2.trimStart()和 trimEnd()

在前端使用 Javascript 时，字符串操作非常常见。有时我们需要调整字符串到需要的格式，我们需要修剪字符串的一部分。ES2019 为我们带来了两个有用的功能:trimStart()和 trimEnd()。trimStart()方法从字符串的开头删除空格，trimEnd()显然从字符串的结尾修剪空格。

让我们来看一个代码示例:

```
var string = '    string to trim     ';console.log(string.trimStart()); // 'string to trim     '
console.log(string.trimEnd()); // '    string to trim'
```

这个看起来很简单，对吧？

# 3.扁平()

新数组。flat()方法创建一个新数组，其中的子数组元素以递归方式集中到特定深度。默认情况下，深度为 1，但可以将其设置为一个参数，也可以将其设置为无穷大，以确保所有的子数组都将被展平。

让我们看一个代码示例:

```
var array = [1, 2, [4, 4, [4, 7, [20, 1]]]];console.log(array.flat()); // [1, 2, 4, 4, [4, 7, [20, 1]]]; 
console.log(array.flat(2)); // [1, 2, 4, 4, 4, 7, [20, 1]];
console.log(arra.flat(Infinity)); // [1, 2, 4, 4, 4, 7, 20, 1];
```

在的第一种用法中。flat()方法，它只集中一个级别数组，因为这是默认深度。在另一个 console.log 中，我定义了两个深度，它集中了两级子数组，最后一个 console.log 我们将深度级别设置为无穷大，它集中了数组的所有级别。

# 4.平面地图()

如果你经常使用数组，你就会知道它有多有用。map()方法，该方法允许遍历数组并对每个元素执行任何函数。的。flatMap()方法是。map()和。扁平()。

让我们来看一个代码示例:

```
var array = [1, 23, [10, 43]];console.log(array.flatMap(item => item + 2)); // [3, 25, 12, 45];
```

这似乎是一个非常有用的方法。

# 5.符号对象描述属性

符号是 ES6 中引入的新功能。符号中的描述主要是为了调试目的而添加的，但直到现在，开发人员都必须将其转换为字符串才能通过 console.log 访问描述。现在，从 2019 年开始，可以通过 Symbol.description 访问描述。

```
var symbolDesc = 'This is Symbol';
var symbolObj = Symbol(symbolObj);console.log(symbolObj.description); // 'This is Symbol';
```

# 6.捕捉可选绑定

许多开发人员使用 try…catch 语句，直到现在还需要在 catch 中使用绑定来避免语法错误，但在某些情况下并不使用。让我们来看看 ES2019 之前的代码示例:

```
try {
  ...
} catch (value) {
  ...
}
```

ES2019 之后:

```
try {
  ...
} catch {
  ...
}
```

在上面的例子中，我们可以看到 catch 语句中的 value 是不需要的，在这种情况下，使用最新版本的 ECMAScript 很可能会跳过它。

# 7.JSON.stringify 超集

在使用 Javascript 时，JSON.strignify 的用法很常见。ES2019 中的这一更改是为了防止 JSON.stringify()方法返回格式错误的 Unicode 字符串。现在 JSON.stringify()将代理代码点表示为字符串，当我们使用 JSON.parse()时，它将被转换为原始表示。

让我们看一些例子:

```
JSON.stringify('\uDF06\uD834'); // '"\\udf06\\ud834"'
JSON.stringify('\uDEAD'); // '"\\udead"'
```

# 结论

ES2019 为我们带来了许多有用的功能，这将使我们的开发过程更加愉快。在本文中，我介绍了最重要的几个:Object.fromEntries()、trimStart()和 trimEnd()字符串方法、flat()和 flatMap()数组方法、Symbol 的新描述属性、catch 可选绑定和 JSON.stringify()超集。

如果您不熟悉前几年的任何更新，我鼓励您查看我们的上一篇文章[自 ES6](https://www.blog.duomly.com/the-most-useful-features-in-the-latest-javascript-since-es6/) 以来最新 Javascript 中最有用的特性，并开始使用最新特性开发您的代码。

好好编码！

![](img/6a75cb93b3344450196d0222c0edeefb.png)

[Duomly — programming online courses](https://www.duomly.com)

感谢您的阅读。

这篇文章是由我们的队友安娜创作的。