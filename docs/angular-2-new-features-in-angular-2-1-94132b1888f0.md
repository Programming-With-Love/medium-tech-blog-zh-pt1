# Angular-Angular 2.1 中的新功能

> 原文：<https://medium.com/google-developer-experts/angular-2-new-features-in-angular-2-1-94132b1888f0?source=collection_archive---------0----------------------->

Angular 2.1 中包含的新功能综述

![](img/eab1a59abcb60d782d8832f1906bdaa8.png)

[The Petri Dish Project — the Bio-Infinity series (4), by J.D Doria, 2015](http://xaoss.tumblr.com/post/128170854028/the-petri-dish-project-the-bio-infinity-series)

在这篇文章中，我们将探讨一下 **Angular 2.1** 中的一些新特性。这是一个小升级。

让我们来探索其中的一些特性:

*   新动画别名
*   惰性加载模块预加载程序

和以前的版本一样，它非常容易升级，在 [Angular 博客](http://angularjs.blogspot.com)上可以找到一个突破性变化的列表来指导你的升级过程。

在 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 的我的 feed 里找到最新的 Angular 内容。

也别忘了查看这篇覆盖 **Angular 2.3** 发布的博文。

[](/@gerard.sans/angular-2-new-features-in-angular-2-3-f2e73f16a09e) [## Angular-Angular 2.3 中的新功能

### Angular 2.3 中包含的新特性综述:组件继承、ViewContainer、Angular 版本和语言…

medium.com](/@gerard.sans/angular-2-new-features-in-angular-2-3-f2e73f16a09e) 

# 新动画别名

这是一个添加了**的功能:输入**和**:留下**别名用于动画过渡。作为一个例子，让我们使用下面这个**切换**动画定义。

```
animations: [
  trigger('toggle', [
    state('show', style({ opacity: 1; })),
    state('void', style({ opacity: 0; })),
    transition('**void => ***', animate('500ms ease-in-out')),
    transition('*** => void**', animate('500ms ease-in-out'))
  ])
],
```

> ***** 是匹配任何状态的特殊状态。 **void** 也是一种特殊状态，由视图中当前未呈现的元素表示。

在 **Angular 2.1** 中，我们可以使用新的别名重构之前的代码，使代码更容易理解。

```
animations: [
  trigger('toggle', [
    state('show', style({ opacity: 1; })),
    state('void', style({ opacity: 0; })),
    transition('**:enter**', animate('500ms ease-in-out')),
    transition('**:leave**', animate('500ms ease-in-out'))
  ])
],
```

这段代码的演示可以在这里找到[。](https://plnkr.co/edit/oydh0yvV27hlQPA8wEYP?p=preview)

你可以阅读这篇介绍 Angular 动画的博客文章。

[](http://blog.thoughtram.io/angular/2016/09/16/angular-2-animation-important-concepts.html) [## 角度动画-基础概念

### 在我们即将于 11 月举行的公开培训中学习 Angular！现在注册动画功能往往是可怕的目标…

blog.thoughtram.io](http://blog.thoughtram.io/angular/2016/09/16/angular-2-animation-important-concepts.html) 

# 惰性加载模块预加载程序

Angular 引入了**延迟加载**，这样我们就可以很容易地决定应用程序中的哪些模块可以按需加载。

惰性加载是一个非常强大的特性，但是按需加载大型模块会花费一些时间，这不利于用户体验。

在使用**路由器预加载器**的 **Angular 2.1** 中，我们可以通过**在后台预加载惰性加载模块来改进默认惰性加载设置，而不会中断用户**。一旦用户最终决定导航到一个惰性加载模块，导航将是即时的。

为了选择加入这个特性，我们需要在我们的**根** **路由**定义中添加一个标志。

```
@NgModule({  
  bootstrap: [App],  
  imports: [ 
    RouterModule.forRoot(ROUTES, { 
      **preloadingStrategy: PreloadAllModules**
    })
  ]
}) class AppModule {}
```

在上面的例子中，我们使用了 **PreloadAllModules** 策略，这将预加载所有定义的惰性加载模块。如果我们想要更多的控制，我们也可以使用自定义预加载策略。

想了解更详细的概述，请阅读 Victor Savkin 的这篇博文。

[](https://vsavkin.com/angular-router-preloading-modules-ba3c75e424cb) [## 角度路由器:预加载模块

### 惰性加载通过将应用程序分成多个包，并按需加载，加快了应用程序的加载时间。我们…

vsavkin.com](https://vsavkin.com/angular-router-preloading-modules-ba3c75e424cb) 

这一增加无疑改善了惰性加载的用户体验。

仅此而已！觉得我错过了一些功能？请通过 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 或 gerard.sans_at_gmail.com 联系我。感谢您的阅读！

[](http://www.meetup.com/AngularZone/) [## 安古拉宗社区

### 欢迎来到我们的社区。我们的激情是有棱角的。加入我们吧！🚀](http://www.meetup.com/AngularZone/) [![](img/abefda0aa7864742686ec7f7fdffe2b5.png)](https://twitter.com/intent/user?screen_name=gerardsans)![](img/e1c4484ca50314e5e2d9314338b129d6.png)