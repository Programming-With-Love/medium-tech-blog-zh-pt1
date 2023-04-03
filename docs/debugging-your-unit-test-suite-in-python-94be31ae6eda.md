# 用 Python 调试单元测试套件

> 原文：<https://medium.com/capital-one-tech/debugging-your-unit-test-suite-in-python-94be31ae6eda?source=collection_archive---------1----------------------->

## 轻松修复单元测试中的错误并模拟组件——如何在 pytest 中使用 Python 调试器 pdb

![](img/28d9360ef09cd1a65387c8a20293b8e5.png)

你有没有发现编写单元测试比实际的业务逻辑更难？Python 以使用简单的语言而闻名，但这并不总是扩展到单元测试；有些事情真的很难写测试。也许您正在测试的函数需要您 [*模仿*](https://docs.python.org/3/library/unittest.mock.html) *出一堆东西，或者也许它输出一个复杂的数据结构，比如 DataFrame，并且需要大量的断言来正确地指定想要的行为。*

无论如何，不管你是软件工程师还是数据科学家，写这种类型的测试都没什么意思。即使在最好的情况下，编写单元测试也可能是一项单调乏味的工作，但是随着模拟等困难的增加，完成它们可能会花费很长时间。在最坏的情况下，您可能会发现自己在通过试错法进行编程——您认为您的代码是正确的，但是您的测试失败了，所以您只是不断调整测试代码并重新运行，直到它通过。

不用说，试错法是编写测试的糟糕方法。这需要很长时间，可能会将一些 bug 放到测试套件中，而不是防止它们。幸运的是，有一个非常通用的工具可以让这变得更容易:Python 调试器`pdb`。如果您使用 pytest 来运行您的测试，它可以方便地与`pdb`集成。

在这篇文章中，我将演示如何使用`pdb`和 [pytest](https://docs.pytest.org/) 。首先，我将简要介绍调试器，以及它们与 Jupyter 等其他工具的比较。然后，我将转到一个模拟数据库连接的单元测试的工作示例。测试代码有一个令人困惑的错误，但是我们可以进入调试器来精确定位错误并解决问题——一个困难的问题变得容易了。

因此，让我们开始吧！

# 使用调试器的高级案例

`pdb`是 Python 自带的基本调试器。它可以让你暂停一个正在运行的程序，检查变量值，打印输出，甚至进行实时修改。如果你从未使用过`pdb`，或者甚至没见过像 Python 这样的解释型语言需要调试器，那么它的杀手锏就是这个- *当遇到异常时，你可以进入一个交互式的环境，而不是让程序崩溃*，这也被称为事后调试。这让您可以准确地看到什么是坏的，什么是需要的，将令人沮丧的试错经验变成快速简单的练习。

如果你在 Jupyter 或类似的环境中工作过，你可能已经习惯了打印东西和在出错后四处查看，但是`pdb`更进了一步。当 Jupyter 让你回到代码的顶层时，`pdb`会在出现异常的那一行暂停——当错误发生在函数调用深处时，这是一个无价的特性。

假设你调用了一个函数，这个函数调用了另一个函数，这个函数调用了另一个函数，这个函数遇到了一个错误，并给出了一个令人困惑的消息，可能是 Python 内核深处的某个东西，或者是像 Pandas 这样的库。实际的 bug 可能在这些中间步骤中的一个，而不是在顶部(您的初始代码行)或底部(库代码)。这就是为什么`pdb`也有方便的命令`up` 和`down`，让你浏览调用堆栈，检查错误追溯的每个级别中的所有局部变量。

关于`pdb`更详细的介绍，我推荐 [Nathan Jennings 在 Real Python 的 pdb 教程](https://realpython.com/python-debugging-pdb/)。对于 Jupyter 爱好者来说，尝试在一个异常后在下一个单元格中运行`%debug`。

# 使用 pytest — pdb

如果您使用 py test-pdb 运行您的测试，它将在每次测试失败或出现某种错误时自动进入调试器。如果您喜欢在日常工作中使用 REPL、IPython 或 Jupyter，那么您就会知道在尝试修复错误时，在交互式环境中使用它们是多么方便！Pytest 有许多有助于编写单元测试的特性，但这是最通用的特性之一。

如果您想在特定的点开始调试，您也可以在感兴趣的点之前添加一个`[breakpoint()](https://docs.python.org/3/library/functions.html#breakpoint)`行(在 Python 3.7+中可用)。

一旦到达那里，您就可以使用所有常用的[调试器命令](https://docs.python.org/3/library/pdb.html#debugger-commands) — `break`、`step`、`next`、`return`、`up`、`down`、`continue`，以及您想要执行的任意 Python 代码。

Ctrl+D 或`exit`会让你退出调试器。

# 示例—使用模拟数据库调试单元测试

我发现使用 pdb 真正有用的一个地方是当我用模拟组件编写测试时。例如，我可能有一些查询数据库的函数，以便进行一些计算。实际上，我不想在单元测试中连接到数据库，因为那样会很慢，并且需要用户名和密码。相反，我可以模拟数据库连接并模拟查询结果。

# 编写业务逻辑和测试用例

让我们看看它在代码中会是什么样子——如果您想继续，请随意复制并粘贴这些代码片段，并在您的计算机上运行它们。

首先，让我们勾画出我们的实际功能。我们使用[Capital One 开源项目 locopy](https://github.com/capitalone/locopy) 来连接到我们的数据库，它提供了一个方便的`ContextManager`，我们可以使用`with`关键字来获得一个连接，这个连接会自动清理。万一你没有安装 locopy，我们可以为`Database`定义一个存根类，让测试暂时运行。

```
# my_pkg.py
try:
   from locopy import Database
except (ImportError, ModuleNotFoundError):
   class Database: pass

connection_params = {}

def refresh_views(purge_expired: bool = False):
   with Database(**connection_params) as db:
       db.execute("query1")
       db.execute("query2")

       if purge_expired:
           db.execute("query3")
```

所以我们的函数连接到数据库并运行一些查询(`query1`和`query2`)。如果我们设置了`purge_expired`标志，它也会运行`query3`。

现在让我们编写一个测试用例来确保`purge_expired`选项按预期工作。

```
# test_my_pkg.py
from unittest.mock import patch
import pytest
from my_pkg import refresh_views

@patch("my_pkg.Database")
def test_refresh_views(mock_Database):
   mock_db = mock_Database()

   # make sure query3 isn't run
   refresh_views(purge_expired=False)
   with pytest.raises(AssertionError):
       mock_db.execute.assert_called_with("query3")

   # make sure query3 _is_ run
   refresh_views(purge_expired=True)
   mock_db.execute.assert_called_with("query3")
```

使用 unittest.mock 中的`@patch`,我们可以在测试运行时用一个`MagicMock`实例替换`Database`类。如果您对它不熟悉，`MagicMock`提供了一个虚拟对象，它将假装拥有您需要的任何方法或属性(尽管默认情况下它们实际上不做任何事情)。在您的测试中，您可以检查 mock 以查看它的哪些方法被调用，或者提供它将代表您返回的测试数据。

在这种情况下，我们正在检查当我们设置`purge_expired=False`时`query3`不会被数据库执行，而当我们设置`purge_expired=True`时会被执行。

模拟真的很方便，但是`MagicMock`与普通的 Python 对象很不一样，用它编程会很混乱。这是事实，因为我们通常只在单元测试环境中使用模拟，所以我们大多数人没有太多的实践经验。

特别注意我们定义`mock_db`的方式，它应该匹配`refresh_views`中的变量`db`。`@patch`让我们用`mock_Database`替换`Database`类本身，但是我们没有直接使用这个类；我们正在使用它的一个实例。这意味着我们需要“实例化”我们的模拟，因此有了`mock_db = mock_Database()`。除了我们在原始代码中所做的每一个转换，我们都应该应用到 mock 中，这并没有什么特别的意义。

现在让我们运行我们的测试……我们得到一个错误。

```
@patch("my_pkg.Database")
   def test_refresh_views(mock_Database):
       mock_db = mock_Database()

       # make sure query3 isn't run
       refresh_views(purge_expired=False)
       with pytest.raises(AssertionError):
           mock_db.execute.assert_called_with("query3")

       # make sure query3 _is_ run
       refresh_views(purge_expired=True)
>      mock_db.execute.assert_called_with("query3")

E           AssertionError: expected call not found.
E           Expected: execute('query3')
E           Actual: not called.
```

太奇怪了。

# 调试测试用例

那么，我们的下一步是什么？mock 可能会出很多问题——可能是`query3`从未被执行(也就是说，测试理所当然地失败了),补丁没有正常工作，或者也许我们没有在 mock 上完美地复制`Database`和`db`之间的所有步骤。

在这种情况下，我的第一步是用`--pdb`选项重新运行测试，并开始查看。

```
~/miniconda3/lib/python3.9/unittest/mock.py:898: AssertionError
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> entering PDB >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

>>>>>>>>>>>>>>>>>> PDB post_mortem (IO-capturing turned off) >>>>>>>>>>>>>>>>>>>
> ~/miniconda3/lib/python3.9/unittest/mock.py(898)assert_called_with()
-> raise AssertionError(error_message)
(Pdb)
```

回溯从库函数内部开始，所以我执行一次`up`来返回到我的测试代码。

```
(Pdb) up
> ~/dev/test_my_pkg.py(17)test_refresh_views()
-> mock_db.execute.assert_called_with("query3")
```

好了，现在我们可以检查一下 mock_db 了。

```
(Pdb) mock_db (Pdb) mock_db.execute (Pdb)
```

我们可以看到`mock_db`应该是一个`Database`实例。奇怪的是，execute 方法似乎从来没有被调用过，因为它有一个空的调用列表。暂时退出调试器，让我们从实际的实现代码内部再试一次。我会在应该执行`query3`之前设置一个断点。

```
def refresh_views(purge_expired: bool = False):
   with Database(**connection_params) as db:
       db.execute("query1")
       db.execute("query2")

       if purge_expired:
>          breakpoint()
           db.execute("query3")
```

现在，当我们运行测试时，我们在这一行进入调试器。第一步是从内部看看`db`是什么样子——它到底是不是一个 mock，如果是，在它上面调用了什么方法？

```
> ~/dev/my_pkg.py(16)refresh_views()
-> db.execute("query3")(Pdb) db
```

马上就跳出来说`db` *是*一个嘲弄，但不仅仅是`Database()`而是`Database().__enter__()`。那是从哪里来的？

嗯，如果我们看看它的定义，我们会发现它来自一个`with`语句——这意味着它实际上是一个`ContextManager`。

```
with Database(**connection_params) as db:
```

就我个人而言，我在 Python 中一直使用`with`语句，但通常不会创建自己的自定义`ContextManagers`。所以当我最初怀疑`with`语句时，我不记得用 mock 复制的确切语法了。

# 应用修复

现在我们知道了问题，我们可以修复我们的测试代码了。我们可以手动复制数据库上的方法调用链…

```
@patch("my_pkg.Database")
def test_refresh_views(mock_Database):
>  mock_db = mock_Database().__enter__()

   # make sure query3 isn't run
   refresh_views(purge_expired=False)
   ...
```

或者，我们可以用`with`来定义它，就像在业务逻辑中一样。非常直观。

```
@patch("my_pkg.Database")
def test_refresh_views(mock_Database):
>  with mock_Database() as mock_db:
       # make sure query3 isn't run
       refresh_views(purge_expired=False)
```

在那个小小的编辑(和删除`breakpoint()`)之后，测试通过了！

```
========================= 1 passed in 0.64s =========================
```

如果几年前你问我，我可能都不知道该在谷歌上搜索什么来解决这个问题。但是有了调试器，我可以毫不费力地马上解决这个问题。能够自助是一件很美好的事情！

# 包裹

当你可以亲自检查每个变量时，修复错误就容易多了，这同样适用于测试代码，就像它适用于其他任何东西一样。但是，即使您习惯于在 Jupyter 中闲逛，也不清楚如何在单元测试中应用同样的技术。更不用说，`MagicMock`的行为与普通 Python 对象如此不同，以至于很容易混淆。这就是`pytest --pdb`和`breakpoint()`的作用。

因此，至少在编写单元测试时，要告别试错编程和无休止的谷歌搜索。有了这个技巧，你最沮丧的测试错误应该是小菜一碟。🎂

*披露声明:2021 首都一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*原载于*[*https://www.capitalone.com*](https://www.capitalone.com/tech/software-engineering/how-to-use-python-degugger-pdb/)*。*