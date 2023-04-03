# Elm III:添加效果

> 原文：<https://medium.com/quick-code/elm-iii-building-a-bridge-adding-effects-574d22ede3e8?source=collection_archive---------0----------------------->

![](img/eaa531b33f1f5b0990719c299bed934a.png)

上周，我们深入探讨了 Elm 的使用。我们看到了如何构建一个更复杂的 web 页面，形成一个待办事项列表应用程序。我们学习了 Elm 架构，并了解了如何使用几个简单的函数来构建我们的页面。我们为将效果带入我们的系统打下了基础，但是没有使用这些。

本周，我们将为我们的 Elm 技能集添加一些有用的东西。我们将看到如何在我们的系统中加入更多的效果，特别是随机性和 HTTP 请求。

要了解更多关于为你的系统构建后端的知识，你应该阅读我们的 [Haskell 网络系列](https://www.mmhaskell.com/haskell-web)。它会教你一些东西，比如连接到数据库和制作 HTTP 服务器。

# 合并效果

上周，我们探索了如何使用`element`表达式来构建我们的应用程序。与`sandbox`不同，这允许我们添加命令，从而产生副作用。但是我们没有使用任何命令。让我们检查一下我们可以在应用程序中使用的几种不同的效果。

我们可以产生的一个简单效果是得到一个随机数。从我们到目前为止的代码来看，这可能不明显，但是目前我们还不能在我们的 Todo 应用程序中实现它！我们的`update`功能是纯粹的！这意味着它不能访问`IO`。它能做的是发送命令作为输出的一部分。命令可以触发信息，并在过程中加入效果。

# 制作随机任务

我们将在应用程序中添加一个按钮。这个按钮将产生一个随机的任务名称，并将其添加到我们的列表中。首先，我们将添加一个新的消息类型进行处理:

```
type TodoListMessage =
  AddedTodo Todo |
  FinishedTodo Todo |
  UpdatedNewTodo (Maybe Todo) |
  AddRandomTodo
```

下面是发送新消息的 HTML 元素。我们可以将它添加到视图的元素列表中:

```
randomTaskButton : Html TodoListMessage
randomTaskButton = button [onClick AddRandomTodo] [text "Random"]
```

现在我们需要将新消息添加到更新函数中。我们需要一个案例:

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  …
  AddRandomTodo ->
    (TodoListState { todoList = todoList, newTodo = newTodo}, …)
```

所以第一次，我们要填入`Cmd`元素！为了产生随机性，我们需要来自`[Random](https://package.elm-lang.org/packages/elm/random/latest/Random)`模块的`generate`函数。

```
generate : (a -> msg) -> Generator a -> Cmd msg
```

我们需要两个参数来使用它。第二个参数是特定类型的随机生成器`a`。第一个参数是从这个类型到我们的消息的函数。在我们的例子中，我们想要生成一个`String`。我们将使用`elm-community/random-extra`包中的一些功能。见[随机。字符串](https://package.elm-lang.org/packages/elm-community/random-extra/latest/Random-String)和[随机。Char](https://package.elm-lang.org/packages/elm-community/random-extra/latest/Random-Char) 了解详情。我们的字符串将有 10 个字母长，并且只使用小写字母。

```
genString : Generator String
genString = string 10 lowerCaseLatin
```

现在我们可以很容易地将它转换成新的信息。我们生成字符串，然后将其作为 Todo 添加:

```
addTaskMsg : String -> TodoListMessage
addTaskMsg name = AddedTodo (Todo {todoName = name})
```

现在我们可以将这些插入到我们的更新函数中，这样我们就有了正常运行的随机命令！

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  …
  AddRandomTodo ->
    (..., generate addTaskMsg genString)
```

现在点击随机按钮将会产生一个随机任务并添加到我们的列表中！

# 发送 HTTP 请求

我们可以添加的一个更复杂的效果是发送一个 HTTP 请求。我们将使用 Elm 的`[Http](https://package.elm-lang.org/packages/elm/http/latest/Http)` [库](https://package.elm-lang.org/packages/elm/http/latest/Http)。每当我们完成一项任务时，我们都会向某个端点发送一个请求，该请求的正文中包含该任务的名称。

我们将挂钩到我们目前的行动为`FinishedTodo`。目前，这将返回`None`命令及其更新。我们将让它发送一个触发 post 请求的命令。这个 post 请求将依次挂钩到另一个消息类型，我们将创建这个消息类型来处理响应。

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  …
  (FinishedTodo doneTodo) ->
    (..., postFinishedTodo doneTodo)
  ReceivedFinishedResponse -> ...postFinishedTodo : Todo -> Cmd TodoListMessage
postFinishedTodo = ...
```

我们使用`send`函数创建 HTTP 命令。它需要两个参数:

```
send : (Result Error a -> msg) -> Request a -> Cmd Msg
```

第一个是解释服务器响应并给我们发送新消息的函数。第二个是期望某种类型结果的请求`a`。让我们为这些参数绘制更多的代码框架。我们会想象我们的响应得到了一个`String`，但这无关紧要。不管怎样，我们都会传达同样的信息:

```
postFinishedTodo : Todo -> Cmd TodoListMessage
postFinishedTodo todo = send interpretResponse (postRequest todo)interpretResponse : Result Error String -> TodoListMessage
interpretResposne _ = ReceivedFinishedResponsepostRequest : Todo -> Request String
postRequest = ...
```

现在我们所需要的就是使用`post`函数创建我们的 post 请求:

```
post : String -> Body -> Decoder a -> Request a
```

现在我们又有三个参数需要填充。第一个是我们发送请求的 URL。第二个是我们的身体。第三个是响应的解码器。我们的解码器将是`Json.Decode.string`，一个库函数。我们将假设我们正在为 URL 运行一个本地服务器。

```
postRequest : Todo -> Request String
postRequest todo = post "localhost:8081/api/finish" … Json.Decode.string
```

我们现在需要做的就是在 post 请求体中编码 to do。这很简单。我们将使用`Json.Encode.object` 函数，它接受一个元组列表。然后我们将在 todo 名称上使用`string`编码器。

```
jsonEncTodo : Todo -> Value
jsonEncTodo (Todo todo) = Json.Encode.object
  [ ("todoName", Json.Encode.string todo.todoName) ]
```

我们将它与`jsonBody`功能一起使用。然后我们就完事了！

```
postRequest : Todo -> Request String
postRequest todo = post
  "localhost:8081/api/finish"
  (jsonBody (jsonEncTodo todo))
  Json.Decode.string
```

提醒一下，最后一部分中的大多数类型和助手函数来自 Elm 的 HTTP 库[。然后，我们可以在我们的`interpretResponse`函数中进一步处理响应。如果我们得到一个错误，我们可以发送不同的消息。不管怎样，我们最终可以在我们的`update`函数中做更多的更新。](https://package.elm-lang.org/packages/elm/http/latest/Http)

# 结论

这是我们关于 Elm 系列的第 3 部分！我们看了一些漂亮的方法来为我们的 Elm 项目添加额外的效果。我们看到了如何在我们的 Elm 项目中引入随机性，以及如何发送 HTTP 请求。下周，我们将探索导航，这是任何 web 应用程序的重要部分。我们将看到 Elm 架构如何支持多页面应用程序。然后，我们将看到如何在不同的页面之间有效地移动，而不需要每次都重新加载我们的 Elm 代码。

既然你已经知道了如何编写一个功能性的前端，你应该学习更多关于后端的知识！阅读我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)来获得一些关于如何做到这一点的教程。您也可以下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)以获得更多创意！