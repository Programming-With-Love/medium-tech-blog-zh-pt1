# 我如何用 Go 播种我的数据库

> 原文：<https://medium.easyread.co/how-i-seed-my-database-with-go-27488d2e6a75?source=collection_archive---------2----------------------->

![](img/3a774aa2ce0734383745ead14a9b386a.png)

对于 Laravel 或其他编程语言中的任何其他框架，我最怀念的是移植和播种数据库的便利性。

我试图在 Go 中找到一个数据库播种器的包，我确实找到了一个[https://github.com/romanyx/polluter](https://github.com/romanyx/polluter)，但是看起来这个包并不是我真正需要的，因为使用`**polluter**`我将需要在。yaml 文件。

我需要一个更灵活的播种器，在那里我可以生成随机数据，执行 SQL 文件，或者也可以调用现有的用例/存储库。因此，在这篇文章中，我将尝试解释我是如何做到这一点的。

首先，我们需要在数据库中创建一个表，假设表名为`**customers**`。以下是生成该表的 SQL 脚本:

然后创建名为`**seeds**`的目录和名为`**seeder.go**`的新文件

```
**mkdir seeds && cd seeds && touch seeder.go**
```

并定义一个保存数据库的`**Seed**`结构，我们稍后将使用该数据库进行播种。

我在这篇文章中使用标准的`**database/sql**`包，你可以用你喜欢的包 tho 来改变它，比如 [ozzo-dbx](https://github.com/go-ozzo/ozzo-dbx) 。

然后我们创建一个名为`**customers.go**`的新文件，并在名为`**CustomerSeed**`的`**Seed**`结构上定义一个方法，这个方法就是我们实现数据库插入的地方，看看下面的代码:

在`**CustomerSeed**`方法中，我用 [faker](http://github.com/bxcodec/faker/v3) 包生成了 100 个客户数据，这个包用于生成**假的**数据，如姓名、地址和电话号码。

然后返回到`**seeder.go**`文件，用下面的代码添加一个函数`**seed**`:

在这个函数中，我们接收作为参数的`**Seed**`结构和要执行的函数/方法的名称。您可以看到，我们正在利用`**reflect**`包来执行基于方法名的特定方法(在本例中，我们希望执行我们的种子方法)。如果你还明白我的意思，**我们希望在运行程序**时能够将种子方法名作为参数传递。使用`**reflect**`，我们可以反映方法变量，并使用`reflect.Call`来调用它。

然后仍然在`**seeder.go**`文件中，我们将添加一个函数`**Execute**`，它接受两个参数，数据库和播种方法的名称(可选)。

如果没有传递函数/方法名，我们也在这里使用`**reflect**`来迭代`**Seed**` struct 上的所有方法，这样它将执行我们所有的 seeder 方法。

现在我们的`**seeder.go**`文件应该是这样的:

现在用下面的代码创建一个`**main.go**`文件:

在主文件中，我们收到一个参数`**seed**`,后面是我们要执行的播种方法的名称，如果没有给出播种方法名称，所有的播种器都将被执行。

现在，我们可以使用以下命令运行我们的播种机:

```
**go run main.go seed**
```

或者仅执行特定的播种方法

```
**go run main.go seed CustomerSeed**
```

或者如果您喜欢先构建它

```
**go build -o app main.go &&
./app seed CustomerSeed**
```

如果你想添加新的种子，你只需要在`**Seed**`结构上添加另一个方法，比如说`**ProductSeed**`

```
**func(s Seed) ProductSeed() {** **// insert product here
}**
```

您还可以使用多个参数运行种子程序:

```
**go run main.go seed CustomerSeed ProductSeed**
```

seeder 方法的实现更加灵活，您可以调用现有的存储库，甚至执行 SQL 文件，因为它只是常规的 Go 方法/函数。

我将演示如何在 seeder 方法中执行 SQL 文件。首先，使用以下脚本创建一个新的`**products**`表:

并在`**seeds**`目录下创建另一个目录和 SQL 文件

```
**cd seeds && 
mkdir scripts && 
cd scripts && 
touch products.sql**
```

使用以下脚本:

现在在`**products.go**`中创建一个新的播种方法

运行`**go run main.go seed ProductSeed**`，它将从 SQL 文件中获取产品数据。

你可以在我的 [Github](https://github.com/redhajuanda/goseeder) 上找到所有的代码。

希望这有所帮助！😉
另外，如果你有更好的想法，欢迎发表评论。