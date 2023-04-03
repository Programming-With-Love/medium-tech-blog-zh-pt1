# Angular —新 HTTP 模块简介

> 原文：<https://medium.com/google-developer-experts/angular-2-introduction-to-new-http-module-1278499db2a0?source=collection_archive---------0----------------------->

使用反应式编程和可观察值的新数据架构

![](img/da5d07559a5f27e4c9473964017ddee6.png)

[Paper.js](http://paperjs.org/) spiral rasterisation plus effects by [gsans](https://ello.co/gsans)

ngular 引入了许多创新:性能改进、组件路由器、锐化依赖注入(DI)、延迟加载、异步模板、服务器渲染(又名 Angular Universal)、编排动画、使用原生脚本的移动开发；所有这些都配备了可靠的工具(多亏了 TypeScript 和新的 Angular CLI)和出色的测试支持。

在本帖中，我们将关注新的 Angular **HTTP 模块**。我们将研究:

*   使用 RxJS 5 的新角度数据架构
*   设置角度/http
*   创建简单的 Http 服务(使用 http.get)
*   基本身份验证场景(使用 http.post)
*   $http 和 angular/http 的区别

Angular 使用**反应式编程**作为其核心构建模块。这是基于异步数据流，又名**可观测数据**。

[演示](https://embed.plnkr.co/r1QFQj0wZ5hgJxrsRoGF/) | [来源](https://plnkr.co/edit/r1QFQj0wZ5hgJxrsRoGF?p=preview)

在 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans) 找到我的最新观点。

# 使用 RxJS 5 的新角度数据架构

JavaScript 的反应式扩展(RxJS)是一个反应式流库，它允许您处理可观察对象。RxJS 结合了**观察值**、**操作符**和**调度器**，因此我们可以订阅流并使用可组合操作对变化做出反应。

棱角团队决定用[***RxJS 5***](https://github.com/ReactiveX/RxJS)代替 [*RxJS 4*](https://github.com/Reactive-Extensions/RxJS) 。这是一次重写，重点是提高性能和降低 API 复杂性，以便于采用。不言而喻，Angular 发布时，第 5 版将取代第 4 版。

> *🐒*角度用途 [RxJS 5](https://github.com/ReactiveX/RxJS) 。在研究特定的操作符和文档时要考虑到这一点。

## 以打字打的文件

在这篇文章中，我们将使用 TypeScript。这是 ES6 的超集。阅读[ES6 简介](/sons-of-javascript/javascript-an-introduction-to-es6-1819d0d89a0f)以熟悉 ES6 的常见功能。

我们将使用[基本类型](http://www.typescriptlang.org/Handbook#basic-types)、[可选参数](http://www.typescriptlang.org/Handbook#functions-optional-and-default-parameters)、[接口](http://www.typescriptlang.org/Handbook#interfaces)和[泛型](http://www.typescriptlang.org/Handbook#generics)。点击链接，了解有关这些功能的更多信息。

# 设置角度/http

为了在我们的组件中使用新的 Http 模块，我们必须导入 **Http** (参见 [ES6 导入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import))。之后，我们可以在构造函数中通过依赖注入来注入它。下面找个例子。

除了使用 **Http** 对我们的组件进行更改之外，我们还需要在引导过程中包含 **HttpModule** 。

在我们的应用程序中，我们使用一个数组(第 8 行)为属性 ***导入 [**ngModule**](https://angular.io/docs/ts/latest/guide/ngmodule.html) 中的*** 在引导期间使 **HttpModule** 可用。注意我们是从:angular/http ( *line 4* )导入的。对于 Angular，Http 不再包含在核心中，而是作为一个单独的文件。

# 创建简单的 Http 服务

让我们创建一个简单的例子，使用一个从 JSON 文件中读取一些数据的服务。

## 新依赖注入

为了使我们的服务对 DI 可用，我们添加了[***@可注射***](https://angular.io/docs/ts/latest/api/core/InjectableMetadata-class.html) 注释(第 5 行)。这指示依赖注入器，该类具有在创建该类的实例时应该注入到构造函数中的依赖项。

> 注释允许我们声明性地将元数据添加到类和属性中，供 Angular 使用。常见的注释有:@Component、@ Injectable、@Pipe 或@NgModule。

我们使用一个类定义了一个简单的服务(第 6 行)。我们还在它前面加上了 [***导出***](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) ，以便其他组件可以访问它。在下面找到用于将 http 注入到构造函数的 Http 参数中的语法。

```
import {**Http**} from ‘@angular/http’;constructor(private http:**Http**){ ... }
```

## Http GET

在构造函数上我们设置了一个公共属性 ***this.http*** 并调用*[***http . get***](https://github.com/angular/angular/blob/8daa9b202d4643e3cd032604b0ae6bb46ea5aaf9/modules/angular2/src/http/http.ts#L118)。我们来看看它的定义。*

```
*http.get(url: string, options?: [RequestOptionsArgs](https://github.com/angular/angular/blob/b0009f03d510370d9782cf76197f95bb40d16c6a/modules/angular2/src/http/interfaces.ts#L29))
  : Observable<[Response](https://github.com/angular/angular/blob/e1d7bdcfe7f25156c8a462452db5367b68a02df6/modules/angular2/src/http/static_response.ts#L26)>interface RequestOptionsArgs {  
  url?: string;  
  method?: string | [RequestMethods](https://github.com/angular/angular/blob/b0009f03d510370d9782cf76197f95bb40d16c6a/modules/angular2/src/http/enums.ts#L6);  
  search?: string | [URLSearchParams](https://github.com/angular/angular/blob/b0009f03d510370d9782cf76197f95bb40d16c6a/modules/angular2/src/http/url_search_params.ts#L28);  
  headers?: [Headers](https://github.com/angular/angular/blob/e1d7bdcfe7f25156c8a462452db5367b68a02df6/modules/angular2/src/http/headers.ts#L37); 
  body?: string;
}*
```

*上面的[***http . get***](https://github.com/angular/angular/blob/8daa9b202d4643e3cd032604b0ae6bb46ea5aaf9/modules/angular2/src/http/http.ts#L118)签名是用打字稿写的，内容如下。我们需要提供一个 url 和一个可选的选项对象，在冒号之后我们找到相应的类型。*

> *ide 可以使用类型信息来提供类似于 Visual Studio 中的 IntelliSense 和类型检查的功能。*

*当提供一个 ***选项*** 对象时，它将遵循 [RequestOptionsArgs](https://github.com/angular/angular/blob/b0009f03d510370d9782cf76197f95bb40d16c6a/modules/angular2/src/http/interfaces.ts#L29) 接口。在我们的例子中，我们没有使用选项参数。最后最后一位表明[***http . get***](https://github.com/angular/angular/blob/8daa9b202d4643e3cd032604b0ae6bb46ea5aaf9/modules/angular2/src/http/http.ts#L118)返回一个 ***可观察*** 发射 [***响应***](https://github.com/angular/angular/blob/e1d7bdcfe7f25156c8a462452db5367b68a02df6/modules/angular2/src/http/static_response.ts#L26) 的对象。*

*你会注意到的第一件事不是返回一个承诺 [***而是返回一个 ***可观察的*** *。*最后我们使用***map****将结果解析成一个 JSON 对象。这段代码使用了一个****](https://github.com/angular/angular/blob/8daa9b202d4643e3cd032604b0ae6bb46ea5aaf9/modules/angular2/src/http/http.ts#L118)****[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)(在 ES6 中)或者胖箭头(在 TypeScript 中也称为 lambda 语法)。让我们看看它如何翻译成 ES5(第 8 行):*****

```
**// TypeScript/ES6
.map(response => response.json())// ES5
.map(function(response){ return response.json(); })**
```

**[***map***](https://github.com/ReactiveX/RxJS/blob/40e9757d751b4328d6f79d8c6ea33adb299d7466/src/operators/map.ts#L17) 的结果也是一个 ***Observable*** 发出一个包含一个人数组的 JSON 对象。**

## **使用返回可观察对象的服务**

**让我们看看如何使用我们刚刚创建的新的***PeopleService***来显示人员列表。参见下面代码的简化版本:**

**我们导入了***PeopleService***(第 1 行)。接下来，在构造函数中，我们使用之前看到的相同语法注入服务(第 8 行)。**

**在 *App* 实例化期间，***peopleservice . get people()****将返回一个可观察值。我们想要显示一个人员列表，这样我们就可以在数据发出时使用[***subscribe***](https://github.com/ReactiveX/RxJS/blob/40e9757d751b4328d6f79d8c6ea33adb299d7466/src/Subscriber.ts#L9)来设置我们的本地 ***people*** 变量。在这种情况下，我们只需将结果数组设置到 *this.people* 中。***

**使用这个[探测器](https://plnkr.co/edit/r1QFQj0wZ5hgJxrsRoGF?p=preview)探索上面的例子。**

# **基本身份验证场景**

**让我们看看如何使用 Angular 实现一个常见的身份验证场景。我们将使用一个来自 [Auth0](https://auth0.com/blog/2015/10/15/angular-2-series-part-3-using-http) 的简化示例，并查看下面的代码。**

## **Http 帖子**

**假设我们从用户提交的表单中收到了*用户名*和*密码*。我们将调用 ***验证*** 登录用户。用户登录后，我们将继续存储令牌，以便在后续请求中包含它。**

```
**http.post(url: string, body: string, options?: [RequestOptionsArgs](https://github.com/angular/angular/blob/b0009f03d510370d9782cf76197f95bb40d16c6a/modules/angular2/src/http/interfaces.ts#L29))
  : Observable<[Response](https://github.com/angular/angular/blob/e1d7bdcfe7f25156c8a462452db5367b68a02df6/modules/angular2/src/http/static_response.ts#L26)>**
```

**上面的[***http . post***](https://github.com/angular/angular/blob/8daa9b202d4643e3cd032604b0ae6bb46ea5aaf9/modules/angular2/src/http/http.ts#L126)签名如下。我们需要提供一个 url 和一个主体，都是字符串，然后可选地提供一个选项对象。在我们的例子中，我们传递修改后的 *headers* 属性。[***http . post***](https://github.com/angular/angular/blob/8daa9b202d4643e3cd032604b0ae6bb46ea5aaf9/modules/angular2/src/http/http.ts#L126)返回一个 ***可观察的*** ，我们使用[***map***](https://github.com/ReactiveX/RxJS/blob/40e9757d751b4328d6f79d8c6ea33adb299d7466/src/operators/map.ts#L17)从响应中提取 JSON 对象和[***subscribe***](https://github.com/ReactiveX/RxJS/blob/40e9757d751b4328d6f79d8c6ea33adb299d7466/src/Subscriber.ts#L9)。这将在我们的流发出结果后立即设置它。最后我们用相应的用户令牌调用***this . store token***。**

> **注意，我们以明文形式传递密码。在真实的场景中，我们会应用更严格的选项并使用 https。**

# **$http 和 angular/http 的区别**

**Angular Http 默认返回一个 ***可观察的*** 与一个 ***承诺*** ( [$q 模块](https://github.com/angular/angular.js/blob/898a3fd3b916602b725a60a5347ee9e01febe628/src/ng/q.js#L219))在$http 中相对。这允许我们使用更加灵活和强大的 RxJS 操作符，如 [switchMap](https://github.com/ReactiveX/RxJS/blob/e1966574715bc526c4c0da9262699187d1a7fbcb/src/operators/switchMap.ts) (版本 4 中的最新 flatMapLatest】、重试、[缓冲](https://github.com/ReactiveX/RxJS/blob/e1966574715bc526c4c0da9262699187d1a7fbcb/src/operators/buffer.ts)、[去抖](https://github.com/ReactiveX/RxJS/blob/dea7847c43061ccffc19d5fdd150240c76c9d50b/src/operators/debounce.ts)、[合并](https://github.com/ReactiveX/RxJS/blob/e1966574715bc526c4c0da9262699187d1a7fbcb/src/operators/merge.ts)或[压缩](https://github.com/ReactiveX/RxJS/blob/e1966574715bc526c4c0da9262699187d1a7fbcb/src/operators/zip.ts)。**

**通过使用 ***Observables*** ，我们提高了应用程序的可读性和可维护性，因为它们可以优雅地响应更复杂的场景，包括多个发出的值，而不是只有一个一次性的单个值。**

## **可观察到的与承诺**

**当与 **Http** 一起使用时，两种实现都提供了处理请求的简单 API，但是有一些关键的区别使得 **Observables** 成为更好的选择:**

*   **除非我们组合多个承诺，否则承诺只接受一个值(例如: [$q.all](https://github.com/angular/angular.js/blob/898a3fd3b916602b725a60a5347ee9e01febe628/src/ng/q.js#L540) )。**
*   **承诺是不能取消的。**

**那都是乡亲们！希望这篇文章能给你一些启发。感谢阅读！有什么问题吗？在推特上给我发短信 [@gerardsans](https://twitter.com/intent/user?screen_name=gerardsans)**

**[](http://www.meetup.com/AngularZone/) [## 安古拉宗社区

### 欢迎来到我们的社区。我们的激情是有棱角的。加入我们吧！🚀](http://www.meetup.com/AngularZone/) 

# 更多资源

*   [以角度](http://blog.thoughtram.io/angular/2015/09/17/resolve-service-dependencies-in-angular-2.html)将服务注入服务，作者帕斯卡尔·普雷奇[@帕斯卡尔·普雷奇](https://twitter.com/PascalPrecht)
*   由杰夫·克罗斯[@杰夫·克罗斯](https://twitter.com/jeffbcross)、罗布·沃玛德[@罗布·沃玛德](https://twitter.com/robwormald)和亚历克斯·里卡鲍 [@synalx](http://twitter.com/@synalx) 所作的《Angular connect talk》[角度数据流](https://www.youtube.com/watch?v=bVI5gGTEQ_U)
*   [角度可观测量，Http 和分离服务和组件](http://chariotsolutions.com/blog/post/angular2-observables-http-separating-services-components/)，作者 Ken Rimple [@krimple](https://twitter.com/krimple)
*   [反应式扩展简介](/google-developer-experts/angular-introduction-to-reactive-extensions-rxjs-a86a7430a61f#.g3ggrh21b)，上一篇文章

[![](img/abefda0aa7864742686ec7f7fdffe2b5.png)](https://twitter.com/intent/user?screen_name=gerardsans)![](img/6b7d888dcaf096cfd65eddc5e39ef3dc.png)![](img/67b37e5d672384f0953563450fca0029.png)**