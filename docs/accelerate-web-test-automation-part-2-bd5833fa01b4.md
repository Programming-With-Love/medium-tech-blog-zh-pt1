# 加速 Web 测试自动化，第 2 部分

> 原文：<https://medium.com/walmartglobaltech/accelerate-web-test-automation-part-2-bd5833fa01b4?source=collection_archive---------5----------------------->

![](img/f9d275731f9233c0c109e6e352874f78.png)

Photo Credit: [WikiImages](https://pixabay.com/en/rocket-launch-rocket-take-off-nasa-67646/)

在 [**加速 web 测试自动化，第 1 部分**](/walmartlabs/accelerate-web-test-automation-part-1-e574f31938d1) 中，我们已经讨论了如何通过异步调用来检查元素是否可操作，从而将 Web 测试自动化的性能提高了 **31.7%** 。

在本文中，我们将介绍一些其他的性能改进，以减少注入 JavaScript 的昂贵方法调用的数量。

# 返祖

在第 1 部分中，我们介绍了 JavaScript 注入作为我们构建的库的核心概念([https://github.com/TestArmada/nightwatch-extra](https://github.com/TestArmada/nightwatch-extra))。选择 jQuery 作为第一个注入的 JavaScript 是因为它完美的浏览器兼容性。创建自己的 JavaScript 元素检测库的一个常见问题是验证其浏览器兼容性的成本。有时它太复杂(或者在某种程度上不可能)以至于不能为你需要的所有浏览器工作。jQuery 很好地解决了这个问题。

多亏了 jQuery，我们不用担心浏览器的兼容性。越来越多易于使用和用户友好的命令和断言是基于 JavaScript 注入机制创建的。我们的顾客对图书馆很满意。我们也很高兴看到这个库被越来越多的用户采用。

# 不完美

很长一段时间以来，我们一直在使用 jQuery 享受生活。不幸的是，每个硬币都有两面。就功能而言，jQuery 无疑是完美的。但是我们有点担心压缩 jQuery 文件的大小( **84KB，**3 . 1 . 0 版)。

这个 84KB 的大小对于桌面浏览器来说并不是问题。Selenium 对注入的 JavaScript 的大小没有任何限制，所以一切都很完美。然而，同样的规则不适用于移动浏览器。S [elendroid](http://selendroid.io/) 的字符串参数有一个大小限制，即 **65KB** 。为了使相同的库适用于移动浏览器，我们必须将一个 JavaScript 注入调用分成两个调用，以便每次字符串的大小都在 **65KB** 的限制内。

因此，为了使我们的库与移动自动化兼容，JavaScript 注入请求的数量增加了一倍。现在是调整一些东西时候了。

# 替代方案

在对如何将 JavaScript 注入调用的数量减少到一个做出任何结论之前，我们需要知道 jQuery 在我们的库中真正做了什么。

jQuery 是一个丰富的 JavaScript 库，但是我们只使用了其中的 1%。我们所需要的是 jQuery 伪函数，它们丰富了 CSS3 选择器，如 *:visible* , *:eq(n)* 等。嗯，你应该知道 jQuery 已经剥离了它的 CSS 选择器，并把它放到了一个单独的库 Sizzlejs(【https://sizzlejs.com/】T4)中。

Sizzlejs 很整洁。才 **19KB** (压缩版 2.3.1)。如果我们可以证明 Sizzlejs 对于我们的情况足够好，那么 **84KB** -to- **19KB** 的改变不仅减少了 JavaScript 注入调用的数量，还可以加快注入速度。JavaScript 注入调用的速度确实与注入的字符串大小有关，如果用户的网络连接速度较慢，速度可能会加快。

## 代码检查

现在是时候检查我们的代码了。我们想在这里检查两件事:

1.  如果我们使用的所有 jQuery 伪都在 Sizzlejs 中有效。
2.  如果我们使用任何 jQuery 伪代码来操作 html 元素，比如点击或者移动光标到一个元素。

结果相当令人鼓舞

1.  Sizzlejs 不支持两个 jQuery 伪函数*:可见*和*:隐藏*。然而，它们很容易被定制的伪代码移植到 Sizzlejs 中。
2.  没有使用任何 jQuery 伪代码来操作 html 元素。我们依靠 Selenium 方法来进行元素操作。

## 代码更改

我们需要做的唯一改变是将 jQuery 中的*:可见*和*:隐藏*的实现移植到 Sizzlejs 中。代码示例:

```
sizzleRef.selectors.pseudos.visible =
 function (elem) {
   return elem.offsetWidth > 0 
          || elem.offsetHeight > 0 
          || elem.getClientRects().length > 0;
 };sizzleRef.selectors.pseudos.hidden =
  function (elem) {
    return !sizzle.selectors.pseudos.visible(elem);
  };
```

# 更多改进

## 改进一

现在，我们准备从 jQuery 迁移到 Sizzlejs。http 调用的数量已经成功地恢复为 1。用户不会感觉到任何不同，因为迁移是无缝的。

但是，我们能改进更多吗？

我们曾经每次都评估注入的 jQuery，以确保 jQuery 在被使用之前总是存在，方法是执行 *eval()* 。

```
var executeSizzlejs = function(jQuerySource){// jQuerySource is the string containing compressed jQuery.min.js
  eval(jQuerySource);
}
```

但是， *eval()* 有点慢。我们调用的 eval() 越多，js 注入就会越慢。

既然 jQuery 中使用 Sizzlejs 作为选择器，那么是不是说如果目标页面内置了 jQuery 就完全不需要注入了呢？答案是**是的**。查看以下代码:

> [https://github . com/jquery/jquery/blob/master/src/selector-sizzle . js # L8](https://github.com/jquery/jquery/blob/master/src/selector-sizzle.js#L8)

说到这里，我们甚至可以完全避免运行 *eval()* 。代码示例:

```
if (window.jQuery) {
  sizzleRef = window.jQuery.find;
}
```

## 改进二

我们的大多数 web 测试自动化命令和断言都是在同一个页面上执行的。与上面的**改进一**类似，如果在页面上检测到 Sizzlejs，则不需要再次注入或评估它，因为页面只有在刷新时才会重新加载。

## 完成改进

```
if (window.Sizzle) {
  // if Sizzlejs is found on page
  sizzleRef = window.Sizzle;
} else if (window.jQuery) {
  // if jQuery is found on page
  sizzleRef = window.jQuery.find;
} else {
  // if none is found on page
  eval(pSizzleSource);
  sizzleRef = Sizzle;
  sizzleRef.noConflict();
  sizzleRef.selectors.pseudos.visible =
    function (elem) {
      return elem.offsetWidth > 0 
             || elem.offsetHeight > 0 
             || elem.getClientRects().length > 0;
    };
  sizzleRef.selectors.pseudos.hidden =
    function (elem) {
      return !sizzle.selectors.pseudos.visible(elem);
    };
  window.Sizzle = sizzleRef;
}
```

完整的代码可以在这里找到:

> [https://github . com/testar mada/night watch-extra/blob/master/lib/injections/js-injection . js](https://github.com/TestArmada/nightwatch-extra/blob/master/lib/injections/js-injection.js)

# 结论

通过本文中的性能改进，我们已经成功地移除了一些不必要的 http 调用，并尽可能多地将页面上昂贵的 javascript *eval()* 用于 javascript 注入。请在评论里给我留言，分享你的经历。也欢迎对该系列的任何评论。如果你想让我为这篇文章写点什么，请告诉我。

我们仍在学习和成长。和我们在一起。

*未完待续……*