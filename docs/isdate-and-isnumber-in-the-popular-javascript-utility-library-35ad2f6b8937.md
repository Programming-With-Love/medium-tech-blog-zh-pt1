# 流行的 Javascript 实用程序库中的 isDate 和 isNumber

> 原文：<https://medium.easyread.co/isdate-and-isnumber-in-the-popular-javascript-utility-library-35ad2f6b8937?source=collection_archive---------3----------------------->

## 如果不知道它是什么，很容易误用它

![](img/e9ac4150fd928e0c3b5fc2a269268bf0.png)

Photo by [Austris Augusts](https://unsplash.com/@austris_a?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/number?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

您可能经常为您的应用程序使用 javascript 实用程序库。或者是为了加快你的开发过程，或者只是想要一些已经被证明有效并经过测试的实用功能。我们知道的一些流行的实用程序库是 [lodash](https://lodash.com/) 和[下划线](https://underscorejs.org/)。

你曾经使用过工具库来验证一个变量是否是一个有效的数字吗？Valid 表示该值不会导致错误或者不会返回意外的结果，比如`**NaN**`。如果您有 lodash 或下划线依赖项，您可能会尝试使用`**isNumber**`函数。它似乎就是这么做的，对吗？

让我们看看`**isNumber**`函数的 lodash 和下划线实现。

[https://github.com/jashkenas/underscore/blob/e51aa7251f3e010fe003fbb9d969a74b9dcda103/underscore.js#L1325-L1330](https://github.com/jashkenas/underscore/blob/e51aa7251f3e010fe003fbb9d969a74b9dcda103/underscore.js#L1325-L1330)

[https://github.com/lodash/lodash/blob/04ebca6c86deba0ff733847f6c11fd5265e1ce03/isNumber.js#L29-L32](https://github.com/lodash/lodash/blob/04ebca6c86deba0ff733847f6c11fd5265e1ce03/isNumber.js#L29-L32)

这两个流行的库都有相似的实现。他们将变量 Javascript 类名类型与`**[object Number]**`字符串进行比较。这不仅适用于数字类型，也适用于其他类型，如日期、函数等。

你可能认为这没什么，但是你知道`**NaN**`在 Javascript 中也是一个数字吗？[JavaScript 中的 NaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN) 是表示值的数字:`**Not-A-Number**`。有点困惑，对吧？您可以在 javascript 控制台中亲自查看。

```
toString.call(NaN)
// -> [object Number]
```

因此，对于上述用例来说，使用`**isNumber**`方法并不是正确的方式。我们需要另一个验证，即检查数字是否不是`**NaN**`。我们最终会得到这样的结果。

```
const isValidNumber = _.isNumber(obj) && !isNaN(obj)
```

*那么* `***isDate***` *功能怎么样呢？什么可能导致问题？*

尝试初始化无效的日期对象，如`**new Date("31/1/2019")**`或`**new Date("test123")**`。结果值仍将是一个日期对象，但它将是一个无效的日期对象。这将很可能导致你的日期相关的逻辑有缺陷。因此，我们应该添加另一个步骤来验证日期对象，这可能就足够了:

```
const isValidDate = _.isDate(obj) && !isNaN(obj.getTime()) 
```

概述:

*   lodash 和下划线中的类型检查函数只检查类型是否是正确的 Javascript 类型，但不验证值(请阅读相应的文档)
*   `**NaN**`是 Javascript 中的一个数字
*   用无效字符串初始化新的日期对象仍然会返回一个日期对象