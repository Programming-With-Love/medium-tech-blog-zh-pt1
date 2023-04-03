# 发布 SQLDelight 1.0

> 原文：<https://medium.com/square-corner-blog/announcing-sqldelight-1-0-d482aa408f64?source=collection_archive---------0----------------------->

![](img/dc11d69aa8e31892d9702d6bd1c41415.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

SQLDelight 在 4 年前作为一个项目开始于 Android 的 ContentValues 和 SQLiteOpenHelper APIs，目标是使编写 SQL 变得简单和安全。该库在内部是 Kotlin 的早期采用者，但从一开始就一直在生成 Java。我们热爱 Kotlin，并且对我们知道可以在 Kotlin 中做得更好的 Java API 不满意，所以一年前我们开始了完全专注于 Kotlin 的全面重写。

与此同时，Kotlin multiplatform 刚刚宣布，并承诺在 Android 和 iOS 之间轻松安全地共享代码。对于 Cash App 来说，这是共享有意义的代码的完美的第一步，一旦模式被共享，上面的一切都可以实现:创建视图模型，与服务器同步，运行测试。因此，经过一年多的重新编写 SQLDelight，我们很兴奋地开始谈论新版本以及它为 Android 和多平台开发带来的变化。

SQLDelight 的前提是不变的:编写 SQLite，让 Gradle 插件生成 API 来为你运行你的查询。SQLDelight 文件使用`.sq`扩展名，并且包含在 Gradle 模块的`src/main/sqldelight`文件夹中。

一个包含一些`CREATE TABLE`、`INSERT`和`SELECT`语句的简单文件就是您开始的全部所需:

```
-- src/main/sqldelight/com/sample/TennisPlayer.sqCREATE TABLE TennisPlayer(
  name TEXT,
  points INTEGER,
  plays TEXT AS Handedness
);insert:
INSERT INTO TennisPlayer
VALUES (?, ?, ?);top10:
SELECT *
FROM TennisPlayer
ORDER BY points DESC
LIMIT 10;
```

SQLDelight 将生成一个可以运行这些查询的`TennisPlayerQueries`类。

```
val tennisPlayers: TennisPlayerQueriestennisPlayers.insert("Naomi Osaka", 5270, Handedness.RIGHT)
tennisPlayers.insert("Aryna Sabalenka", 3365, Handedness.RIGHT)
tennisPlayers.insert("Simona Halep", 6641, Handedness.RIGHT)val top10: List<TennisPlayer> = tennisPlayers.top10()
  .executeAsList()println(top10[0].name) // prints "Simona Halep"
```

获取`TennisPlayerQueries`对象需要平台驱动程序。SQLDelight 包括在 Android、JVM 和 iOS 平台上使用 SQLite 的驱动程序。更多详细信息，请参考[自述文件](https://github.com/square/sqldelight)。

对于 Square SQLite 库的早期用户来说，代码生成已经使用 Kotlin 进行了彻底检查，这意味着为 Kotlin 用户生成了一个基于 SQL 的新 API。为了从 SQLDelight 的早期版本中迁移您的代码，我们发布了一些工件并有一个[简短指南](https://github.com/square/sqldelight/blob/master/UPGRADING.md)。

编译后的代码还会跟踪活动的查询，并在结果发生变化时通知下游。有一个扩展可以将这种行为公开为 RxJava Observable，这意味着这个版本也反对[SQL write](https://github.com/square/sqlbrite)。我们还添加了一个 [Android 分页](https://developer.android.com/topic/libraries/architecture/paging/)扩展，允许通过`DataSource`暴露查询。

还有许多其他功能，如迁移验证、自定义类型和设置参数，这些都记录在自述文件中。试一试，如果遇到任何问题，请联系我们！