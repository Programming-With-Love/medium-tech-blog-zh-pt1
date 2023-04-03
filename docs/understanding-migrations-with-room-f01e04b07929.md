# 了解迁移与空间

> 原文：<https://medium.com/androiddevelopers/understanding-migrations-with-room-f01e04b07929?source=collection_archive---------0----------------------->

![](img/f235272d6d97d2ed5437e42ae37934a2.png)![](img/f235272d6d97d2ed5437e42ae37934a2.png)

Flip the switch and migrate ([Source](https://unsplash.com/photos/qAShc5SV83M))

用 SQLite API 执行数据库迁移总是让我觉得我在拆除一个炸弹——好像我差一步就让应用程序在我的用户手中爆炸了。如果您正在使用[房间](https://developer.android.com/topic/libraries/architecture/room.html)来处理您的数据库操作，迁移就像扳动开关一样简单。

有了 Room，如果你更改了数据库模式但没有升级版本，你的应用就会崩溃。如果您升级版本但不提供任何迁移，您的应用程序将会崩溃，或者数据库表被丢弃，您的用户将会丢失数据。不要拿你(应用程序)的生命冒险去猜测 w̶h̶i̶c̶h̶̶s̶w̶i̶t̶c̶h̶̶t̶o̶̶f̶l̶i̶p̶如何实现迁移。相反，要理解 Room 的内部工作方式，以便放心地迁移您的数据库。

# 幕后数据库迁移

## SQLite API 做什么

SQLite 数据库在数据库版本控制的帮助下处理模式变化。更准确地说，每次通过添加、删除或修改表来改变模式时，都必须增加数据库版本号并更新`SQLiteOpenHelper.onUpgrade`方法的实现。这就是你如何告诉 SQLite 从旧版本到新版本需要做什么。

这也是当您的应用程序开始使用数据库时触发的第一个调用。SQLite 将首先尝试处理版本升级，然后才打开数据库。

## 哪个房间有

Room 以`[Migration](https://developer.android.com/reference/android/arch/persistence/room/migration/Migration.html)`类的形式提供了一个抽象层来简化 SQLite 迁移。一个`Migration`类定义了从一个特定版本迁移到另一个版本时应该执行的动作。Room 使用自己的`SQLiteOpenHelper`实现，并且在`onUpgrade`方法中，将触发您定义的迁移。

以下是您第一次访问数据库时发生的情况:

1.  建立房间数据库
2.  `SQLiteOpenHelper.onUpgrade`方法被调用，房间触发迁移
3.  数据库被打开

如果您不提供迁移，但增加了数据库版本，您的应用程序可能会崩溃，或者您的数据可能会丢失，这取决于我们将在下面考虑的一些情况。

迁移内部的一个重要部分是由一个`identity hash String`扮演的，它被 Room 用来唯一地标识每个数据库版本。当前版本的身份哈希保存在数据库中由 Room 管理的配置表中。因此，如果您看到数据库中的`room_master_table`表，不要感到惊讶。

让我们举一个简单的例子，我们有一个`users`表，有两列:

*   一个 ID，`int`，也是主键
*   一个用户名，`String`

`users`表是版本为 1 的数据库的一部分，使用`SQLiteDatabase` API 实现。

让我们假设您的用户已经在使用这个版本，并且您想开始使用 Room。让我们看看 Room 如何处理一些场景。

# 将 SQLite API 代码迁移到房间

在[另一篇文章](/google-developers/7-steps-to-room-27a5fe5f99b2)中，我们讨论了如何将您的应用迁移到房间，让我们在此基础上更深入地了解数据迁移的细节。让我们假设`User` [实体类](https://developer.android.com/topic/libraries/architecture/room.html#entities)和`UserDao`[数据访问对象类](https://developer.android.com/topic/libraries/architecture/room.html#daos)被创建，并且只关注扩展`[RoomDatabase](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.html)`的`UsersDatabase`类。

```
@Database(entities = {User.class}, version = 1)
public abstract class UsersDatabase extends RoomDatabase
```

## 场景 1:保持数据库版本不变——应用崩溃

如果我们保持数据库版本不变并运行我们的应用程序，这就是 Room 在幕后做的事情。

步骤 1:尝试打开数据库

*   通过将当前版本的标识哈希与保存在 room_master_table 中的标识哈希进行比较，检查数据库的标识。但是，由于没有保存身份哈希，**应用程序会因`IllegalStateException` ❌而导致**崩溃

```
java.lang.IllegalStateException: Room cannot verify the data integrity. Looks like you’ve changed schema but forgot to update the version number. You can simply fix this by increasing the version number.
```

> 如果您修改了数据库模式但没有更新版本号，Room 将总是抛出`***IllegalStateException***`。

让我们听一听错误并增加数据库版本。

```
@Database(entities = {User.class}, **version = 2**)
public abstract class UsersDatabase extends RoomDatabase
```

## 场景 2:版本增加，但不提供迁移—应用崩溃

现在，当再次运行应用程序时，Room 正在做以下事情:

步骤 1:尝试从版本 1(安装在设备上)升级到版本 2

*   由于没有迁移，应用程序因`IllegalStateException`而崩溃。❌

```
java.lang.IllegalStateException: A migration from 1 to 2 is necessary. Please provide a Migration in the builder or call fallbackToDestructiveMigration in the builder in which case Room will re-create all of the tables.
```

> 如果不提供迁移，Room 将抛出一个`*IllegalStateException*`。

## 场景 3:版本增加，支持回退到破坏性迁移—数据库被清除

如果您不想提供迁移，并且您明确地**希望在升级版本时**清除您的数据库，请在数据库构建器中调用`fallbackToDestructiveMigration`:

```
*database* = Room.*databaseBuilder*(context.getApplicationContext(),
                        UsersDatabase.class, "Sample.db")
                .**fallbackToDestructiveMigration()**
                .build();
```

现在，当再次运行应用程序时，Room 正在做以下事情:

步骤 1:尝试从版本 1(安装在设备上)升级到版本 2

*   因为没有迁移，我们退回到破坏性迁移，所以**表被删除**并且`identity_hash`被插入。🤷

步骤 2:尝试打开数据库

*   当前版本的身份哈希与保存在`room_master_table`中的身份哈希相同。✅

所以现在，我们的应用程序不会崩溃，但我们会丢失所有数据。因此，请确保这是您特别希望处理迁移的方式。

## 场景 4:版本增加，提供迁移—保留数据

为了保留用户的数据，我们需要实现迁移。由于模式没有改变，我们只需要提供一个**空迁移实现**并告诉 Room 使用它。

```
@Database(entities = {User.class},version = 2)
public abstract class UsersDatabase extends RoomDatabase {…static final Migration *MIGRATION_1_2* = new Migration(1, 2) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
        // Since we didn't alter the table, there's nothing else to do here.
    }
};…database =  Room.*databaseBuilder*(context.getApplicationContext(),
        UsersDatabase.class, "Sample.db")
        **.addMigrations(*MIGRATION_1_2*)**
        .build();
```

运行应用程序时，Room 会执行以下操作:

步骤 1:尝试从版本 1(安装在设备上)升级到版本 2

*   触发了✅的空迁移
*   在`room_master_table` ✅中更新身份哈希

步骤 2:尝试打开数据库

*   当前版本的身份哈希与保存在`room_master_table`中的身份哈希相同。✅

所以现在，我们的应用程序打开，用户的数据被迁移！🎉

# 通过简单的模式更改进行迁移

让我们通过修改`User`类，将另一列`last_update`添加到我们的`users`表中。在`UsersDatabase`类中我们需要做如下的修改:

1.将版本增加到 3

```
@Database(entities = {User.class}, **version = 3**)
public abstract class UsersDatabase extends RoomDatabase
```

2.添加从版本 2 到版本 3 的迁移

```
static final Migration *MIGRATION_2_3* = new Migration(2, 3) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
        database.execSQL("ALTER TABLE users "
                + " ADD COLUMN last_update INTEGER");
    }
};
```

3.将迁移添加到房间数据库生成器:

```
database = Room.*databaseBuilder*(context.getApplicationContext(),
        UsersDatabase.class, "Sample.db")
 **.addMigrations(*MIGRATION_1_2*, *MIGRATION_2_3*)**        .build();
```

运行应用程序时，需要完成以下步骤:

步骤 1:尝试从版本 2(安装在设备上)升级到版本 3

*   触发迁移并更改表，保持用户数据✅
*   更新`room_master_table` ✅中的身份哈希

步骤 2:尝试打开数据库

*   当前版本的身份哈希与保存在`room_master_table`中的身份哈希相同。✅

# 具有复杂模式更改的迁移

SQLite 的`ALTER TABLE…`命令[相当有限](https://sqlite.org/lang_altertable.html)。例如，将用户的 id 从`int`更改为`String`需要几个步骤:

*   用新模式创建一个新的临时表，
*   将数据从`users`表复制到临时表，
*   放下`users`桌子
*   将临时表重命名为`users`

使用 Room，迁移实现如下所示:

```
static final Migration *MIGRATION_3_4* = new Migration(3, 4) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
        // Create the new table
        database.execSQL(
                "CREATE TABLE users_new (userid TEXT, username TEXT, last_update INTEGER, PRIMARY KEY(userid))");// Copy the data
        database.execSQL(
                "INSERT INTO users_new (userid, username, last_update) SELECT userid, username, last_update FROM users");// Remove the old table
        database.execSQL("DROP TABLE users");// Change the table name to the correct one
        database.execSQL("ALTER TABLE users_new RENAME TO users");
    }
};
```

# 多个数据库版本增量

如果您的用户有一个旧版本的应用程序，运行数据库版本 1，并希望升级到版本 4，该怎么办？到目前为止，我们已经定义了以下迁移:版本 1 到 2、版本 2 到 3、版本 3 到 4，所以 Room 将一个接一个地触发所有迁移。

Room 可以处理不止一个版本增量:我们可以定义一个从版本 1 到版本 4 的单步迁移，使得迁移过程更快。

```
static final Migration *MIGRATION_1_4* = new Migration(1, 4) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
        // Create the new table
        database.execSQL(
                "CREATE TABLE users_new (userid TEXT, username TEXT, last_update INTEGER, PRIMARY KEY(userid))");

        // Copy the data
        database.execSQL(
                "INSERT INTO users_new (userid, username, last_update) SELECT userid, username, last_update FROM users");// Remove the old table
        database.execSQL("DROP TABLE users");// Change the table name to the correct one
        database.execSQL("ALTER TABLE users_new RENAME TO users");
    }
};
```

接下来，我们将它添加到迁移列表中:

```
database = Room.*databaseBuilder*(context.getApplicationContext(),
        UsersDatabase.class, "Sample.db")
        .**addMigrations**(*MIGRATION_1_2*, *MIGRATION_2_3*, *MIGRATION_3_4*, ***MIGRATION_1_4***)
        .build();
```

> 注意，您在`*Migration.migrate*`实现中编写的查询在运行时不会被编译，这与您的 Dao 中的查询不同。确保您正在[为您的迁移](/google-developers/testing-room-migrations-be93cdb0d975)实现测试。

# 给我看看代码

您可以在这个示例应用程序中查看[的实现。为了便于比较，每个数据库版本都以自己的方式实现:](https://github.com/googlesamples/android-architecture-components)

1.  [**sqlite**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/sqlite/java/com/example/android/persistence/migrations) —使用 sqliteOpenHelper 和传统的 SQLite 接口。
2.  [**房间**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/room/java/com/example/android/persistence/migrations) —用房间替换实现，并提供到版本 2 的迁移
3.  [**room2**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/room2/java/com/example/android/persistence/migrations) —将数据库更新到新的模式，版本 3
4.  [**room3**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/room3/java/com/example/android/persistence/migrations) —将 DB 更新到新的版本 4。提供从版本 2 到版本 3、版本 3 到版本 4 以及版本 1 到版本 4 的迁移路径。

# 结论

你的模式改变了吗？只需增加数据库版本并编写新的`[Migration](https://developer.android.com/reference/android/arch/persistence/room/migration/Migration.html)`实现。你将确保你的应用不会崩溃，你的用户数据不会丢失。就像扳动开关一样简单！

但是，你怎么知道你是否按对了开关呢？您如何测试您是否正确地从您的`SQLiteDatabase`实现迁移到了 Room，您是否在不同的数据库版本之间实现了正确的迁移，或者您的数据库是否确实在特定版本中正常启动？我们详细讨论了[测试迁移](https://developer.android.com/topic/libraries/architecture/room.html#db-migration-testing)，这里涵盖了几种不同的场景:

[](/google-developers/testing-room-migrations-be93cdb0d975) [## 测试室迁移

### 在之前的一篇文章中，我解释了带房间的数据库迁移是如何工作的。我们看到一个不正确的…

medium.com](/google-developers/testing-room-migrations-be93cdb0d975)