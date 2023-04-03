# Elm II:制作待办事项列表！

> 原文：<https://medium.com/quick-code/elm-ii-todo-list-redux-ed1aae2dabbd?source=collection_archive---------7----------------------->

![](img/ac24ca7ad56959e9a4781c0dd3dd5190.png)

上周我们学习了榆树的基本知识。Elm 是一种可以用于前端 web 开发的函数式语言。它的语法非常接近 Haskell。尽管正如我们所探索的，它缺少一些关键的语言特性。

本周，我们将制作一个简单的 Todo List 应用程序来展示 Elm 是如何工作的。我们将看到如何应用我们所学的基础知识，并进一步发展。

但是没有后端前端也没多大用！看看我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)来学习一些很酷的 Haskell 后端库！

# 待办事项类型

在我们开始之前，让我们定义我们的类型。我们将有一个基本的`Todo`类型，用一个字符串作为它的名字。我们还将为表单的状态创建一个类型。这包括我们的项目列表以及“待办事项”，包含以下形式的文本:

```
module Types exposing
  ( Todo(..)
  , TodoListState(..)
  , TodoListMessage(..)
  )type Todo = Todo 
  { todoName : String }type TodoListState = TodoListState
  { todoList : List Todo
  , newTodo : Maybe Todo
  }
```

我们还想定义一个消息类型。这些是我们将从`view`发送的消息，用于更新我们的模型。

```
type TodoListMessage =
  AddedTodo Todo |
  FinishedTodo Todo |
  UpdatedNewTodo (Maybe Todo)
```

# 榆树的建筑

现在让我们回顾一下 Elm 的架构是如何工作的。上周我们用`sandbox`函数描述了我们的程序。这个简单的函数有三个输入。它有一个初始状态(我们使用了一个基本的`Int`)、一个`update`函数和一个`view`函数。`update`函数接受一个`Message`和我们现有的模型，并返回更新后的模型。`view`函数获取我们的模型，并以 HTML 的形式呈现出来。视图的结果类型是`Html Message`。您应该将这种类型理解为“可以发送类型为`Message`的消息的渲染 HTML”。这个表达式的结果类型是一个`Program`，由我们的模型和消息类型参数化。

```
sandbox :
  { init : model
  , update : msg -> model -> model
  , view : model -> Html msg
  }
  -> Program () model msg
```

一个`sandbox`程序虽然不允许我们与外界交流太多！换句话说，没有`IO`，除了渲染 DOM！所以我们可以使用一些更高级的函数来创建一个`Program`。对于一个普通的应用程序，你需要使用`application`函数，见[这里的](https://github.com/elm/browser/blob/master/src/Browser.elm#L200)。对于我们本周要做的单页例子，我们几乎可以不用`sandbox`。但是我们将展示如何使用`element`函数来至少在我们的系统中获得一些效果。`element`函数看起来很像`sandbox`，有一些变化:

```
element :
  { init : flags -> (model, Cmd msg)
  , view : model -> Html msg
  , update : msg -> model -> (model, Cmd msg)
  , subscriptions : model -> Sub msg
  }
  -> Program flags model msg
```

同样，我们有用于`init`、`view`和`update`的函数。但是有几个签名有点不同。我们的`init`函数现在接受程序标志。我们不会用这些。但是它们允许您将 Elm 项目嵌入到一个更大的 Javascript 项目中。标志是从 Javascript 传递到 Elm 程序的信息。

使用`init`也会产生一个模型和一个`Cmd`元素。这将允许我们在初始化应用程序时运行“命令”。你可以认为这些命令是副作用，它们也可以产生我们的消息类型。

我们看到的另一个变化是`update`函数也可以像新模型一样产生命令。最后，我们有最后一个元素`subscriptions`。这允许我们订阅外部事件，比如时钟滴答声和 HTTP 请求。下周我们会看到更多。现在，让我们布置一下应用程序的框架，并记下所有的类型签名。(进口清单见附录)。

```
main : Program () TodoListState TodoListMessage
main = Browser.element 
  { init = todoInit
  , update = todoUpdate
  , view = todoView
  , subscriptions = todoSubscriptions
  }todoInit : () -> (TodoListState, Cmd TodoListMessage)todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)todoView : TodoListState -> Html TodoListMessagetodoSubscriptions : TodoListState -> Sub TodoListMessage
```

初始化我们的程序很容易。我们将忽略这些标志，并返回一个没有任务的状态和正在进行的任务的`Nothing`。我们将返回`Cmd.none`，表明初始化我们的状态没有影响。我们还将为订阅填写`Sub.none`。

```
todoInit : () -> (TodoListState, Cmd TodoListMessage)
todoInit _ = 
  let st = TodoListState { todoList = [], newTodo = Nothing }
  in (st, Cmd.none)todoSubscriptions : TodoListState -> Sub TodoListMessage
todoSubscriptions _ = Sub.none
```

# 填充视图

现在对于我们的视图，我们将把基本的模型组件转换成 HTML。当我们有一个 Todo 元素列表时，我们将在一个有序列表中显示它们。我们会为他们每个人列出一个清单。该项目将说明项目的名称，并给出一个“完成”按钮。单击该按钮，我们可以发送一条消息来完成该待办事项:

```
todoItem : Todo -> Html TodoListMessage
todoItem (Todo todo) = li [] 
  [ text todo.todoName
  , button [onClick (FinishedTodo (Todo todo))] [text "Done"]
  ]
```

现在，让我们把添加待办事项的输入表单放在一起。首先，我们将确定输入中的值以及是否禁用 done 按钮。然后我们将定义一个函数，将输入字符串转换成一个新的`Todo`项。这将发送更改新待办事项的消息。

```
todoForm : Maybe Todo -> Html TodoListMessage
todoForm maybeTodo = 
  let (value_, isEnabled_) = case maybeTodo of
                              Nothing -> ("", False)
                              Just (Todo t) -> (t.todoName, True)
      changeTodo newString = case newString of
                               "" -> UpdatedNewTodo Nothing
                               s -> UpdatedNewTodo (Just (Todo { todoName = s }))
  in ...
```

现在，我们将制作表单的 HTML。输入元素本身将绑定到我们的`onChange`函数中，该函数将更新我们的状态。“添加”按钮将发送添加新待办事项的消息。

```
todoForm : Maybe Todo -> Html TodoListMessage
todoForm maybeTodo = 
  let (value_, isEnabled_) = ...
      changeTodo newString = ...
  in div []
       [ input [value value_, onInput changeTodo] []
       , button [disabled (not isEnabled_), onClick (AddedTodo (Todo {todoName = value_}))] 
          [text "Add"]
       ]
```

然后我们可以在`view`函数中将所有的视图代码放在一起。我们有待办事项列表，然后添加表单。

```
todoView : TodoListState -> Html TodoListMessage
todoView (TodoListState { todoList, newTodo }) = div []
  [ ol [] (List.map todoItem todoList)
  , todoForm newTodo
  ]
```

# 更新模型

我们需要做的最后一件事是写出我们的`update`函数。这只是处理一条消息并相应地更新状态。我们需要三个箱子:

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  (AddedTodo newTodo_) -> ...
  (FinishedTodo doneTodo) -> ...
  (UpdatedNewTodo newTodo_) -> ...
```

每一种情况都很简单。对于添加 Todo，我们将把它附加在列表的前面:

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  (AddedTodo newTodo_) ->
    let st = TodoListState { todoList = newTodo_ :: todoList
                           , newTodo = Nothing
                           }
  (FinishedTodo doneTodo) -> ...
  (UpdatedNewTodo newTodo_) -> ...
```

当我们完成一个待办事项时，我们将通过过滤名称相等来将其从列表中删除。

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  (AddedTodo newTodo_) -> ...
  (FinishedTodo doneTodo) ->
    let st = TodoListState { todoList = List.filter (todosNotEqual doneTodo) todoList
                           , newTodo = newTodo
                           }
    in (st, Cmd.none)
  (UpdatedNewTodo newTodo_) -> ..todosNotEqual : Todo -> Todo -> Bool
todosNotEqual (Todo t1) (Todo t2) = t1.todoName /= t2.todoName
```

更新新的待办事项是最容易的！我们需要做的就是在州内更换它。

```
todoUpdate : TodoListMessage -> TodoListState -> (TodoListState, Cmd TodoListMessage)
todoUpdate msg (TodoListState { todoList, newTodo}) = case msg of
  (AddedTodo newTodo_) -> ...
  (FinishedTodo doneTodo) -> ...
  (UpdatedNewTodo newTodo_) -> ..
    (TodoListState { todoList = todoList, newTodo = newTodo_ }, Cmd.none)
```

就这样，我们完成了！我们有一个用于待办事项列表的基本程序。

# 结论

这就完成了我们的基本 Todo 应用程序！从 Elm 开始，我们还需要学习更多的东西。下周，我们将看到如何实现任何站点都需要的一些重要类型的效果。我们将看到 Elm 效果系统的介绍，并使用它来发送 HTTP 请求。

想了解更多关于在 Haskell 中构建酷产品的想法，请看一下我们的[生产清单](https://www.mmhaskell.com/production-checklist)。它介绍了许多主题的一些库，包括数据库和解析！

# 附录:进口

```
import Browser
import Html exposing (Html, button, div, text, ol, li, input)
import Html.Attributes exposing (value, disabled)
import Html.Events exposing (onClick, onInput)
```