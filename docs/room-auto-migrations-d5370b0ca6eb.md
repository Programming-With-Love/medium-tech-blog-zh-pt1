# 房间自动迁移

> 原文：<https://medium.com/androiddevelopers/room-auto-migrations-d5370b0ca6eb?source=collection_archive---------0----------------------->

![](img/bf10264d38ec2d8074c31f1abd230fe7.png)

*轻松在房间间移动桌子*

在版本`2.4.0-alpha01`中引入的自动迁移的帮助下，使用 Room 实现数据库迁移变得更加容易。到目前为止，每当您的数据库模式发生变化时，您都必须实现一个`Migration`类，并告诉 Room 到底发生了什么变化。在大多数情况下，这涉及到编写和执行复杂的 SQL 查询。

现在有了自动迁移功能，您可以指定要从哪个版本迁移到哪个版本。Room 可以自动生成简单情况下的迁移，如添加、删除列或新表。对于更模糊的场景，Room 将需要一些帮助。您将能够提供具体的规范—比如重命名或删除的列或表—基于此，Room 将为您生成并运行迁移。让我们来看看一些例子，看看这是什么样子！

# 将自动设备置于自动迁移模式

假设我们正在向一个表中添加一个新列，从数据库的版本 1 升级到版本 2。我们必须通过增加版本号并添加从版本 1 到版本 2 的自动迁移来更新@Database 注释:

每当您的数据库版本再次更改时，只需更新自动迁移列表，添加新的版本:

对于在`@Database`中声明的实体，模式更改(如添加新的列或表、更新主键、外键或索引或更改列的默认值等)由 Room 自动检测，不需要任何其他输入。

⚠️Note:在引擎盖下，房间自动迁移依赖于生成的数据库模式，所以确保在使用`autoMigrations`时`@Database`中的`exportSchema`选项是`true`。否则导致错误:`Cannot create auto-migrations when export schema is OFF`。

# 当自动迁移需要一些帮助时

房间自动迁移不能检测数据库上执行的所有可能的更改，所以有时他们需要一些帮助。一个常见的例子是，Room 不能检测表或列是否被重命名或删除。当这种情况发生时，Room 将抛出一个编译时错误，要求您实现 AutoMigrationSpec。这个类允许你指定你所做的改变的类型。实现 AutoMigrationSpec，并用以下一项或多项对其进行注释:

*   `@DeleteTable(tableName)`
*   `@RenameTable(fromTableName, toTableName)`
*   `@DeleteColumn(tableName, columnName)`
*   `@RenameColumn(tableName, fromColumnName, toColumnName)`

例如，假设我们将表`Doggos`重命名为`GoodDoggos`。Room 无法检测这是否是一个全新的表并且我们删除了`Doggos`表，或者该表被重命名并且所有的值都需要保留。

# 迁移与自动迁移

## 何时使用迁移

为了[手动处理迁移](https://developer.android.com/training/data-storage/room/migrating-db-versions)，从版本 1.0 开始，Room 提供了`[Migration](https://developer.android.com/reference/kotlin/androidx/room/migration/Migration)`类。每当您要进行复杂的数据库模式更改时，就必须使用这个类。例如，假设我们决定将一个表拆分成两个不同的表。Room 无法检测这种拆分是如何执行的，也无法自动检测要移动哪些数据。所以这就是你必须实现一个`Migration`类并通过`addMigrations()`方法将其添加到`databaseBuilder()`的时候:

# 结合迁移和自动迁移

Room 允许将迁移与自动迁移结合起来。例如，可以使用`Migration`从版本 1 迁移到版本 2，使用自动迁移等从版本 2 迁移到版本 3。如果您为同一个版本定义了一个`Migration`和一个自动迁移，那么`Migration`将会运行。

在幕后，自动迁移构造了一个`Migration`类，所以在这里详细描述的相同迁移逻辑仍然适用。TL；DR:当第一次访问数据库时，Room 检查当前数据库版本是否与`@Database`中的版本不同。如果是，则 Room 寻找从一个到另一个的迁移路径。然后，迁移会连续运行。

# 测试自动迁移

为了测试自动迁移，您可以使用`MigrationTestHelper`测试规则并调用`helper.runMigrationsAndValidate()`，就像使用迁移类一样。在我们的[文档](https://developer.android.com/training/data-storage/room/migrating-db-versions#single-migration-test)中阅读更多关于测试迁移的信息。

# 结论

自动迁移特性允许您通过使用`@Database`中的`autoMigration`参数来轻松处理数据库模式变更。虽然许多基本情况可以由 Room 处理，但是对于表/列的删除或重命名，您必须实现一个`AutoMigrationSpec`。对于所有其他情况，继续使用`Migrations`。

这个功能目前在 **alpha** 中。通过对我们的[问题跟踪](https://issuetracker.google.com/issues/new?component=413107&template=1096568)提供反馈，帮助我们做得更好。