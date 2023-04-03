# 使用 GridFS 进行文件存储和检索

> 原文：<https://medium.com/version-1/file-storage-and-retrieval-using-gridfs-30169630e75a?source=collection_archive---------0----------------------->

![](img/056345ebd003301b9cabd2c7c748a817.png)

Photo by [Ilya Pavlov](https://unsplash.com/@ilyapavlov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

GridFS，正如我在[之前的博文](/version-1/an-introduction-to-gridfs-e8de4537cd4)中所讨论的，是对 Mongo DB 的抽象，它利用 Mongo DB 驱动程序来存储文件，并在需要时检索它们。让我们深入研究如何实现 GridFS 来上传和下载文件。

[](/version-1/an-introduction-to-gridfs-e8de4537cd4) [## GridFS 简介

### MongoDB 是领先的 NoSQL、开源、面向文档的数据库平台。然而，BSON 的最大尺寸…

medium.com](/version-1/an-introduction-to-gridfs-e8de4537cd4) 

> 注意:这篇文章使用了 C#驱动程序和 GridFS 异步方法。

**初始化 GridFS**

第一步是创建一个 GridFS bucket，它将存储文件和块集合。使用桶来访问集合。

安装 NuGet 包 **MongoDB。Driver.GridFS** 并创建 GridFS bucket，如下所示:

Creating the GridFS Bucket

**上传文件**

一个文件可以通过以下几种方式上传:
1。将文件内容作为字节数组上传。
2。从源流执行上传。
3。将文件内容写入输出流。

下面的代码描述了如何将文件内容写入输出流。

Uploading to an output stream

**检索文件**

要检索文件，最简单的方法是以字节数组的形式获取文件内容。

第一个方法演示了如何使用文件名获取文件内容。默认情况下，这将检索文件的最新版本。如果您需要获取特定的修订，可以使用选项。

FindAsync 方法可用于使用所需的过滤器查找特定文件。该函数返回存储在 files 集合中与特定文件相关的所有数据。如方法二所示，这个对象的 id 参数可以用来从 GridFS 下载文件。

检索文件的其他选项包括:

1.  将文件内容写入流。
2.  从流中读取文件内容。

**结论**

在选择系统之前，需要考虑许多因素，如文件大小、元数据存储、文件数量等。正如在[之前的文章](/version-1/an-introduction-to-gridfs-e8de4537cd4)中所述，GridFS 是 Mongo DB 提供的一个存储大文件的很好的工具，但是它有优点也有缺点。在某些场景中，使用 GridFS 可能是有意义的，但对于其他场景则不是这样。因此，您必须在仔细评估利弊后做出明智的决定。

**关于作者:
Geethu Suresh 是微软的一员。Net 顾问在这里的版本 1。**