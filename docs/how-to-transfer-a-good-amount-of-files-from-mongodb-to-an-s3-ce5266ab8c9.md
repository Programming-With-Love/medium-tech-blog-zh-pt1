# 如何将大量文件从 MongoDB 传输到 S3

> 原文：<https://medium.com/quick-code/how-to-transfer-a-good-amount-of-files-from-mongodb-to-an-s3-ce5266ab8c9?source=collection_archive---------1----------------------->

## 一天内迁移大量文件

![](img/e166f7155d21230aa59a20ea8b2c834b.png)

> 在这个故事中，也是我的第一个故事，我们将深入到将 MongoDB 文件迁移到 S3 对象存储的过程中(尽管如果您想将它们迁移到其他地方，您可以遵循迁移之前的所有步骤)。
> 
> 我们将简要介绍 mongo 如何存储文件以及如何访问它们。然后，我们将了解如何将这些文件导出到您的服务器磁盘，最后，如何将它们上传到您的 S3 实例。
> 
> 但是首先，有一点背景故事关于我如何结束需要转移 41GB 的 300K 图像文件。
> 
> (为了坚持到底，如果您对 Linux 生态系统有一个基本的了解将会有所帮助)

几年前，我们开始开发一个社交移动应用，它使用[解析服务器](https://github.com/parse-community/parse-server)作为后端。对于那些不知道的人来说，Parse Server 是一个`Node.js`基础设施，它利用了`express`框架的优点，并将`MongoDB`用作数据库。

默认情况下，Parse Server 有一个文件适配器，可以让你非常容易地将应用程序文件直接存储到 MongoDB 中*(他们也提供 S3 和 GCS 文件适配器)*。于是负责后端的无知的过去的自己，以及 app 的 iOS 部分，认为让它保持默认的方式是一个非常方便/省钱的想法。

两年和 **30 万个图像文件**之后，我们的应用程序增长了很多，我们决定将所有这些文件存储到我们的 MongoDB，这可能不是最好的主意！就这样，我们的文件传输之旅开始了！

# 首先，MongoDB 到底是怎么存储文件的？

嗯，经过一些研究，我发现 MongoDB 有一个名为`GridFS`的文件系统，它负责存储和检索文件。它是这样工作的。共有两个系列，`fs.files`和`fs.chunks`。第一个包含您存储的每个文件的元数据，如其名称和创建日期。后者包含——除了别的以外——二进制格式的文件的实际数据。

下面是文件在 **fs.files** 中的实际样子:

```
{
  "_id" : ObjectId("5ae97922c1dabec8d2d0bdb0"),
  "filename" : "2b57455f3878d11dabc9c984da7de314_postImage.jpeg",
  "contentType" : "binary/octet-stream",
  "length" : 2291623,
  "chunkSize" : 261120,
  "uploadDate" : ISODate("2018-05-02T08:38:58.549Z"),
  "aliases" : null,
  "metadata" : null,
  "md5" : "9ad420eaa7c28a73e449199430627802"
}
```

下面是它在 **fs.chunks** 中的样子:

```
{
  "_id" : ObjectId("5ae2d77f6616b4a9d93cb4b1"),
  "files_id" : ObjectId("5ae97922c1dabec8d2d0bdb0"),
  "n" : 0,
  "data": BinData(0,"iVBORw0KGgoAAAANSUhEUgAAAuAAAAJvCAYAAAA6OGQEAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAABWWlUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iWE1QIENvcmUgNS40LjAiPgogICA8cmRmOlJERiB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRher38tcAAAAASUVORK5CYII=....")
}
```

那么，我们现在到底在看什么？

**fs.files** 非常简单，让我们从那里开始吧。每个文档有 9 个标准字段，其中 3 个是我们现在关心的:`_id`、`filename`和`uploadDate`。那三个字段以后会派上用场。

现在让我们来看看 **fs.chunks** 。我们看到它有一个`_id`字段，就像 mongo 中的任何其他文档一样。接下来，我们看到它有一个`files_id`字段。这是其关联的`fs.files`文档的 id 引用。接下来是`n`字段，它是块的序列号。现在，如果你是 MongoDB 的初学者，你可能会说“老兄，你把我搞糊涂了！”。你看，mongo 不仅仅是把一个文件放到一个文档中。它实际上把它分成几部分——或者几大块😉—每个 255kB。因此，如果你去存储一个 400kB 的文件，它将创建 2 个`fs.chunks`文档…一个 255kB，值为 0，另一个 145kB，值为 1。最后，我们有`data`字段，它包含文件的部分数据(取决于文件是否被分割成 1 个以上的块。如果不是，则包含全部。).

> 如果您想亲眼看看您的文件在 mongo 中的样子，请连接到您的 mongo shell 并键入:

```
use your_db_name
db.fs.files.findOne()
```

> 要查找相关的块，请键入:

```
db.fs.chunks.find( { files_id: ObjectId('the _id your fs.files document has') } )
```

现在我们已经很好地理解了 MongoDB 中文件的结构，让我们进入下一部分！

# 如何将文件从数据库导出到磁盘？

那是我最纠结的部分！在这之前，我对 mongo 没有很好的了解，所以我做了每个开发人员发现自己走进死胡同时都会做的事情… google 我的出路！

显然，mongo 提供了一个非常方便的命令行工具——这意味着您可以从 bash shell 而不是 mongo shell 运行它——称为`mongofiles`。它所做的事情之一，是在本地导出您要求的实际文件，无论指定在哪里。

让我们先看一个更简单的例子:

```
mongofiles -d db_name list
```

它将向您显示一个列表——仅仅是文件名和它们的大小，**而不是实际的文件**——所有您存储在数据库中的文件。

> **注意:**如果您已经启用了对数据库的认证访问，这意味着您已经设置了用户名和密码，您将需要在命令中包含以下参数:
> 
> `-u`:用户名
> `-p`:密码
> `--authenticationDatabase` : auth_db_name **//大概管理员**

现在来看看更有趣的东西:

```
mongofiles -d db_name get filename.jpeg
```

这里我们**实际上从数据库中导出了一个名为`filename.jpeg`的文件**，并将其存储到当前目录中。很简单，对吧？好吧，如果我们只想要一个文件，是的！但是我们想要导出每一个，所以事情变得有点复杂！你看，因为我们需要我们想要导出的每个文件的名称，我们必须先得到它们，然后为每个文件调用`mongofiles get`。那么我们该怎么做呢？

我们可以像以前一样使用`list`参数来获取每个文件名，但是我们将面临三个问题:

1.  通过使用`list`，我们实际上从数据库中请求每一个文件名！如果我们有一千或两千个文件，当然…继续！但是如果你有 100K 呢？20 万？你需要等待相当长的时间来获取文件名，并且要花很长时间来导出它们。我实际计算了一下，对于我的机器来说，导出 41GB 的 300K 文件大约需要 10 个小时！
2.  我们必须解析出每个文件的返回大小，因为我们只需要文件名。
3.  当我们导出所有这些文件时，新的文件随时可能被存储！尤其是如果你的应用有很多流量，它们可能太多了！那些会怎么样？我们不能只说“谁在乎？别管他们了！”我们也不能一遍又一遍地重复整个过程。

显然这不是一个可行的解决方案，**所以这是我决定要做的:**我使用了`mongoexport`命令行工具，根据文件名的`uploadDate`来获取文件名(`mongoexport`提供了一个查询参数，所以我们可以非常具体地搜索什么文件)。这样，我可以将文件名从`uploadDate`导出到`uploadDate`。比如在`2018–07–01`和
T10 之间。之后，我会将它们上传到我的 S3——稍后会详细介绍——然后，我会从`2018–12–02`和`2019–03–01`导出文件名。我重复这个循环，直到我找到最后一天的文件。

下面是我如何使用`mongoexport`:

```
mongoexport -d db_name -c fs.files -f filename -q '{ uploadDate: { $gte: new Date("2019-12-01T00:00:00.000Z"), $lt: new Date("2019-12-16T16:35:00.000Z") } }' --csv -o 2019-12-01-to-2019-12-16.csv
```

> **注 1:** 同样，如果您已经启用了对数据库的认证访问，您将需要在命令中包含`-u`、`-p`和`--authenticationDatabase`参数。
> 
> **注 2:** 在我们运行这个命令之后，我们将得到一个导出文档数量的提示(在我们的例子中只是文件名)。记住这个数字很有帮助，可以在以后用导出文件的数量进行交叉验证。

让我们快速浏览一下我们使用的论点:

> `-d`:数据库名称
> `-c`:我们要搜索的集合(在我们的例子中是`fs.files`)
> `-f`:我们要导出的字段(因为我们只需要`filename` s，这就是我们要设置的)
> `-q`:搜索`fs.files`集合所基于的查询
> `--csv`:文件名要导出到的文件的类型 **//我们使用 csv，因为它最适合我们**
> `-o`:`-o`

所以我们有了我们想要的文件名。如果我们`cat`这个`.csv`文件，我们会看到那个列表。

现在是时候使用那个列表，并最终从数据库中导出我们的文件了！

为此，我们将创建一个名为`export_files.sh`的`bash script`。我们就是这样做的，，因为这是一个更好的实践，而不是每次只键入命令:

```
#!/bin/bash# Define a timestamp functiontimestamp() {
    date +"%T"
}MONGO_DATABASE="db_name"
MONGO_HOST="127.0.0.1"
MONGO_PORT="27017"
DATABASE_USERNAME="username"
DATABASE_PWD="password"
DATABASE_AUTH_DB="admin"echo "Started at $(timestamp)"xargs <"$1" -P$(nproc) -n1 sudo mongofiles -h "$MONGO_HOST" -d "$MONGO_DATABASE" -u "$DATABASE_USERNAME" -p "$DATABASE_PWD" --authenticationDatabase "$DATABASE_AUTH_DB" --quiet getecho "Finished at $(timestamp)"
```

代码的简要描述:

*   我们首先声明一个 timestamp 函数，我们将在开始导出文件之前和完成之后调用它，只是为了了解它花费了多少时间。
*   然后我们声明一些必要的变量，我们需要使用它们作为`mongofiles`命令的参数。
*   接下来，有趣的事情！在这里，我们将把命令分成两部分:

1.  `xargs <”$1" -P$(nproc) -n1` : `xargs`逐行读取输入(`“$1”`)，并将每行(文件名)作为参数传递给进程。`-n1`告诉 xargs 为每一行/文件名运行命令。`nproc`是一个获取 CPU 数量的 linux 实用程序，该选项被传递给`-P`，后者指定并发运行的进程数量。
2.  `sudo mongofiles -h “$MONGO_HOST” -d “$MONGO_DATABASE” -u “$DATABASE_USERNAME” -p “$DATABASE_PWD” — authenticationDatabase “$DATABASE_AUTH_DB” --quiet get`:这将每个文件名作为命令前一部分的输入，并最终将其导出。

> **注意:**为了运行上面的脚本，你需要给它执行权限，像这样:`chmod +x export_files.sh`。

如果您继续运行`./export_files.sh filenames.sh`，导出将开始！*(记住，你指定导出的文件越多，花费的时间就越多)*

脚本完成后，运行`ls`来查看所有导出的文件。接下来，您可以运行`find . -type f | wc -l`来确保导出文件的数量等于导出到。csv 文件(记住，我之前提过)。如果你想查看这些文件的总大小，运行
`du -h .`。

> **提示:**确保您创建了一个目录，所有文件都将导出到该目录。我所做的是为我要导出的每组文件创建目录。所以保持干净！相信我，你不会想在你的主目录中有成千上万的文件的！

该解决方案有许多优势，例如，如果需要，我可以随时停止，然后从我停止的地方继续，我不必让我的服务器连续 10 个小时处于这种压力下，更重要的是，我可以非常轻松地传输最后剩余的文件，即那些在我们导出时创建的文件。此外，通过使用`xargs`，我们有能力运行的东西更快，所以这绝对是一个加号！

所以我们得到了文件…现在让我们把它们转移到我们的 S3！

# 如何将这些文件传输到我的 S3 兼容对象存储器？

> **注:**注意到我在标题中使用的**兼容**关键字了吗？原来，不仅亚马逊提供 S3 对象存储服务…还有数字海洋，还有其他的！

所以我们开始吧！我们的最后一步！如果你能走到这一步，相信我，这很容易！

为了将文件从您的服务器传输到您的 S3，我们需要一个工具来完成这项工作。如果你谷歌一下，你会发现很多！甚至有这方面的浏览器！我们要用的工具，叫做[**S3-并行-放**](https://github.com/mishudark/s3-parallel-put) **。**(点击查看安装方法)

我之所以选择这个工具，是因为——顾名思义——它可以并行传输文件。如果您有多个 GB 的数据，这一点非常重要！在我们使用 s3-parallel-put 之前，请确保您已经安装了它，并且位于正确的目录中——导出文件的位置——并且没有任何其他不想传输的文件，比如 bash 脚本。

准备好了吗？键入以下内容:

```
s3-parallel-put --bucket=bucketName --bucket_region=region --host=region.host.com --put=stupid --processes=13 --grant=public-read .
```

简单吧？让我们来看看如何使用它:

> `--bucket`:那是你的 S3 桶的名字
> `--bucket_region`:你为你的桶选择的地区，像`us-east-1`，或者`fra1`等
> `--host`:你提供的主机名，像`s3.amazonaws.com`或者`digitaloceanspaces.com
> --processes`:那是它可以创建多少个进程，为了更快的传输文件。这个数字取决于你的机器有多少个 CPU * 3。(3 不是“正确”的数字…您应该用不同的值来测试它，以找出哪个数字更适合您)
> `--grant=public-read`:您的文件应该具有的访问类型
> `.`:您导出的文件所在的本地目录(在我的例子中是当前目录)

就是这样！文件传输需要一些时间，但是我们已经完成了(至少对于当前的一批文件来说)。没那么难，对吧？你现在所要做的，就是对剩下的文件重复导出和上传的步骤，直到你完成！

# 最后的想法

现在我们完成了，让我们总结一下所有需要的步骤:
首先，我们必须根据文件名的`uploadDate`找到文件名。我们使用了`mongoexport`命令。接下来，使用`bash script`导出这些文件。最后，使用`s3-parallel-put`上传导出的文件。重复一遍。

我还想提一下 [s3cmd](https://s3tools.org/s3cmd) ，这是一个可以在 S3 上轻松搜索、删除甚至上传文件的工具，尽管我们不会用它来上传，因为它会慢很多。我想你会发现它很有用，万一出了问题，需要重新开始(希望不会)。

> **注意:**请记住，您可能需要用来自 S3 的新 URL 替换数据库中存储的文件 URL，因为它们不再托管在您的数据库中。谢天谢地，我不用处理它…解析服务器的文件适配器处理了它。

当我不得不做这个改变的时候，我找不到一个完整的指南来指导我怎么做！我找了又找，我不得不到处收集碎片…这太令人沮丧和疲惫了！所以我希望读这篇文章时，你会发现它比我更容易！

祝你好运！🙏