# Python 类型提示:消除循环导入导致的导入错误

> 原文：<https://medium.com/quick-code/python-type-hinting-eliminating-importerror-due-to-circular-imports-265dfb0580f8?source=collection_archive---------0----------------------->

![](img/c0714b38db62e8ec8611ba843ff6a17c.png)

Photo by [Chris Ried](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

由于循环导入只发生在类型提示中，我们有时都会遇到`ImportError`。处理这类问题有一个简单的方法。

假设我们有如下两个文件:

另一个文件:

这两个文件将很好地工作，因为我们没有添加任何类型提示。只有`BookManager`被导入到`book_model.py`文件中。但是如果我们将类型提示添加到来自`BookManager`的`create_new_version_of_book`方法中，那么它将如下所示:

现在，由于循环导入，我们将得到一个`ImportError`，当我们想要运行项目/文件时，我们将得到类似下面的消息:

```
ImportError: cannot import name 'BookManager' from partially initialized module 'mymodule.models' (most likely due to a circular import)
```

解决方法是使用`typing.TYPE_CHECKING`常量，如下所示:

**第 1 行:**我们导入了`annotations`。`__future__.annotations`现在在 Python 中不是默认的；但在 Python 3.11 中会成为默认。详细情况你看一下 [PEP 563](https://www.python.org/dev/peps/pep-0563/#abstract) 。如果不导入，可以将类型提示作为字符串使用。在我们的例子中，它将是`old_book_object: ‘Book’`。

**第 3 行:**我们导入了`typing.TYPE_CHECKING`特殊常量。常量的值始终是`False`，但是被任何类型检查器设置为`True`，例如`Mypy`。我们已经使用 this 常量使我们的导入有条件。

**第 7–8 行:**我们已经带条件导入了模型，所以只有当`TYPE_CHECKING`变量为`True`时才会导入。

希望，这能有所帮助。:)