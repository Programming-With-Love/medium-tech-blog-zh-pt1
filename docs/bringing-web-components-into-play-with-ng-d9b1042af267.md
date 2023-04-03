# 用 ng 发挥 Web 组件的作用！

> 原文：<https://medium.com/globant/bringing-web-components-into-play-with-ng-d9b1042af267?source=collection_archive---------3----------------------->

![](img/876cd5a07484bf68430f4dcce24758bc.png)

Photo by [Tudor Baciu](https://unsplash.com/@baciutudor?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

从[上一篇关于 web 组件的文章](/globant/unraveling-the-web-components-5df1df4ec605)继续，我们现在对它们有了一个概念！

关于角形组件转换成 Web 组件，需要理解的重要一点是，它们是简单的角形组件——作为 Web 组件使用，它们没有什么不同。你可以在里面做任何事情，你可以用一个普通的角度组件，它会正确地工作。为了让组件工作，您必须做的任何配置步骤都是在模块级完成的；组件本身没有任何变化。这意味着你可以毫不费力地将任何现有的角形组件转换成 Web 组件。

那么我们如何在 angular 中创建一个 web 组件呢？

# 设置您的项目

首先，我们将使用 Angular CLI 创建一个 Angular 项目。我们需要通过运行以下命令来安装 Angular CLI:

```
npm install -g @angular/cli
```

这个命令将把 Angular CLI 工具安装到我们的命令行中。安装后，运行以下命令创建 CLI 项目。

```
ng new my-new-app
```

该命令将创建一个角度项目，并将安装所有必要的 NPM 软件包。完成后，我们可以运行:

```
ng serve
```

这个命令将在 localhost:4200 上本地运行我们的 Angular 应用程序

使用以下命令通过 CLI 安装角度元素:

```
npm i @angular/elements --save
```

**@angular/elements** 实现 angular 的自定义元素 API，可以将组件打包成自定义元素。

接下来，我们需要安装自定义元素 polyfill。

> 一个“***”polyfill***”本质上是一段代码，它允许你访问浏览器应该提供的 API。但是，有时这些特性和功能并不容易获得，因此 polyfill 会填充缺少的内容，并确保您的应用程序继续按预期工作。

这意味着您的应用程序在一定程度上可以向后兼容旧的浏览器。

```
npm i @webcomponents/custom-elements --save
```

完成这些工作后，进入 poyfills.ts 文件，该文件位于 Angular 应用程序的 src 文件夹中，并将其导入到该文件中。

```
import ‘@webcomponents/custom-elements/custom-elements.min’;
```

# 创建新组件

Angular CLI 没有为我们的组件编写所有这些文件，而是为我们提供了一种快速生成这些文件的方法。让我们使用这个方法在我们的系统中创建一个 HelloWorld 组件。

```
ng g c hello-world
```

该命令将在 app 文件夹中创建一个名为 hello-world 的文件夹。如果你打开这个文件，你会发现里面有 4 个文件:

*   hello-world.component.css
*   hello-world.component.html
*   你好-世界.组件.规格
*   hello-world.component.ts

在 hello-world.component.ts 文件中，您会看到选择器被设置为 app-hello-world。所以，如果我们像这样把这个加到 app.component.html 上:

然后，hello-world.component.html 的内容将显示在浏览器中。

我们的初始设置已经准备好了。让我们从如何使用角元素开始。

# 将角度构件转换为腹板构件

我们可以将项目中的任何组件转换成自定义元素。这个过程相当简单。接下来要做的事情是告诉 Angular 我们想要构建为定制元素的组件是 entryComponents，在我们的 AppModule 中看起来是这样的:

现在，通过从@angular/core 中导入 **Injector，并从 **app.module.ts** 中的@angular/elements** 中导入 **createCustomElement，将其转换为一个 Angular 自定义元素**

然后在 AppModule 类中，编写一个构造函数创建一个 **createCustomElement** 的新实例，并向其传递 HelloWorldComponent，如下所示:

注射器是我们用来解决依赖性的东西。这样，我们就创建了 HelloWorldComponent 的一个实例作为自定义元素。

向 createCustomElement()方法传递要转换为角度元素的角度分量。这个方法将有助于创建一个桥梁，将您的 Angular 代码转换成能够与 DOM APIs 一起工作的原生 JavaScript 代码。

**代码片段:**

# **后记**

角度元素可让您在角度元件和自定义元素浏览器标准之间架起桥梁。将 Angular 组件转换成 web 组件提供了一种在 Angular 应用程序之外创建动态 HTML 内容的简单方法。