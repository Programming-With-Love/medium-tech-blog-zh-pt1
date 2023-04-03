# 测试室迁移

> 原文：<https://medium.com/androiddevelopers/testing-room-migrations-be93cdb0d975?source=collection_archive---------4----------------------->

![](img/ea23a0c4fa4a9a78b9db0112e65f963b.png)

Is everything where it’s supposed to be? Test migrations. (Photo by [Dmitri Popov](http://unsplash.com/photos/dzCBKa8rIAM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText))

在之前的一篇文章中，我解释了带房间的数据库迁移是如何工作的。我们看到，不正确的迁移实施会导致您的应用崩溃或用户数据丢失。

[](/google-developers/understanding-migrations-with-room-f01e04b07929) [## 了解迁移与空间

### 用 SQLite API 执行数据库迁移总是让我感觉像是在拆除一颗炸弹——好像我就是一颗…

medium.com](/google-developers/understanding-migrations-with-room-f01e04b07929) 

最重要的是，您在`Migration.migrate`方法中执行的 SQL 语句在编译时没有被检查，这为更多的问题打开了大门。了解了所有这些，测试迁移就成了一项基本任务。Room 提供了`MigrationTestHelper`测试规则，允许您:

*   创建给定版本的数据库
*   在数据库上运行一组给定的迁移
*   验证数据库模式

*注意，Room 不会验证数据库中的数据。这是你需要自己去实现的事情。*

下面是测试房间迁移需要了解的内容。

# 在后台

为了测试迁移，Room 需要知道关于您当前数据库版本的一些事情:版本号、实体、身份散列以及创建和更新`room_master_table`的查询。所有这些都是由 Room 在编译时自动生成的，并存储在一个 schema JSON 文件中。

在您的`build.gradle`文件中，您指定一个文件夹来放置这些生成的模式 JSON 文件。当您更新模式时，您将得到几个 JSON 文件，每个版本一个。确保将每个生成的文件都提交给源代码管理。下次您再次增加您的版本号时，Room 将能够使用 JSON 文件进行测试。

# 先决条件

为了能够生成 JSON 文件，用下面的代码更新您的`build.gradle`文件:

1.  定义模式位置

```
defaultConfig {
  javaCompileOptions {
            annotationProcessorOptions {
                arguments += ["room.schemaLocation": 
                                "$projectDir/schemas".toString()]
            }
        }
}
```

2.将模式位置添加到源集中

```
android {

    sourceSets {
        androidTest.assets.srcDirs +=  
                           files("$projectDir/schemas".toString())
    }
```

3.将 room testing 库添加到依赖项列表中

```
dependencies {androidTestImplementation    
                “android.arch.persistence.room:testing:1.0.0-alpha5”}
```

# 迁移测试规则

创建数据库、模式、打开和关闭数据库、运行迁移——几乎每个测试都需要编写大量样板代码。为了避免写所有这些，在您的迁移测试类中使用`MigrationTestHelper`测试规则。

```
@Rule
public MigrationTestHelper testHelper =
        new MigrationTestHelper(
                InstrumentationRegistry.*getInstrumentation*(),
                *<your_database_class>*.class.getCanonicalName(),
                new FrameworkSQLiteOpenHelperFactory());
```

`MigrationTestHelper`非常依赖生成的 JSON 文件，既用于创建数据库，也用于验证迁移。

您能够**在特定版本**中创建您的数据库:

```
// Create the database with version 2
SupportSQLiteDatabase db = 
                         testHelper.createDatabase(TEST_DB_NAME, 2);
```

您可以**运行一组迁移，并自动验证**模式是否被正确更新:

```
db = testHelper.**runMigrationsAndValidate**(TEST_DB_NAME, 4, validateDroppedTables, MIGRATION_1_2, MIGRATION_2_3, MIGRATION_3_4);
```

# 实施测试

测试策略很简单:

1.  在特定版本中打开数据库
2.  插入一些数据
3.  运行迁移并验证模式
4.  检查数据库中的数据是否正确。

例如，数据库的版本 3 增加了一个新列:`date`。因此，当测试从版本 2 到版本 3 的迁移时，我们检查版本 2 中插入的数据的有效性，以及新列的默认值。下面是我们的`AndroidJUnitTest`的样子:

```
@Test
public void migrationFrom2To3_containsCorrectData() throws 
                                                       IOException {
    // Create the database in version 2
    SupportSQLiteDatabase db = 
                         **testHelper.createDatabase**(*TEST_DB_NAME*, 2);
    // Insert some data
    insertUser(*USER*.getId(), *USER*.getUserName(), db);
    //Prepare for the next version
    db.close();

    // Re-open the database with version 3 and provide MIGRATION_1_2  
    // and MIGRATION_2_3 as the migration process.
    **testHelper.runMigrationsAndValidate**(*TEST_DB_NAME*, 3,   
              validateDroppedTables, *MIGRATION_1_2*, *MIGRATION_2_3*);

    // MigrationTestHelper automatically verifies the schema  
    //changes, but not the data validity
    // Validate that the data was migrated properly.
    User dbUser = getMigratedRoomDatabase().userDao().getUser();
    *assertEquals*(dbUser.getId(), *USER*.getId());
    *assertEquals*(dbUser.getUserName(), *USER*.getUserName());
    // The date was missing in version 2, so it should be null in 
    //version 3
    *assertEquals*(dbUser.getDate(), null);
}
```

# 测试从 SQLiteDatabase 实现到 Room 的迁移

我之前谈到了从标准的`SQLiteDatabase`实现到 Room 需要采取的步骤，但是我没有详细介绍如何测试迁移实现。

[](/google-developers/7-steps-to-room-27a5fe5f99b2) [## 7 步到房间

### 如何将您的应用程序迁移到 Room 的分步指南

medium.com](/google-developers/7-steps-to-room-27a5fe5f99b2) 

由于最初的数据库版本不是使用 Room 实现的，我们没有相应的 JSON 文件，因此我们不能使用`MigrationTestHelper`来创建数据库。

我们需要做的是:

1.  扩展`SQLiteOpenHelper`类，在`onCreate`中，执行创建数据库表的 SQL 查询。
2.  在测试的`@Before`方法中，创建数据库。
3.  在测试的`@After`方法中，清空数据库。
4.  使用您的`SQLiteOpenHelper`实现来插入测试所需的数据，检查从您的`SQLiteDatabase`版本到使用 Room 的其他版本的迁移。
5.  使用`MigrationTestHelper`运行迁移并验证模式。
6.  检查数据库数据的有效性。

数据库版本 1 是使用`SQLiteDatabase` API 实现的，然后在版本 2 中我们迁移到了 Room，在版本 3 中我们添加了一个新列。检查从版本 1 到版本 3 的迁移的测试如下所示:

```
@Test
public void migrationFrom1To3_containsCorrectData() throws IOException {
    // Create the database with the initial version 1 schema and    
    //insert a user
    **SqliteDatabaseTestHelper**.*insertUser*(1, *USER*.getUserName(), sqliteTestDbHelper);

    // Re-open the database with version 3 and provide MIGRATION_1_2 
    // and MIGRATION_2_3 as the migration process.
    **testHelper.runMigrationsAndValidate**(*TEST_DB_NAME*, 3, true,
            *MIGRATION_1_2*, *MIGRATION_2_3*);

    // Get the latest, migrated, version of the database
    // Check that the correct data is in the database
    User dbUser = getMigratedRoomDatabase().userDao().getUser();
    *assertEquals*(dbUser.getId(), 1);
    *assertEquals*(dbUser.getUserName(), *USER*.getUserName());
    // The date was missing in version 2, so it should be null in   
    //version 3
    *assertEquals*(dbUser.getDate(), null);
}
```

# 给我看看代码

您可以在这个示例应用程序中查看[的实现。为了便于比较，每个数据库版本都以自己的方式实现:](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample)

1.  [**sqlite**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/sqlite/java/com/example/android/persistence/migrations) —使用`SQLiteOpenHelper`和传统的 sqlite 接口。
2.  [**房间**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/room/java/com/example/android/persistence/migrations) —用房间替换实施并提供到版本 2 的迁移
3.  [**room2**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/room2/java/com/example/android/persistence/migrations) —将数据库更新为新模式版本 3
4.  [**room3**](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample/app/src/room3/java/com/example/android/persistence/migrations) —将数据库更新到新的版本 4。提供从版本 2 到版本 3、版本 3 到版本 4 以及版本 1 到版本 4 的迁移路径。

# 结论

有了空间，实现和测试迁移就变得容易了。`MigrationTestHelper`测试规则允许您在任何版本下打开数据库，只需几行代码就可以运行迁移和验证模式。

您是否已经开始使用空间并实施迁移？如果是这样，请在下面的评论中告诉我们进展如何。