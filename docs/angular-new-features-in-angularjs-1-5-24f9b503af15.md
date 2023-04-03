# Angular-Angular 1.5 中的新功能

> 原文：<https://medium.com/google-developer-experts/angular-new-features-in-angularjs-1-5-24f9b503af15?source=collection_archive---------1----------------------->

对未来 Angular 1.5 版本中包含的新功能的回顾。

![](img/b1e6129c7eaadb0cef95be8aa8749c58.png)

Ferrofluid picture by Linden Gledhill

> 在 Angular 1.5 rc.1 发布后审查([15–1 月](https://github.com/angular/angular.js/blob/master/CHANGELOG.md#150-rc1-quantum-fermentation-2016-01-15))

在这篇文章中，我们将会看到 **Angular 1.5** 的一些新特性。这是自去年 7 月以来的一次重大升级。

让我们来探索其中的一些特性:

*   组件定义
*   多时隙交叉
*   ng-animate-swap(旋转横幅)
*   惰性跨集群(性能提升)

和以前的版本一样，它非常容易升级，在 [Angular 博客](http://angularjs.blogspot.com)上可以找到一个突破性变化的列表来指导你的升级过程。

在 Twitter 上关注我的最新动态 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 。

# 组件定义

此功能将组件的概念引入 Angular 1。组件是 Angular 2 架构的构建模块，所以现在开始熟悉我们的应用程序中的 Angular 2 风格是有意义的。

这只是一个围绕 ***模块的 helper 方法。directive*** 特定于 UI 元素，带有一些合理的默认值，如 ***controllerAs、*** ***范围隔离*** 和***bindToController***。这是一个非常好的补充，因为它消除了创建通用指令时的许多样板文件。

看看它与下面的旧指令相比如何。

```
// Angular 1.5
**module.component(name, options);**// Angular 1.4
module.directive(name, fn);
```

让我们以一个名为 ***卡片的 UI 组件*** 为例，用一个 ***标题*** 属性和内容如下:

```
<card-component title="Mariah Carey">
  All I Want for Christmas Is You
</card-component>
```

我们可以使用 ***绑定*** 属性轻松链接该属性。在模板上，我们可以使用 *$ctrl* 作为默认值，或者使用 ***controllerAs*** 定义一个别名。

上面的代码肯定没有下面的指令那么冗长。

你可以在这里探索上面[的例子，或者另外看看 Angular](http://plnkr.co/edit/rHqpiEPBYMgyDODzqKgq?p=preview) [源代码](https://github.com/angular/angular.js/blob/d98c5f03a4cee88a3f0a383c217e3b2f84bbaa25/src/ng/compile.js#L1160-L1199)。

# 多时隙交叉

这个特性实现了一个新层次的交叉，我们可以针对多个占位符或位置。这使我们在创建指令时更加灵活。

让我们看看如何翻译一个基本的 transclusion 示例，后面跟着一个多插槽示例。

## 简单交叉

在 Angular 1.4 中，我们需要激活 ***transclude*** 属性。除此之外，我们使用一个绑定到 ***标题*** 属性的隔离范围。

```
<!--  Angular 1.4\. Simple Transclusion -->
<card title="Mariah Carey">
  All I Want for Christmas Is You
</card><!--  Angular 1.4\. Default transclusion -->
<card></card>
```

为了获得默认值，我们可以使用 ***ng-transclude*** 的内容，如下所示(第 15 行)。

## 多时隙交叉

为了应用这个新特性，我们使用类似于隔离作用域的 ***transclude*** 属性。

```
<!-- Angular 1.5\. Multislot Transclusion -->
<multislot-card>
  <card-title>Mariah Carey</card-title>
  <card-song>All I Want for Christmas Is You</card-song>
</multislot-card>
```

在右侧，我们定义了一个与 camelCase 中的 transclusion 插槽相对应的别名。

> *👮*问号表示可选插槽。否则，该插槽是必需的，如果不可用，将会引发错误。

在模板上，我们可以使用别名插入交叉包含的内容。有两种可能的语法。

```
<h3 **ng-transclude="title"**>No title</h3><h3><ng-transclude **ng-transclude-slot="title"**>No title</ng-transclude></h3>
```

当元素没有内容时，可以如上所示添加默认内容。

```
<!-- Angular 1.5\. Multislot default transclusion -->
<multislot-card></multislot-card>
```

这里可以玩这个例子[。](http://plnkr.co/edit/rHqpiEPBYMgyDODzqKgq?p=preview)

# ng-animate-swap(旋转横幅)

在 Angular 1.4 中，***ngan imate****被完全重构，变得更加灵活、可重用和高性能。现在是时候看看这一努力的成果了，它为我们的动画技术增添了一个强大的元素。*

*[***ng-animate-swap***](https://code.angularjs.org/1.5.0-rc.0/docs/api/ngAnimate/directive/ngAnimateSwap)允许我们轻松实现一个旋转的横幅。为了做到这一点，我们可以挂钩 ng-enter 和 ng-leave CSS 类来设置我们的动画。*

*让我们探索一个实现 MEME 旋转横幅的示例。*

*[![](img/acd8992c3f876b62bbea40978ed29ac6.png)](http://embed.plnkr.co/4jzCEOxg3PwUGiLt221Q/)*

*让我们看一下 HTML 来得到这个结果。*

*我们使用了一个 image 元素，它的 ***ng-src*** 指令指向我们的控制器中定义的图像。使用 ***作为语法*** 这个产生 ***mc.image*** 。[***ng-animate-swap***](https://code.angularjs.org/1.5.0-rc.0/docs/api/ngAnimate/directive/ngAnimateSwap)与 CSS 类结合使用。在这种情况下我们称之为 ***横幅*** 。*

```
*// main syntax
<img ... **ng-animate-swap="mc.image"** class="banner" />// alternative syntax
<img ... **ng-animate-swap** **for="mc.image"** class="banner" />*
```

*在我们的控制器中，我们定义了一个图像数组，使用$interval 每 5000 ms 交替一次。最后，我们确保清除间隔。*

*每次 ***mc.image*** 发生变化，[***ng-animate-swap***](https://code.angularjs.org/1.5.0-rc.0/docs/api/ngAnimate/directive/ngAnimateSwap)会拾取变化并使用下面的 CSS 类触发必要的动画。厉害！*

```
*// First block. Animation for last element if any
.banner.ng-leave {}
.banner.ng-leave-active {}// Second block. Animation for current element if Truthy or 0
.banner.ng-enter {}          
.banner.ng-enter-active {}*
```

> *请注意这与 ngRepeat、ngView、ngInclude、ngSwitch、ngIf 和 ngMessage 中的行为有何不同，在这些行为中，元素仅运行当前元素的其中一个块，具体取决于它是变为 Falsy (ng-leave)还是 Truthy (ng-enter)。*

*请看下面允许图像从左向右滑动的 CSS:*

> **🐒*我们可以使用[***ng-animate-swap***](https://code.angularjs.org/1.5.0-rc.0/docs/api/ngAnimate/directive/ngAnimateSwap)*对任意 DOM 元素使用对底层数据进行修改所触发的一个动画。**

# **惰性跨集群(性能提升)**

**这是在 ***$compile*** 模块中完成的性能改进，可用于所有使用跨包的指令。它的工作原理是推迟对跨包内容的编译，这样只有在必要的时候才会发生。**

**对于使用嵌套 ***ng-if*** 或*【ng-switch】-when*(例如:对象层次结构) ***的需要复杂树的指令，有望获得显著的性能提升。*** 这些现在会执行得更好，因为只有当它们的绑定条件匹配时，才会编译跨包含的内容。这也意味着在初始渲染时，一些部分甚至不需要实际编译。**

**你可以得到这个[提交](https://github.com/angular/angular.js/commit/652b83eb226131d131a44453520a569202aa4aac)的所有技术细节。**

> **👮对于使用 replace:true 和 template 或 templateUrl 的指令，惰性跨语句不可用，但不使用多槽跨语句。**

**觉得我错过了一些功能？通过 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 或 gerard.sans_at_gmail.com 联系我，感谢阅读！**

**好奇 Angular 1.4 引入的一些新功能？请点击下面的链接！**

**[](http://www.meetup.com/AngularZone/) [## 安古拉宗社区

### 欢迎来到我们的社区。我们的激情是有棱角的。加入我们吧！🚀](http://www.meetup.com/AngularZone/) 

# 资源

*   [探索棱角 1.5。组件()方法](https://toddmotto.com/exploring-the-angular-1-5-component-method/)，由**托德座右铭**， [@toddmotto](https://twitter.com/toddmotto)
*   [多重互斥和命名槽](http://blog.thoughtram.io/angular/2015/11/16/multiple-transclusion-and-named-slots.html)，由**Pascal prech**， [@PascalPrech](http://twitter.com/PascalPrecht) t
*   [Angular 1.5 beta 2 发行说明](http://angularjs.blogspot.com.es/2015/11/angularjs-15-beta2-and-14-releases.html)

[![](img/abefda0aa7864742686ec7f7fdffe2b5.png)](https://twitter.com/intent/user?screen_name=gerardsans)![](img/e663fed5134951f693d4c5847836a881.png)![](img/67b37e5d672384f0953563450fca0029.png)**