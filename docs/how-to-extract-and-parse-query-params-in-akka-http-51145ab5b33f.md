# 如何提取和解析 Akka HTTP 中的查询参数

> 原文：<https://medium.com/quick-code/how-to-extract-and-parse-query-params-in-akka-http-51145ab5b33f?source=collection_archive---------0----------------------->

![](img/77e5223d9247f96c7b26212275804288.png)

在我们免费的 [Scala 和 Akka HTTP 课程](http://link.codemunity.io/website-akka-http-quickstart)中，我们为 Todo 应用程序构建了一个 API。原来我知道…

在本教程中，我们将通过添加一个允许我们搜索 todos 的端点来扩展 API 的功能。

端点将接受两个查询参数，我们将提取、解析它们并返回适当的 todo 列表。

克隆[过程报告](https://github.com/Codemunity/akkahttp-quickstart)并检查分支`8.3-test-update-route`。

随意四处看看，当你准备好了，我们就开始编码！

# 在存储库中实现搜索

我们的第一步是在存储库中实现搜索功能。

如果你看看我们的模型，我们已经为每个端点接收的数据创建了一个模型。

让我们为我们的搜索添加一个，我们希望通过文本或完成字段进行搜索，如果指定了文本，我们将检查标题和描述。

我们希望这两个值都是可选的，以保持搜索的灵活性。

该模型看起来像:

```
case class SearchTodo(text: Option[String], done: Option[Boolean])
```

接下来，打开`TodoRepository`文件，向 trait 添加以下方法:

```
def search(searchTodo: SearchTodo): Future[Seq[Todo]]
```

它期待我们刚刚创建的模型，它将返回一系列的 todos。

该项目现在不会编译，因为我们必须在几个地方实现这个新方法。先说最简单的。

我们需要更新测试文件夹中的`FailingRepository`，它存在于`TodoMocks`特征中。向其添加以下方法:

```
override def search(searchTodo: SearchTodo): Future[Seq[Todo]] = Future.failed(new Exception("Mocked exception"))
```

整个文件现在看起来像这样:

```
import scala.concurrent.Future

trait TodoMocks {

  class FailingRepository extends TodoRepository {
    override def all(): Future[Seq[Todo]] = Future.failed(new Exception("Mocked exception"))

    override def done(): Future[Seq[Todo]] = Future.failed(new Exception("Mocked exception"))

    override def pending(): Future[Seq[Todo]] = Future.failed(new Exception("Mocked exception"))

    override def save(createTodo: CreateTodo): Future[Todo] = Future.failed(new Exception("Mocked exception"))

    override def update(id: String, updateTodo: UpdateTodo): Future[Todo] = Future.failed(new Exception("Mocked exception"))

    override def search(searchTodo: SearchTodo): Future[Seq[Todo]] = Future.failed(new Exception("Mocked exception"))
  }

}
```

现在我们可以在`InMemoryTodoRepository`类中实现实际的搜索登录。为了简单起见，我们将一步一步地过滤待办事项列表。这毕竟是一个虚拟的内存实现。

覆盖新方法:

```
override def search(searchTodo: SearchTodo): Future[Seq[Todo]] = Future.successful {
}
```

我们知道我们需要返回一个`Future`，所以我们将继续使用整个类中使用的模式和`Future.successful`。现在让我们来实现这个函数。

首先，我们将折叠文本字段，如果它是空的，这意味着我们不必用它来过滤待办事项列表，所以我们将返回待办事项列表。如果它不为空，我们将检查标题或描述是否包含文本:

```
val textTodos = searchTodo.text.fold(todos)(text => todos.filter { todo =>
  todo.title.contains(text) || todo.description.contains(text)
})
```

其次，我们将在`done`字段上做同样的事情，但是这次我们过滤了`textTodos`:

```
searchTodo.done.fold(textTodos)(done => textTodos.filter(_.done == done))
```

我们的整个函数看起来像:

```
override def search(searchTodo: SearchTodo): Future[Seq[Todo]] = Future.successful {
  val textTodos = searchTodo.text.fold(todos)(text => todos.filter { todo =>
    todo.title.contains(text) || todo.description.contains(text)
  })
  searchTodo.done.fold(textTodos)(done => textTodos.filter(_.done == done))
}
```

# 添加搜索路线

有了我们的搜索功能，是时候通过添加新路线来展示它了。

我们希望响应对`/todos/search`路由的 GET 请求，我们将接受前面提到的两个查询参数，`done`和`text`。

让我们转到`Router`文件，并在里面看一看`TodoRouter`类。要监听所需的路由，我们需要将它添加到*“pending”*端点的旁边:

```
... ~ path("pending") {
    get {
      handleWithGeneric(todoRepository.pending()) { todos =>
        complete(todos)
      }
    }
  } ~ path("search") {
    get {
    }
  }
```

这样我们就可以监听路径`/todos/search`下的 GET 请求，现在我们需要处理参数。

为此，我们可以使用`parameters`指令。我们可以查看官方文档来获得一些指导。

它接受我们想要接受的参数列表，按照文档中的第一个示例，用法如下:

```
parameters('text, 'done) { (text: String, done: String) =>
  // search!
}
```

但这意味着我们得到的`text`和`done`值都是字符串，这不是我们想要的。

我们希望`text`值是一个`Option[String]`，而`done`是一个`Option[Boolean]`。我们总是可以手动进行解析/对话，但是让我们看看 Akka HTTP 是否提供了我们需要的东西。

根据文档，我们可以使用`?`函数获得一个`Option[String]`:

```
parameters('text.?, 'done) { (text: Option[String], done: String) =>
  // the text is now Option[String]
}
```

但是`done`呢？

让我们从使它成为一个布尔值开始，文档显示了如何通过使用`as`方法将`color`转换为`Int`，让我们看看是否可以用它来转换为`Boolean`:

```
parameters('text.?, 'done.as[Boolean]) { (text: Option[String], done: Boolean) =>
  // done is a Boolean now!
}
```

我们如何使`Boolean`成为可选值？文档中没有提到这个具体的用例，但是我们可以看到，在像使用`"distance".as[Int].*`一样使用`as`之后，我们可以使用`*`方法。让我们看看是否可以使用`?`方法来完成我们所需要的:

```
parameters('text.?, 'done.as[Boolean]) { (text: Option[String], done: Option[Boolean]) =>
  // finally, we got Option[Boolean]
}
```

现在我们可以创建我们的`SearchTodo`模型的一个实例。我们可以手动操作，但不能自动操作吗？

原来[我们可以](https://doc.akka.io/docs/akka-http/current/routing-dsl/case-class-extraction.html)通过使用`as`函数和模型的应用方法:

```
parameters('text.?, 'done.as[Boolean].?).as(SearchTodo) { searchTodo: SearchTodo =>
  // we've got our model now!
}
```

让我们完成路线的实施。在得到模型之后，我们可以按照通常的模式用泛型指令处理错误，然后调用我们在存储库中早期实现的`search`方法:

```
... ~ path("search") {
      get {
        parameters('text.?, 'done.as[Boolean].?).as(SearchTodo) { searchTodo =>
          handleWithGeneric(todoRepository.search(searchTodo)) { todos =>
            complete(todos)
          }
        }
      }
    }
```

一旦我们获得了搜索的 todos，如果有的话，我们将与他们一起响应请求。

在`Main`对象中，我们用几个 todos 启动我们的应用程序，让我们运行它并向我们的 API 发送一些搜索请求。

我使用的是 [HTTPie](https://httpie.org/) ，但也可以随意使用`curl`或您选择的客户端。

`http localhost:9000/todos/search?done=true`:

![](img/e245d87d1dc3fd6a491df7a6b95e7c63.png)

`http localhost:9000/todos/search?done=false`:

![](img/46c439f0915156c26b87fdcc44e6fa63.png)

`http localhost:9000/todos/search?text=Buy`:

![](img/96f75fbf3542def9164de52482167d4d.png)

您可以随意使用它，并思考我们尚未涉及的任何边缘案例。

# 测试新路线

手动测试在开始时很有趣，你可以亲眼看到你构建的东西在工作，但是随着你的应用程序的发展，它变得不再实际，或者不可行。

所以让我们为我们的搜索路线添加一些测试。如果你不熟悉如何测试 Akka HTTP，不要担心，这很简单，已经有一些测试了，看看`main`文件夹旁边的`test`文件夹。

我们希望确保某些场景按预期工作:

*   按完成搜索
*   按文本搜索
*   按两者搜索

让我们开始吧。

在`test/scala`下创建一个名为`TodoRouterSearchSpec`的新文件:

```
import akka.http.scaladsl.model.StatusCodes
import akka.http.scaladsl.server.ValidationRejection
import akka.http.scaladsl.testkit.ScalatestRouteTest
import org.scalatest.{Matchers, WordSpec}class TodoRouterSearchSpec  extends WordSpec with Matchers with ScalatestRouteTest {
  import de.heikoseeberger.akkahttpcirce.FailFastCirceSupport._
  import io.circe.generic.auto._}
```

当我们完成测试时，我们已经有了我们需要的导入，我们将使用 ScalaTest 的`WordSpec`样式，我们导入喀尔刻来编码和解码我们与 JSON 之间的模型。

出于测试目的，我们将实例化几个 todos，就像我们的`Main`一样:

```
private val doneTodo =
  Todo("2", "Buy milk", "The cat is thirsty!", done=true)
private val pendingTodo =
  Todo("1", "Buy eggs", "Ran out of eggs, buy a dozen", done=false)private val todos = Seq(doneTodo, pendingTodo)
```

让我们从第一个测试开始:

```
"A TodoRouter" should { "search todos by done field" in {
    // what goes here?! }
}
```

我们将遵循其他测试中使用的模式，首先我们将实例化一个内存存储库和一个路由器:

```
val repository = new InMemoryTodoRepository(todos)
val router = new TodoRouter(repository)
```

然后，我们将发出两个请求，一个搜索已完成的待办事项，另一个搜索未完成的待办事项。我们将断言我们获得了正确的状态代码和响应:

```
Get("/todos/search?done=true") ~> router.route ~> check {
  status shouldBe StatusCodes.OK
  val respTodos = responseAs[Seq[Todo]]
  respTodos shouldBe Seq(doneTodo)
}

Get("/todos/search?done=false") ~> router.route ~> check {
  status shouldBe StatusCodes.OK
  val respTodos = responseAs[Seq[Todo]]
  respTodos shouldBe Seq(pendingTodo)
}
```

我们的测试用例看起来像:

```
"search todos by done field" in {
  val repository = new InMemoryTodoRepository(todos)
  val router = new TodoRouter(repository) Get("/todos/search?done=true") ~> router.route ~> check {
    status shouldBe StatusCodes.OK
    val respTodos = responseAs[Seq[Todo]]
    respTodos shouldBe Seq(doneTodo)
  } Get("/todos/search?done=false") ~> router.route ~> check {
    status shouldBe StatusCodes.OK
    val respTodos = responseAs[Seq[Todo]]
    respTodos shouldBe Seq(pendingTodo)
  }
}
```

对于我们的下一个测试，我们也将执行两个请求，一个请求标题，另一个请求描述:

```
"search todos by text" in {
  val repository = new InMemoryTodoRepository(todos)
  val router = new TodoRouter(repository)

  Get("/todos/search?text=dozen") ~> router.route ~> check {
    status shouldBe StatusCodes.OK
    val respTodos = responseAs[Seq[Todo]]
    respTodos shouldBe Seq(pendingTodo)
  }

  Get("/todos/search?text=Buy") ~> router.route ~> check {
    status shouldBe StatusCodes.OK
    val respTodos = responseAs[Seq[Todo]]
    respTodos shouldBe todos
  }
}
```

与第一个非常相似，但是这次我们发送的是`text`查询参数。

我们将为我们的快乐之路增加一个测试，在这里我们将搜索两者:

```
"search todos by both" in {
  val repository = new InMemoryTodoRepository(todos)
  val router = new TodoRouter(repository)

  Get("/todos/search?done=true&text=egg") ~> router.route ~> check {
    status shouldBe StatusCodes.OK
    val respTodos = responseAs[Seq[Todo]]
    respTodos shouldBe Seq.empty
  }
}
```

随意添加更多的测试用例，并对它们进行试验。

# 验证参数

您可能想知道:“如果我们得到无效数据怎么办？”。你的担心是对的，我们不能相信用户会给我们发送有效的数据。

但是如果 Akka HTTP 正在为我们创建模型，我们如何验证它呢？

我们总是可以在创建后验证它，但是如果 Akka HTTP 可以处理它，并且一旦我们得到模型，我们就可以直接使用它，这不是很好吗？

嗯，当然有可能…不然我也不会这么说！😉

玩笑归玩笑，有多种方法可以做到这一点，我们将在本教程中使用一个简单的方法。

我们将利用 Scala 的 [require](https://www.scala-lang.org/api/current/scala/Predef%24.html) (如果检查链接，在搜索`require`时查找最后出现的内容)方法。

如果没有满足这些约束，Akka HTTP 将使用这些约束来拒绝请求。

我们将讨论两种情况:

*   如果指定了字符串，则它不能为空，例如应该拒绝`/todos/search?text=`
*   至少应该指定一个参数

必须更新模型以包含这些验证，接下来我们来做:

```
case class SearchTodo(text: Option[String], done: Option[Boolean]) {
  require(text.fold(true)(_.nonEmpty), "If specified, the text can't be empty")
  require(text.nonEmpty || done.nonEmpty, "At least one parameter has to be specified")
}
```

我们甚至可以写自己的错误信息，酷！

让我们编写一个测试来验证第一种情况:

```
"not search with empty text" in {
  val repository = new InMemoryTodoRepository(todos)
  val router = new TodoRouter(repository) Get("/todos/search?text=") ~> router.route ~> check {
    rejection shouldBe a[ValidationRejection]
  }
}
```

这里的新内容是我们如何断言请求应该被拒绝。Akka HTTP 的 testkit 用`rejection`方法让我们知道。

我们断言`rejection`方法返回的值应该有一个`ValidationRejection`类型，这就是 Akka HTTP 将返回的类型。

运行测试并确保它通过。

但是，当实际提出请求时，这种拒绝会是什么样的呢？让我们运行`Main`对象，自己看看:

`http localhost:9000/todos/search?text=`:

![](img/9c06e8d7fef1686118bc2a3e8fbd9f12.png)

我们看到了我们所期望的`400 Bad Request`，但是请注意，我们也得到我们的错误消息`requirement failed: If specified, the text can't be empty`。太好了！

我们的最后一个测试将确保我们的搜索端点不会被用来搜索所有的 todos，因为我们有一个不同的端点。(没有具体原因😁)

与前一个非常相似，但是这一次我们没有指定参数。让我们用 HTTP 客户端测试一下:

`http localhost:9000/todos/search`:

![](img/e3fd1af50d8629cb491beea6a94c89d4.png)

# 结论

我们学习了如何在我们的 API 中提取、解析甚至验证查询参数！

Akka HTTP 提供了许多实用程序，涵盖了大多数用例，如果没有，您可以选择自己用强大的原语来扩展这个库。

希望你喜欢这个教程，我们会很快见到你！

[想通过免费的分步课程学习如何构建基础项目？你当然知道！](http://link.codemunity.io/md-query-params)😁

*原载于*[*https://www . code unity . io*](https://www.codemunity.io/tutorials/akka-http-query-params/)*。*