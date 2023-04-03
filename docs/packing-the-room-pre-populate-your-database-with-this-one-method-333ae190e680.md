# 收拾房间:用这种方法预先填充你的数据库

> 原文：<https://medium.com/androiddevelopers/packing-the-room-pre-populate-your-database-with-this-one-method-333ae190e680?source=collection_archive---------0----------------------->

![](img/04f4f85f42a4ee4e632d17907429b3c0.png)

Illustration by [Virginia Poltrack](http://twitter.com/vpoltrack)

假设您想要用打包在 APK 中或从服务器下载的数据预填充数据库。无论您是想使用 SQLite 还是 Room 来完成这项工作，都有几件事情需要处理:打开数据库、验证模式、锁定数据库文件并处理线程同步、复制所有内容并关闭数据库。

从 Room 2.2(目前在 alpha 中)开始，您可以通过调用一个方法来预填充您的数据库:`[RoomDatabase.Builder](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.Builder)#createFromAsset()`或`createFromFile()`，这取决于您预打包的数据库的位置。请继续阅读，了解如何使用这些方法、使用预打包数据库的一些技巧以及涉及迁移时会发生什么。

# `createFromAsset()`

如果你的预打包数据库是你的应用程序的`assets/`文件夹的一部分，使用`createFromAsset()`传递资产的路径作为参数。例如，如果您的数据库位于:`assets/database/myapp.db`，您将通过`“database/myapp.db”`:

```
Room.databaseBuilder(appContext, TestDatabase.class, “Sample.db”)
    .createFromAsset(“database/myapp.db”)
    .build()
```

# createFromFile()

如果您的数据库不在`assets/`文件夹中，例如您从服务器下载并保存到磁盘，使用`createFromFile()`并传递`File`。

```
Room.databaseBuilder(appContext, TestDatabase.class, “Sample.db”)
    .createFromFile(File(“mypath”))
    .build()
```

> 🚫在内存中，数据库不支持通过`createFromAsset`或`createFromFile`预填充数据库，如果你尝试这样做的话`RoomDatabase.build()`方法将抛出一个`IllegalArgumentException`。

# 文件权限

Room 不会打开您提供的数据库，而是复制它。因此，如果您使用的是`File`，请确保您有读取权限，以便 Room 可以复制它。

# 数据库验证

当从一个版本迁移到另一个版本时，Room 确保数据库的有效性，因此当从资产或文件创建数据库时，它应用相同的有效性检查。就像这样，它确保了您正在复制数据的数据库的模式匹配。

> 💡Room 允许通过`@Database`注释中的`[exportSchema](https://developer.android.com/reference/android/arch/persistence/room/Database#exportschema)`参数导出数据库模式。因此，当您创建预打包的数据库时，请使用其中定义的模式。

# 数据库迁移

一般来说，当数据库模式改变时，在`@RoomDatabase`注释中增加数据库版本，或者实现迁移或者[启用破坏性迁移](https://developer.android.com/training/data-storage/room/migrating-db-versions#handle-missing-migrations)。Room 会查看设备上已安装的版本和应用程序中定义的最新版本。

例如，假设设备上的数据库版本为 2，而您的应用程序的数据库版本(在`@RoomDatabase`中定义)为 4。下面我们来看看当预打包的数据库也加入到组合中时，在不同的场景中会发生什么:

**1。破坏性迁移**已启用，并且**预打包数据库**的**版本比您的应用程序中定义的**版本旧，则数据库被丢弃，数据不会被复制。

示例:如果预打包的数据库版本为 3，并且启用了破坏性迁移，则该数据库将被删除，并且不会复制数据。

**2。破坏性迁移**已启用，并且**预打包数据库**的**版本**与您的应用程序中定义的版本相同，然后删除数据库，并从预打包数据库中复制数据。

示例:如果预打包的数据库是版本 4，如应用程序的数据库版本，并且启用了破坏性迁移，则该数据库将被删除，数据将从预打包的数据库中复制。

**3。实施了迁移**，并且**预打包数据库的版本**比您的`@RoomDatabase`注释中的版本旧，那么 Room 将复制数据并运行迁移。

示例:如果预打包的数据库是版本 3，并且您实施了以下迁移:从版本 2 到版本 3 和从版本 3 到版本 4，那么将运行从版本 2 到版本 3 的迁移，将复制数据库，然后运行从版本 3 到版本 4 的迁移。

> 💡作为一个最佳实践，确保预打包的数据库是与在`@RoomDatabase`注释中声明的最新版本相同的版本。这样，您可以避免处理迁移案例。
> 
> 📖在这篇博文的[中阅读更多关于室内迁移的信息，在这里](/androiddevelopers/testing-room-migrations-be93cdb0d975)阅读更多关于测试迁移的信息[。](/androiddevelopers/understanding-migrations-with-room-f01e04b07929)

现在，您可以通过应用程序中的数据或文件轻松填充房间数据库。Room 保护您数据的完整性，并在必要时帮助您执行迁移。请在下面的评论中告诉我们您如何在应用中使用预打包的数据库！

感谢[丹尼尔·圣地亚哥](https://medium.com/u/78430ce91f3a?source=post_page-----333ae190e680--------------------------------)、[尼克·布彻](https://medium.com/u/22c02a30ae04?source=post_page-----333ae190e680--------------------------------)和[何塞·阿尔塞雷卡](https://medium.com/u/e0a4c9469bb5?source=post_page-----333ae190e680--------------------------------)！💚