# 在 Akka HTTP 中创建自定义指令

> 原文：<https://medium.com/quick-code/creating-custom-directives-in-akka-http-f179755ef60?source=collection_archive---------0----------------------->

*整理好你的路线。*

![](img/5d72fc7ff78e5d82e6e57f5b8a29bd06.png)

Photo by [Fabian Grohs](https://unsplash.com/photos/U0tBTn8UR8I?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如果您曾经用 Akka HTTP 编写过比 Hello World API 更复杂的东西，您可能会发现路由 DSL 可能会很快失控。

在你的路线中有很深的嵌套层，甚至可能有一些代码重复。

这就是指令的作用。但是什么是指令呢？

[指令是小的、可组合的构建块](https://doc.akka.io/docs/akka-http/current/routing-dsl/directives/index.html)，允许你一点一点地创建你的路线。

典型的路线如下所示:

```
pathPrefix("todos") {
  pathEndOrSingleSlash {
    get {
      onComplete(todoRepository.all()) {
        **case** *Success*(todos) =>
          complete(todos)
        **case** *Failure*(exception) =>
          *println*(exception.getMessage)
          complete(ApiError.*generic*.statusCode, ApiError.*generic*.message)
      }
    }
  }
}
```

上面的路由接受路径`/todos`或`/todos/`中的 GET 请求。当`todoRepository.all()`返回的未来完成时，它也做一些错误处理。

然而，在我们的大多数(如果不是全部)路由中，我们将需要这种错误处理。就像现在这样，我们不得不到处复制粘贴。

这只是错误处理，我们可能还想在路由中处理其他问题，比如身份验证。

那么，我们如何重用这些错误处理位呢？

我们可以创建自己的自定义指令。

## 设置项目

我们将使用一个[基础项目](http://link.codemunity.io/m-custom-directives-repo)，确保克隆它并检查`6.3-api-errors`分支以跟进。

在创建自定义指令之前，让我们对项目做一个快速的概述。

该项目使用 Scala `2.12.6`和 SBT `1.1.6`，您可以分别在`build.sbt`和`build.properties`文件中确认版本。

该项目是 todo 应用程序的 API。在项目的根级别运行`tree src`会给我们带来以下结果:

```
src
├── main
│   └── scala
│       ├── ApiError.scala
│       ├── Main.scala
│       ├── Router.scala
│       ├── Server.scala
│       ├── Todo.scala
│       └── TodoRepository.scala
└── test
    └── scala
        ├── TodoMocks.scala
        └── TodoRouterListSpec.scala4 directories, 8 files
```

简而言之，这些班级的职责是:

*   **ApiError:** 模型错误。
*   **主:**申请入口点。
*   **路由器:**我们的路由住的地方。
*   **服务器:**取一个路由器，绑定到主机和端口。
*   **待办事项:**应用模型。
*   **TodoRepository:** 为 todos 处理 CRUD。

在`TodoRouter`实现中，你可以看到我们上面讨论过的路由，我们正在做一些手动错误处理，我们希望在其他路由中做同样的事情。

如果你看一下`build.sbt`文件，你可以看到我们需要的依赖关系， [Akka](https://akka.io/docs/) actors，streams 和 HTTP 模块，[喀尔刻](https://circe.github.io/circe/)用于 JSON，以及一个额外的库用于 Akka HTTP。

## 创建自定义指令

有多种方法可以创建自定义指令，但是我们将转换现有的指令来创建新的指令。

在名为`TodoDirectives`的`src/main/scala`下创建一个新的`trait`，它将扩展特征`Directives`，我们还需要导入喀尔刻及其支持库，我们正在使用它们进行 JSON 序列化。

```
**import** akka.http.scaladsl.server.Directives

**trait** TodoDirectives **extends** Directives {
  **import** de.heikoseeberger.akkahttpcirce.FailFastCirceSupport._
  **import** io.circe.generic.auto._

}
```

让我们创建一个通用函数，它将处理未来的失败，给定一个函数，它决定当意外发生时应该返回哪个`ApiError`:

```
**def** handle[T](f: Future[T])(e: Throwable => ApiError): Directive1[T] =
  onComplete(f) flatMap {
    **case** *Success*(t) =>
      provide(t)
    **case** *Failure*(error) =>
      **val** apiError = e(error)
      complete(apiError.statusCode, apiError.message)
  }
```

这与我们路由中的错误处理逻辑非常相似，但是让我们来看看不同之处。

我们接收一个接收一个`Throwable`并返回一个`ApiError`的函数，这将允许我们在外部处理这个问题，并使指令更加通用。

此外，我们使用`flatMap`是因为我们正在转换指令以创建新的指令，而不是应用它们。

如果未来成功，我们`provide`该值，这就是为什么我们的返回类型是一个`Directive1[T]`，我们的指令将返回或提供一个值，这是包含在未来的值。

然而，如果 future 失败了，我们从函数中得到`ApiError`,然后我们将带有相应状态代码和消息的响应发送回客户端。

如果您查看`ApiError.scala`文件，我们只有一个一般性错误。现在我们的存储库在很多方面都不会失败，因为我们只列出了待办事项。

让我们创建另一个指令，无论异常是什么，它总是用一般的`ApiError`进行回复:

```
**def** handleWithGeneric[T](f: Future[T]): Directive1[T] =
  handle[T](f)(_ => ApiError.*generic*)
```

让我们用它来整理我们的`TodoRouter`中的错误处理。首先，我们需要扩展我们的`TodoDirective`特征来访问我们的自定义指令。

然后我们可以更新路线，它将看起来像:

```
pathPrefix("todos") {
  pathEndOrSingleSlash {
    get {
      handleWithGeneric(todoRepository.all()) { todos =>
        complete(todos)
      }
    }
  } ~ ...
```

它比最初的更干净，更重要的是，我们现在可以用更少的样板文件更新其他路由。我们的`TodoRouter`最终看起来像:

```
**import** akka.http.scaladsl.server.{Directives, Route}

**trait** Router {
  **def** route: Route
}

**class** TodoRouter(todoRepository: TodoRepository) **extends** Router **with** Directives **with** TodoDirectives {
  **import** de.heikoseeberger.akkahttpcirce.FailFastCirceSupport._
  **import** io.circe.generic.auto._

  **override def** route: Route = pathPrefix("todos") {
    pathEndOrSingleSlash {
      get {
        handleWithGeneric(todoRepository.all()) { todos =>
          complete(todos)
        }
      }
    } ~ path("done") {
      get {
        handleWithGeneric(todoRepository.done()) { todos =>
          complete(todos)
        }
      }
    } ~ path("pending") {
      get {
        handleWithGeneric(todoRepository.pending()) { todos =>
          complete(todos)
        }
      }
    }
  }
}
```

我们还可以通过运行`TodoRouterListSpec`测试，或者从项目根目录下的命令行运行`sbt test`,来确保(或许应该)我们的路由仍然按照预期工作。

运行`sbt test`应该会得到类似如下的输出:

```
[info] TodoRouterListSpec:
[info] A TodoRouter
[info] - should return all the todos
[info] - should return all the done todos
[info] - should return all the pending todos
[info] - should handle repository failure in the todos route
[info] - should handle repository failure in the done todos route
[info] - should handle repository failure in the pending todos route
[info] Run completed in 1 second, 859 milliseconds.
[info] Total number of tests run: 6
[info] Suites: completed 1, aborted 0
[info] Tests: succeeded 6, failed 0, canceled 0, ignored 0, pending 0
[info] All tests passed.
[success] Total time: 13 s, completed 20-Aug-2018 23:21:12
```

也就是说所有的测试都通过了。厉害！👏🏽

如果你喜欢这个教程，我们有一个免费的 Akka HTTP 课程，你可以从头开始构建我们在这里使用的 Todo 项目，一步一步地解释。里面见！👇🏽

[](http://link.codemunity.io/cd-akka-http-quickstart-course) [## Akka HTTP 快速入门

### 了解如何创建这个免费的课程与 Akka HTTP 的 web 应用程序和 API！

link . code community . io](http://link.codemunity.io/cd-akka-http-quickstart-course)