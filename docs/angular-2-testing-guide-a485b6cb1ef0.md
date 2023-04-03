# 角度—测试指南(v4+)

> 原文：<https://medium.com/google-developer-experts/angular-2-testing-guide-a485b6cb1ef0?source=collection_archive---------0----------------------->

使用 TestBed、fixtures、async 和 fakeAsync/tick 的九个易于理解的示例。

![](img/a777f90602e08b2079df40dc60d20dd2.png)

[Fluidscapes](http://www.syedrezaali.com/#/fluidscapes/) (Reza Ali)

在这篇文章中，我们想涵盖最常见的单元测试，如:组件、服务、Http 和管道；还包括一些不太为人所知的领域，如:指令、路由器或可观察对象。我们将提供设计的示例，以便您可以使用它们作为快速参考。不要错过 [**测试清单**](#8ea4) 来帮助你创建你自己的测试。

从 RC 到 final，测试已经改进了很多，在迁移到 NgModule 之后甚至更多。Angular 核心团队努力减少样板文件，不仅能够使用 Jasmine，还能够使用其他测试框架，如 Mocha。

为了从测试中获得最佳效果，您应该了解一些新的 API。我们将涵盖:

*   **茉莉**简介:[套件](#b343)、[规格](#b343)、[期望](#b343)和[匹配器](#b343)。
*   **角度测试** : [设置](#e122)、[依赖注入](#4625)和[测试检查表](#8ea4)。
*   **测试实用程序** : [测试床](#4625)，[注入](#4625)，[夹具](#8d61)，[异步](#4625)， [fakeAsync/tick](#6134) 和 jasmine.done。
*   **测试实例** : [组件](#8d61)，[服务](#d889)， [Http](#b1fc) 和 [MockBackend](#f7c7) ， [Directives](#6134) ， [Pipes](#2626) ， [Routes](#97c2) ， [Observables](#9fa4) 和 [EventEmitter](#3882) 。

要测试所有的 **26 规格**或更改代码，可以使用这个 [Plunker](https://plnkr.co/edit/jm6T17qPbzM8abmRMckw?p=preview) 。

[](http://slides.com/gerardsans/ng-stockholm-testing-recipes) [## Gerard Sans 的角度测试方法和最佳实践(v2+)

### 在本次演讲中，我们将介绍开发坚如磐石的角度应用时最常见的测试场景…

slides.com](http://slides.com/gerardsans/ng-stockholm-testing-recipes) 

在 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 的我的订阅中找到最新的角度内容。

> 我们使用了[茉莉](http://jasmine.github.io/)，但是既然 Angular final 你也可以使用其他测试框架，比如[摩卡](https://mochajs.org/)。

# 茉莉简介

Jasmine 是来自 [Pivotal Labs](http://pivotal.io/labs) 的开源测试框架，它使用行为驱动的符号，带来流畅和改进的测试体验。

## 主要概念

*   Suites — ***describe(string，function)****functions，取一个标题和一个包含一个或多个规格的函数。*
*   *Specs — ***it(string，function)*** functions，取一个标题和一个包含一个或多个期望的函数。*
*   *期望——是评估为*真*或*假*的断言。基本语法为 ***expect(实际)。****
*   *匹配器—是通用断言的预定义助手。例如: ***toBe(预期)******to equal(预期)*** 。点击查看完整列表[。](https://github.com/JamieMason/Jasmine-Matchers)*

> *注意**。toEqual()** 做深度比较而**。toBe()** 只是一个引用等式。*

## *安装和拆卸*

*Jasmine 提供了四个处理程序来添加我们的安装和拆卸代码:每个 之前的***；每个规格执行一次的*** 之后的**；每个套件执行一次的 ***之前的*** 、 ***之后的*** 。***

> *使用 beforeEach 和 afterEach 在每个规范之前和之后进行更改*

*在我们的规范中避免代码重复的一个好方法是在设置中重构重复的代码。*

# *角度测试*

## *测试设置*

*设置环境的选项很少。你可以使用来自[独立发行版](https://github.com/pivotal/jasmine/releases)的 Jasmine*SpecRunner.html*来开始或创建你自己的。你也可以把它和一个类似 [Karma](https://karma-runner.github.io) 的测试者整合在一起。我们不会涵盖所有组合，因为我们对实际测试更感兴趣。*

> *此设置仅供参考，仅适用于 Plunker。*

*我们加载 Jasmine 依赖项，后面是 Angular 依赖项。我们正在使用一个 **System.js** 和 **TypeScript** 设置。我们使用 *Promise.all()* 一次性加载我们的规范，一旦所有规范都可用，我们通过调用 **onload** 手动触发 Jasmine 测试运行程序。*

## *依赖注入*

***TestBed** ，类似于@NgModule 帮助我们建立测试的依赖关系。我们调用**testbed . configuretestingmodule**传递我们的配置。此信息将用于解决任何依赖关系。下面我们可以看到一个例子:*

```
*@NgModule({
  declarations: [ **ComponentToTest** ] 
  providers: [ **MyService** ]
}) 
class AppModule { }TestBed.configureTestingModule({
  declarations: [ **ComponentToTest** ],
  providers: [ **MyService** ]  
});//get instance from TestBed (root injector)
let service = TestBed.get(**MyService**);*
```

***注入**，允许我们在测试层获得依赖关系。*

```
*it('should return ...', **inject**([MyService], service => { 
  service.foo();
}));*
```

***组件注入器**，允许我们在组件级别得到一个依赖。*

```
*@Component({ 
  providers: [ **MyService** ] 
}) 
class **ComponentToTest** { }let fixture = TestBed.createComponent(**ComponentToTest**);
let service = fixture.debugElement.injector.get(**MyService**);*
```

*对于在组件级定义的依赖关系，我们需要使用如上所示的组件注入器。TestBed.get 或 inject 不起作用。*

*让我们看看如何将 **TestBed** 与*languageservice*组件一起使用:*

*首先，我们用**testbed . configuretestingmodule**加载测试所需的依赖项。然后在我们的规范中，我们使用 [*注入*](https://angular.io/docs/ts/latest/api/testing/inject-function.html) 来自动实例化每个依赖项。*

*我们现在可以重构*注入*，这样我们就不会为每个规范重复它。*

*让我们看两个实例化组件的例子。第一个例子是同步的，它创建了一个 fixture，包含一个实例 **MyTestComponent** 。*

```
*// synchronous
  beforeEach(() => {
    fixture = TestBed.createComponent(**MyTestComponent**);
  });*
```

*第二个例子是异步的**，**因为使用外部模板和 css，需要 XHR 调用。*

```
*// asynchronous 
  beforeEach(**async**(() => {
    TestBed.configureTestingModule({
      declarations: [ **MyTestComponent** ],
    }).**compileComponents**(); // compile external templates and css
  }));*
```

*当依赖关系涉及异步处理时，我们可以使用[*async*](https://angular.io/docs/ts/latest/api/core/testing/index/async-function.html)*。这将在内部创建一个区域，并处理任何异步处理。**

> **根据您的构建设置，您的模板和 css 可能都是内联的，因此您可以安全地使用同步方法**

## **测试清单**

*   ****哪种测试**？[孤立](https://angular.io/docs/ts/latest/guide/testing.html#!#isolated-unit-tests)、[浅](https://angular.io/docs/ts/latest/guide/testing.html#!#shallow-component-test)或[综合测试](https://vsavkin.com/three-ways-to-test-angular-2-components-dcea8e90bd8d)。**
*   **我可以使用模拟、存根或间谍吗？依赖关系应该被他们自己的测试所覆盖。使用它们可以提高你的测试，而不会失去效力。**
*   ****同步还是异步**？你的测试进行异步调用了吗？使用 XHR、承诺、可观测量等。组件使用的是 TemplateUrl 还是 styleUrls 还是 inline？确保您使用的是相应的 API。**

**让我们看看如何测试应用程序的不同构件。为了简洁起见，我们将跳过所有必需的 ***导入*** 。我们在必要时添加了注释。他们可以在这里找到。**

# **测试示例**

## **测试组件**

**让我们来看一个简单的组件，它使用一个 [@ *Input()*](https://angular.io/docs/ts/latest/api/core/Input-var.html) 属性来呈现一个*问候*消息。**

**为了测试该组件，我们将使用一个通用设置，该设置使用*试验台*。**

> **使用 TestBed 加载相应的依赖项，以便它们在测试期间可用**

**通常的做法是在每个之前使用*来重构我们的测试。通过这样做，我们避免了为每个测试*重复一些代码，比如*注入*。这也将简化我们的规格。****

我们使用*testbed . create component*来创建我们的 *Greeter* 组件的一个实例。组件实例可在[夹具](https://github.com/angular/angular/blob/a7e9bc97f6a19a2b47b962bd021cb91346a44baa/modules/angular2/src/testing/test_component_builder.ts#L31)中访问。这是它的主要 API:

```
abstract class [ComponentFixture](https://github.com/angular/angular/blob/a7e9bc97f6a19a2b47b962bd021cb91346a44baa/modules/angular2/src/testing/test_component_builder.ts#L31) {
  debugElement;       // test helper 
  componentInstance;  // to access properties and methods
  nativeElement;      // to access DOM element
  detectChanges();    // trigger component change detection
}
```

我们使用 *name* 属性设置一个值，使用 **detectChanges** 触发变化检测，并使用 **whenStable** 检查所有异步调用结束时的预期结果。为了访问呈现的文本，我们使用了两种不同的 API，通过 CSS 选择器进行过滤(第 22–23 行)。

> debugElement 的其他查询有:query(by . all())query(by . directive(my directive))

## 测试服务

*LanguagesService* 只有一个方法返回应用程序可用语言的数组。

类似于我们之前的例子，我们使用 *beforeEach 实例化服务。*正如我们所说，这是即使我们只有一个规格，这也是一个很好的实践。在这种情况下，我们正在检查每种语言和总计数。

## 使用 Http 进行测试

我们通常不希望在测试过程中进行 HTTP 调用，但是我们将显示它作为参考。我们已经将最初的服务 *LanguageService* 替换为 *LanguageServiceHttp。*

在这种情况下，它使用 *http.get()* 来读取一个 JSON 文件。然后我们使用[*observable . map()*](https://github.com/ReactiveX/RxJS/blob/master/src/operator/map.ts)*使用 *json()将响应转换成最终结果。**

*我们的测试看起来与前一个非常相似。主要的区别是由于订阅，我们使用了异步测试，就像我们对组件所做的那样。*

> *注意，如果涉及 XHR 呼叫，您不能使用 *fakeAsync* 。这是由[设计的](https://github.com/angular/angular/issues/8280)。*

*返回一个我们可以订阅的可观察值。我们将在后面更详细地讨论可观察到的现象。*

## *使用模拟后端进行测试*

*更明智的方法是用一个 *MockBackend* 代替 HTTP 调用。为了做到这一点，我们可以使用*提供*(第 10 行)*。这个**

**在我们的测试中，我们构建了模拟响应(第 23–25 行),所以当我们最终调用我们的服务时，它会得到预期的结果。**

> **注意:我们不需要异步，因为 MockBackend 的行为是同步的。感谢[帕斯卡·普雷希特·ʕ•̫͡•ʔ](https://medium.com/u/32a0d1331713?source=post_page-----a485b6cb1ef0--------------------------------)指出这一点。**

## **测试指令**

**Angular 中的指令是一种特殊类型的组件，通常没有附带视图。我们将使用一个[属性指令](https://angular.io/docs/ts/latest/guide/attribute-directives.html)、 *logClicks、*来记录我们在**、*主机元素*、**上点击了多少次，这样您就可以理解了。**

**为了测试这个指令，我们决定创建一个*容器*组件。我们将对它进行设置，使它充当我们的主机，再现由我们的指令发出的事件。**

**我们在每个之前使用*来将创建组件的逻辑与测试分开。该器件现在可用于所有规格。***

**为了触发容器上的**点击**，我们使用了 DOM API(推荐)。我们也可以使用*fixture . debug element . triggereventhandler(' click ')。***

**在这个测试中，我们使用了 **fakeAsync** 和 **tick** 。使用 **fakeAsync** 所有的异步处理将被暂停，直到我们调用 **tick** 。这给了我们更大的控制权，并避免了不得不求助于嵌套的承诺或可观察的块。**

> **使用 fakeAsync/tick 我们可以更好地控制异步代码，尽管它不能用于 XHR。**

## **测试管道**

**管道是可以将输入数据转换成用户可读格式的函数。我们将使用标准的[*string . toupper case*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase)*()*来编写一个自定义的大写管道*。这只是为了简单起见作为有角的有自己的[](https://angular.io/docs/ts/latest/api/common/UpperCasePipe-class.html)**实现。*****

***管道只是可以被注入的普通类，所以我们可以使用 *inject* 非常容易地设置我们的规范。***

**为了测试我们的管道，我们检查了常见的情况:它应该与空字符串一起工作，它应该大写，最后，当不与字符串一起使用时，它抛出。**

> **注意:我们使用了一个箭头函数来捕获 *expect* 中的异常**

## **测试路线**

**路线有时会被遗漏，但这通常被视为复式簿记的良好做法。在我们的示例中，我们将使用一个简单的路由配置，其中只有几条路由和一条指向 home 的路由。**

**如果没有提供重定向到归属的初始路由，则第一个路由定义捕获初始路由(第 14 行)。第二个实例化了 **Home** 组件(第 15 行)；最后一个捕获所有剩余的路由，并将它们重定向到 home(第 16 行)。我们的测试将使用这些路线来检验我们的期望。**

**我们导入了**routertestingmodule . with routes(routes)**，用我们测试的路由初始化 **Router** 实例(第 6 行)。在上面我们测试的代码中，我们可以使用 **async** 、 **asyncFake/tick** 和 **done** 导航到 home。注意我们如何使用 **asyncFake** 编写更好的测试，因为我们不必嵌套承诺处理。**

> **fakeAsync/tick 非常适合不涉及 XHR 的复杂异步测试**

## **测试可观测量**

**可观测量非常适合处理异步任务。它们被用在 Angular 中的几个地方，像 *Http* ，表单控件，验证或者在 *EventEmitter* 后面。我们将使用下面的*观察值*来展示我们如何测试它们的行为。**

**我们创建了一个*可观察的*，它发出 1，2，3 并完成。为了测试它，我们在 subscribe 上设置了 next、error 和 complete 回调。由于下一次回调将被调用几次，我们必须动态地设置我们的期望。**

## **测试事件发射器**

****事件发射器**用于 Angular 组件之间的事件通信。我们创建了一个计数器组件， **Counter** ，，它允许我们增加或减少初始值 0。每次我们这样做的时候，新的值将会使用一个 *EventEmitter* 在*改变*时被公开。**

**设置将非常类似于 Observables。**

**在这种情况下，我们检查是否可以使用 *EventEmitter* 上的 subscribe**增加**或**减少**，因为它公开了一个*可观察对象*。我们通过调用 change 方法触发不同的值，并在下一次回调中检查我们的期望。**

**你可以在这篇文章中找到所有的测试，更多的测试可以在演示中找到。**

**我只有这些了！感谢阅读！有什么问题吗？在 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 给我发短信**

## **想要更多吗？**

**如果您需要更多示例，请随时通过 Gerard _ dot _ sans _ at _ Gmail _ dot _ com 联系我，或者前往 GitHub 中的[角度单元测试](https://github.com/angular/angular/tree/e748adda2e7a1f6e302628d0d76b5c3d1e3fc196/modules/angular2/test)！**

**[](http://www.meetup.com/AngularZone/) [## 安古拉宗社区

### 欢迎来到我们的社区。我们的激情是有棱角的。加入我们吧！🚀](http://www.meetup.com/AngularZone/) 

# 进一步阅读

*   [测试](https://angular.io/docs/ts/latest/guide/testing.html)(正式文件)
*   角度应用的单元测试由**vik ram subra manian**([@ viker man)](https://twitter.com/vikerman)在 [ng-Europe](https://ngeurope.org/) 演讲。[视频](https://www.youtube.com/watch?v=dVtDnvTLaIo) | [幻灯片](https://docs.google.com/presentation/d/1fFxQvx2WHFPqR4piq0oWgKBuSMvrCwc1vfYggHlYEbQ/edit?usp=sharing)
*   [三种测试角度组件的方法](https://vsavkin.com/three-ways-to-test-angular-2-components-dcea8e90bd8d)作者**维克托·萨维金**([@维克托·萨维金](https://twitter.com/victorsavkin))

[![](img/abefda0aa7864742686ec7f7fdffe2b5.png)](https://twitter.com/intent/user?screen_name=gerardsans)![](img/e7feb1af735b3fcdd1c800bfa8958065.png)![](img/67b37e5d672384f0953563450fca0029.png)**