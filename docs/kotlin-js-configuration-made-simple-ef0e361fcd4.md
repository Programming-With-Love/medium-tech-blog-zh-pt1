# Kotlin/JS 配置变得简单

> 原文：<https://blog.kotlin-academy.com/kotlin-js-configuration-made-simple-ef0e361fcd4?source=collection_archive---------3----------------------->

如果你以前用 Gradle 做过 Kotlin/JS，配置可能相当复杂。Webpack 和 Gradle 互相调用，多个 Webpack 配置文件……真是一塌糊涂。

![](img/70a90889cf654d476e522bc9cc385bc2.png)

幸运的是，现在它已经改变了，你可以[使用新的可能性来使你的配置清晰简单](https://github.com/MarcinMoskala/KotlinAcademyApp/commit/a8d6b26ad8effa285b64d59cede7af0360f953d2)。我很乐意解释如何使用`[kotlin-frontend-plugin](https://github.com/Kotlin/kotlin-frontend-plugin)`进行前端梯度配置。请注意，这是一个官方的 Kotlin 插件，所以它将继续存在，并将与 Kotlin 生态系统的其他部分一起开发。

## 基本配置

在最小版本中，您在 Gradle 配置中需要的是:

```
apply plugin: 'org.jetbrains.kotlin.frontend'
apply plugin: 'kotlin2js'// kotlin2js configuration
compileKotlin2Js {
  kotlinOptions 
    metaInfo = true
    outputFile = "$project.buildDir.path/js/${project.name}.js"
    sourceMap = true
    moduleKind = 'commonjs'
    main = "call"
  }
}dependencies {
  compile "org.jetbrains.kotlin:kotlin-stdlib-js:$kotlin_version"
  // Your other dependencies
}
```

使用这种配置，您可以构建一个功能完整的项目。如果您分析它，您可以找到依赖项和说明 Kotlin 文件应该如何编译的`kotlin2js`配置。没有`[kotlin-frontend-plugin](https://github.com/Kotlin/kotlin-frontend-plugin)` ( `org.jetbrains.kotlin.frontend`)配置。这是因为该插件可以与默认的一个。虽然我们可以指定一个，使用`kotlinFrontend`块。在里面我们可以指定一些重要的东西，比如:

*   我们想要使用的 Node.js 版本
*   npm 依赖项
*   webpack 的配置

让我们看看我们如何能做它。

> Webpack 当前要求将`moduleKind`设置为以下之一:`'commonjs'`、`'amd'`或`'umd'`。`'plain'`，即默认，不适合。这就是为什么如果我们想使用插件，需要在`compileKotlin2Js`块中指定`moduleKind`。

## Kotlin 前端插件配置

从 [Kot 来分析配置吧。学院申请](https://github.com/MarcinMoskala/KotlinAcademyApp)为例:

```
kotlinFrontend {
    downloadNodeJsVersion = **'latest'** // 1npm { // 2
        dependency(**"firebase"**, **"^4.6.2"**)
        dependency(**"react"**, **"15.6.1"**)
        dependency(**"react-dom"**, **"15.6.1"**)
        dependency(**"react-router-dom"**, **"4.2.2"**)
    } sourceMaps = **true** // 3webpackBundle { // 4
        bundleName = **"main"** // 5contentPath = file(**'src/main/web'**) // 6
        proxyUrl = **"http://localhost:8080"** // 7}
}
```

1.  这就是我们总是希望使用最新 Node.js 版本的原因。
2.  我们指定 npm 依赖关系。我们可以使用`dependency`要求正常的依赖关系，或者使用`devDependency`要求开发依赖关系(这些依赖关系不会随您的应用程序一起分发)。
3.  我们启用源地图。当我们需要调试应用程序时，源代码图可以帮助我们——它们允许我们对 Kotlin 代码进行操作，而不是对由`kotlin2js`插件创建的模糊的 JavaScript 结果进行操作。
4.  我们配置 webpack 捆绑包。Bundle 是从我们所有的 Kotlin 源代码和依赖项中创建的单一 JavaScript 文件。
5.  我们可以指定包名。当我们将其设置为“XXX”时，生成的包将被命名为“XXX.bundle.js”。默认名称是模块的名称，因此在本例中，它应该是“web.bundle.js”。一旦我们将 name 设置为“main”，生成的包就是“main.bundle.js”。重要信息:这个命名将这个插件与以前的配置区分开来。一旦转移到`[kotlin-frontend-plugin](https://github.com/Kotlin/kotlin-frontend-plugin)`，记得在`index.html`中更改包名。
6.  我们指定静态文件的位置。它们包括`index.html`，图像，css 文件等。
7.  在这里，我们指定后端网络服务器的位置。所有外部电话都将被转接到那里。

> 给出的配置指定了 Gradle 和 Webpack 的依赖关系，它们都是在我们构建项目时下载的。这个插件让我们可以一起使用两个构建系统，并在一个构建过程中发挥它们的最大优势。

就是这样——对于一个全功能的项目！Gradle 和 Webpack 在一个文件中配置如此容易。太简单了，:D

![](img/52e44ad366ea18edcce098bd698ec6d1.png)

如果您需要更多，还有其他一些 webpack 配置:

```
kotlinFrontend {
    webpackBundle {
        bundleName = "main"
        sourceMapEnabled = true|false // enable/disable source maps 
        contentPath = file(...) // a file that represents a directory to be served by dev server)
        publicPath = "/"  // web prefix
        host = "localhost" // dev server host
        port = 8088   // dev server port
        proxyUrl = "" | "http://...."  // URL to be proxied, useful to proxy backend webserver
        stats = "errors-only"  // log level
    }
}
```

[![](img/018370a2476e1ce49e6d3299428b4f2a.png)](https://www.kt.academy/#workshops-offer)

## 构建和运行

我们可以使用`run`任务在本地运行前端:(在所有这些命令中，我们假设前端模块被命名为`web`

```
./gradlew :web:run
```

这将启动服务于网站的后台守护程序。

一旦启动网站将在后台运行。我们可以使用`stop`任务来停止它:

```
./gradlew :web:stop
```

我们可以使用`build`任务构建一个模块:

```
./gradlew :web:build
```

如果我们只需要捆绑生成，我们可以使用捆绑任务:

```
./gradlew :web:bundle
```

请注意，如果我们需要将应用程序分发到某个外部服务器，我们需要构建 bundle，并将其与静态服务的静态文件信息后端文件夹一起复制。您可以使用复制任务来完成。[这里](https://github.com/MarcinMoskala/KotlinAcademyApp)我们定义了`serverPrepare`任务，用于准备准备好服务后端和前端的 jar。

## 热更换

该插件支持热替换。在最简单的情况下，您所需要的就是运行一个带有`-t`标志的模块:

```
./gradlew -t :web:run 
```

如果我们需要在热替换过程中保留一些数据，那么我们需要将它们保留在状态中，并在重装后恢复它们。这是可以做到的:

```
module.hot?.let { hot ->
    hot.accept() // accept hot reload
    hot.dispose { data ->
        data.my-fields = myGetStateFunction(data)
    }
}module.hot?.data?.let { data -> 
    myRestoreFunction(data)
}
```

## 测试

该插件支持使用 Karma 框架进行测试，但它需要额外的配置。这里有一个例子:

```
kotlinFrontend {
    karma {
        port = 9876
        runnerPort = 9100
        reporters = listOf("progress") 
        frameworks = listOf("qunit") // 1
        preprocessors = listOf("...")
    }
}
```

1.  目前仅支持 Qunit。

相反，我们也可以使用经典的 Karma 配置:

```
kotlinFrontend {
    karma {
        customConfigFile = "myKarma.conf.js"
    }
}
```

我们可以使用`test`任务运行测试:

```
./gradlew :web:test
```

## 摘要

带`[kotlin-frontend-plugin](https://github.com/Kotlin/kotlin-frontend-plugin)`的 Kotlin/JS 功能齐全，随时可以使用。它简单而直观。这个插件非常重要，尤其是在多平台项目中，我们需要使用 Gradle 来构建前端模块。[迁徙于康泰。Academy 应用程序](https://github.com/MarcinMoskala/KotlinAcademyApp/commit/a8d6b26ad8effa285b64d59cede7af0360f953d2)很简单，主要包括删除过时的配置文件。此外，这个插件是由 Kotlin 团队开发的，使用起来很安全。这看起来像最终的 Kotlin 前端配置。我认为这是 Kotlin 迈出的一大步，作为一个多平台解决方案。

## 学到了什么？单击👏说“谢谢！”并帮助他人找到这篇文章。

如果你认为这很重要，与他人分享。

你需要 Kotlin 工作室吗？访问[我们的网站](https://www.kt.academy/)，看看我们能为您做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

我要感谢 [Edvin Syse](https://twitter.com/edvinsyse) 对范例项目的重要贡献。也感谢 Ilya Gorbunov 对本文的重要更正。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)![](img/77f73b2843c7321d074810231e681255.png)