# 使用 sqf lite-Flutter 简化您的应用程序💙

> 原文：<https://medium.com/google-developer-experts/sqflite-flutter-1d0c0737ca4b?source=collection_archive---------2----------------------->

## 不能上网？想要将您的数据存储在本地存储中吗？别担心！SQFLite 将会拯救你！

![](img/4e9af4ef1dc01a9a00082644acd5f94f.png)

互联网是世界上发生的最好的事情之一！但是，您的用户可能并不总是连接到互联网。或者您可能需要在本地存储一些东西。这就是我们可以使用 SQFLite 的地方，它在用户的设备中创建一个基于 SQL 的本地数据库！

让我们看看如何使用 SQFLite 执行 CRUD 函数！

首先在 pubspec.yaml 文件中包含 [sqflite](https://pub.dev/packages/sqflite) 和 [path_provider](https://pub.dev/packages/path_provider) 包！需要 path_provier 来获取存储 DB 文件的路径。您也可以执行所有原始 SQL 查询！

# 创建一个数据库并打开它…

一旦添加了这两个包，就需要创建一个包含所有表的数据库。您可以使用以下代码片段来实现这一点:

```
static late Database database;
  static Future<void> createDB() async {
    Directory documentsDirectory = await getApplicationDocumentsDirectory();
    String path = documentsDirectory.path + "sqflite.db";
    database = await openDatabase(path);
    await createTable();
  }
```

这里，我们创建了一个函数，其中我们首先获取路径，然后在该路径中创建一个数据库！创建并打开数据库后，我们调用了 createTable()函数，这是一个用户定义的函数。

# 创建表格…

您可以使用以下代码片段在数据库中创建一个表:

```
static Future<void> createTable() async {
    await database.execute('CREATE TABLE SQFLITE (first_name TEXT, last_name TEXT)');
  }
```

这里，我们创建了一个名为 SQFLITE 的表，它有两列:first_name 和 last_name，它们的数据类型是 TEXT！

# 将数据插入表格…

一旦创建了表，就可以使用下面的代码片段将数据插入到该表中:

```
static Future<void> insertData({required User user}) async {
    await database
        .rawInsert('INSERT INTO SQFLITE(first_name, last_name) VALUES("${user.firstName}", "${user.lastName}")');
  }
```

在这里，我们将名字和姓氏插入到 SQFLITE 表中！

# 从表中获取数据…

您可以使用以下代码片段从表中检索数据:

```
static Future<User> getData({required String firstName}) async {
    List<Map> list = await database.rawQuery('SELECT * FROM SQFLITE WHERE first_name = "$firstName"');return User(
        firstName: list.length > 0 ? list[0]['first_name'] : "", lastName: list.length > 0 ? list[0]['last_name'] : "");
  }
```

在这里，rawQuery 函数返回列表<map>，其中包含满足给定查询的所有条目。如果你愿意，你可以使用一个循环或者返回整个列表。或者你可以像上面的例子一样返回第 0 个索引数据。</map>

# 更新特定行的数据…

您可以使用我们的原始 SQL 查询来更新特定行的数据，如下所示:

```
static Future<void> updateData({required User user}) async {
    await database
        .rawUpdate('UPDATE SQFLITE SET last_name = "${user.lastName}" WHERE first_name = "${user.firstName}"');
  }
```

这里，我们更新了名与用户提供的名相匹配的特定行的姓。

# 从表中删除数据…

您可以从表格中删除一行，如下所示:

```
static Future<void> deleteData({required String firstName}) async {
    await database.rawDelete('DELETE FROM SQFLITE WHERE first_name = "$firstName"');
  }
```

这里，我们删除了名字与用户提供的名字相同的行。

# 关闭数据库…

最后，在所有任务完成后，您需要关闭数据库，这样就不会有内存泄漏或未使用的资源。您可以通过下面的函数简单地做到这一点:

```
static Future<void> closeDB() async {
    await database.close();
  }
```

你也可以从 [GitHub](https://github.com/AbhishekDoshi26/sqflite_example) 中得到同样的小例子！

希望你喜欢这篇文章！

如果你喜欢，你可以 [**给我买杯咖啡**](https://www.buymeacoffee.com/abhishekdoshi26) **！**

[![](img/fa704f2ad01834f36e82857a82497eff.png)](https://www.buymeacoffee.com/abhishekdoshi26)

[https://www.buymeacoffee.com/abhishekdoshi26](https://www.buymeacoffee.com/abhishekdoshi26)

# 不要忘记通过以下方式与我联系:

*   [**Instagram**](https://www.instagram.com/abhishekdoshi26/)
*   [**推特**](https://twitter.com/AbhishekDoshi26)
*   [**LinkedIn**](https://www.linkedin.com/in/AbhishekDoshi26)
*   [**GitHub**](https://github.com/AbhishekDoshi26)

> 不要停止，直到你在呼吸！💙
> -阿布舍克·多希