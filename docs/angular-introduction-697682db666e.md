# 角度介绍

> 原文：<https://medium.com/version-1/angular-introduction-697682db666e?source=collection_archive---------4----------------------->

![](img/3567f6681ead5232787102e609d4e6ee.png)

Angular 是一个 JavaScript 框架，允许使用 HTML 和 Typescript 构建反应式单页面客户端应用程序。Angular 1.0 的第一个版本被称为 AngularJS。

Angular 是 AngularJS 的完全重写，由构建 AngularJS 的同一个团队完成。

# 我们先来了解一下什么是单页应用？

单页应用程序是一个 web 应用程序或网站，它为用户提供了非常流畅、反应迅速的体验。它在单个页面上包含一个菜单、按钮和块，当用户点击其中任何一个时；它动态重写当前页面，而不是从服务器加载整个新页面。

# 为什么推出 Angular？

传统上，开发人员使用 VanillaJS 和 jQuery 来开发动态网站。随着网站变得更加复杂，增加了新的特性和功能，开发人员很难维护代码。此外，jQuery 没有提供跨视图的数据处理工具。因此，Angular 就是为了解决这些问题而构建的，通过将代码分成更小的信息片段(在 Angular 中称为组件),使开发人员更容易理解。客户端框架允许开发像 SPAs 这样的高级 web 应用程序，如果由 VanillaJS 开发，这个过程会比较慢。

# 棱角分明的特征。

*   按指定路线发送
*   状态管理
*   RXJS 库
*   一致性
*   可维护性
*   代码可重用性
*   尽早发现错误
*   命令行界面
*   装饰性用户界面
*   跨平台应用开发
*   谷歌的长期支持

# 从角度开始的步骤

*   **安装 Node JS** : Node.js 是基于 Chrome 的 V8 JavaScript 引擎构建的 JavaScript 运行时。它是一个开源、跨平台、后端的 JavaScript 运行时环境，在 web 浏览器之外执行 JavaScript 代码。要安装节点，请访问 NodeJS.org[的](https://nodejs.org/en/)
*   安装 NPM，即节点包管理器 : NPM 是 JavaScript 编程语言的包管理器。Angular、Angular CLI 和 Angular 应用程序的许多特性和功能都依赖于 npm 软件包。NPM 随着节点 JS 的安装而安装。

**#** 检查安装:进入命令提示符并键入“node -v 和 npm -v”

*   安装 Angular CLI:Angular CLI 安装必要的 Angular npm 软件包和其他依赖项。它帮助我们创建项目，生成应用程序和库代码，并执行各种正在进行的开发任务，如测试、捆绑和部署。安装 Angular CLI 的命令**` " NPM install-g @ Angular/CLI " `**
*   **创建工作区**:在需要的位置创建工作区。运行 CLI 命令 ng new，并为应用程序命名。安装 Angular CLI 的命令**` " ng new[<project name>]" `**
*   **运行应用程序**:首先，使用命令**`“CD todo app”`**导航到前面步骤中创建的工作区。

最后，运行命令**`“ng serve-open”`**来构建并运行应用程序。

**#** 默认情况下应用程序在您的浏览器中运行到 [http://localhost:4200/](http://localhost:4200/) 、

我们可以通过两种方式更改应用程序的端口:

*   **永久**:转到 node _ modules/angular-CLI/commands/server。js，search var defaultPort = process。环境。端口| | 4200；把 4200 换成任何你想要的。
*   **运行** : ng serve — port 4500(您可以将 4500 更改为任何您想用作端口的数字)

# 角度应用是如何工作的？

每个 angular app 都由一个名为 **angular.json** 的文件组成。该文件将包含应用程序的所有配置。

*   在构建应用程序时，构建者查看 **angular.json** 文件来找到应用程序的入口点。
*   在构建部分中，options 对象的 main 属性定义了应用程序的入口点，在本例中是 **main.ts.**
*   **main.ts** 文件为应用程序的运行创建了一个浏览器环境，同时，它还调用了一个名为 bootstrap module 的函数来引导应用程序。
*   在 bootstrap 模块函数中，我们调用 **AppModule** 。app 模块在 app.module.ts 中声明，该模块包含所有组件的声明。
*   AppComponent 正在被引导。该组件在 app.component.ts 中定义，该文件与网页交互，并向其中提供数据。
*   最后，Angular 调用 index.html 文件。因此，该文件调用根组件 app-root。

# Angular 的关键组件是什么？

*   **组件:**控制 HTML 视图的 angular 应用程序的基本构件。这是我们编写绑定代码的地方。
*   **模块:**一个 angular 应用程序被分成逻辑块，每一段代码被称为执行单一任务的模块。模块对组件进行逻辑分组。
*   **模板:**模板代表角度应用的视图。
*   **路由:**定义应用程序的路由策略
*   **服务:**可以在整个应用程序中共享的可重用组件。
*   **元数据:**可用于向角度类添加更多数据。

**关于作者**
Nirmal Kandwal 是这里版本 1 的 Angular 开发者。