# 7 步到房间

> 原文：<https://medium.com/androiddevelopers/7-steps-to-room-27a5fe5f99b2?source=collection_archive---------2----------------------->

![](img/7e1c4c0c79789eb9ca3210aa5aee7fae.png)

Take the 7 steps to Room ([Source](https://unsplash.com/photos/bt-Sc22W-BE))

*如何将你的应用迁移到房间的逐步指南*

[Room](https://developer.android.com/topic/libraries/architecture/room.html) 是一个持久性库，是 Android [架构组件](https://developer.android.com/topic/libraries/architecture/index.html)的一部分。这使得在应用程序中使用 SQLiteDatabase 对象更加容易，减少了样板代码的数量，并在编译时验证 SQL 查询。

您是否已经有了一个使用 SQLite 进行数据持久化的 Android 项目？如果有，可以把它迁移到房间！让我们来看看如何通过 7 个简单的步骤，利用一个预先存在的项目并重构它来使用空间。

> **TL；DR:更新你的 gradle 依赖项，创建你的实体、DAO 和数据库，用 DAO 方法调用替换你的 SQLiteDatabase 调用，测试你创建或修改的所有东西，删除不用的类。就是这样！**

我们的[迁移示例应用程序](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample)显示了一个可编辑的用户名，存储在数据库中，作为用户对象的一部分。我们使用产品风格来展示数据层的不同实现:

1.  **sqlite** —使用 sqliteOpenHelper 和传统的 SQLite 接口。
2.  **房间** —用房间代替实施并提供迁移。

每种风格都使用相同的 UI 层，应用模型-视图-展示者设计模式并使用 UserRepository 类。

在 sqlite 版本中，您会看到在`UsersDbHelper`和`LocalUserDataSource`类中查询数据库的每个方法中都有大量重复的代码。在 ContentValues 的帮助下构建查询，并逐列读取由`Cursor`对象返回的数据。所有这些代码很容易引入微妙的错误，比如忘记在查询中添加一列，或者从数据库数据中错误地构造模型对象。

让我们看看 Room 如何改进我们的代码。最初，我们只是从`sqlite`版本中复制类，然后逐渐修改它们。

# 步骤 1-更新梯度相关性

Room 的依赖项可以通过 Google 的新 Maven 资源库获得，只需将其添加到主 build.gradle 文件的资源库列表中:

```
allprojects {
    repositories {
        google()
        jcenter()
    }
}
```

在同一文件中定义您的房间库版本。目前，它还处于 alpha 阶段，但是请关注我们的[开发者页面](https://developer.android.com/topic/libraries/architecture/release-notes.html)获取版本更新。

```
ext {
   ... 
    roomVersion = '1.0.0-alpha4'
}
```

在您的`app/build.gradle`文件中，添加 Room 的依赖项。

```
dependencies{
 …implementation        
   “android.arch.persistence.room:runtime:$rootProject.roomVersion”annotationProcessor 
   “android.arch.persistence.room:compiler:$rootProject.roomVersion”androidTestImplementation 
   “android.arch.persistence.room:testing:$rootProject.roomVersion”}
```

为了迁移到 Room，我们需要增加数据库版本，为了保存用户数据，我们需要实现一个`[Migration](https://developer.android.com/reference/android/arch/persistence/room/migration/Migration.html)`类。为了[测试迁移](https://developer.android.com/topic/libraries/architecture/room.html#db-migration)，我们需要导出模式。为此，将以下内容添加到您的`app/build.gradle`文件中:

```
android {
    defaultConfig {
        ...
       // used by Room, to test migrations
        javaCompileOptions {
            annotationProcessorOptions {
                arguments = ["room.schemaLocation": 
                                 "$projectDir/schemas".toString()]
            }
        }
    }

    // used by Room, to test migrations
    sourceSets {
        androidTest.assets.srcDirs += 
                           files("$projectDir/schemas".toString())
    }
...
```

# 步骤 2-将模型类更新为实体

Room 为每个标注了`[@Entity](https://developer.android.com/reference/android/arch/persistence/room/Entity.html)`的类创建一个表；类中的字段对应于表中的列。因此，实体类往往是不包含任何逻辑的小型模型类。我们的`User`类表示数据库中数据的模型。所以让我们更新它，告诉 Room 它应该基于这个类创建一个表:

*   用`@Entity`注释该类，并使用`tableName`属性设置表的名称。
*   通过向正确的字段添加`@PrimaryKey`注释来设置主键——在我们的例子中，这是用户的 ID。
*   使用`@ColumnInfo(name = “column_name”)`注释设置类字段的列名。如果您的字段已经有了正确的列名，请随意跳过这一步。
*   如果有多个合适的构造函数，添加`@Ignore`注释来告诉 Room 哪些应该使用，哪些不应该使用。

```
@Entity(tableName = "users")
public class User {

    @PrimaryKey
    @ColumnInfo(name = "userid")
    private String mId;

    @ColumnInfo(name = "username")
    private String mUserName;

    @ColumnInfo(name = "last_update")
    private Date mDate;

    @Ignore
    public User(String userName) {
        mId = UUID.*randomUUID*().toString();
        mUserName = userName;
        mDate = new Date(System.*currentTimeMillis*());
    }

    public User(String id, String userName, Date date) {
        this.mId = id;
        this.mUserName = userName;
        this.mDate = date;
    }
...
}
```

**注意:**为了实现无缝迁移，请密切注意初始实现中的表和列名，并确保在`@Entity`和`@ColumnInfo`注释中正确设置它们。

# 步骤 3 —创建数据访问对象(Dao)

Dao 负责定义访问数据库的方法。在我们项目的初始 SQLite 实现中，所有对数据库的查询都是在`LocalUserDataSource`文件中完成的，在这里我们使用的是`Cursor`对象。有了 Room，我们不需要所有与`Cursor`相关的代码，可以简单地使用`UserDao`类中的注释定义我们的查询。

例如，当查询所有用户的数据库时，Room 会做所有的“繁重”工作，我们只需要编写:

```
@Query(“SELECT * FROM Users”)
List<User> getUsers();
```

# 步骤 4 —创建数据库

到目前为止，我们已经定义了我们的`Users`表及其相应的查询，但是我们还没有创建将这些空间的其他部分集合在一起的数据库。为此，我们需要定义一个扩展`RoomDatabase`的抽象类。这个类用`@Database`进行了注释，列出了数据库中包含的实体，以及访问它们的 Dao。数据库版本必须从初始值增加 1，所以在我们的例子中，它将是 2。

```
@Database(entities = {User.class}, version = 2)
@TypeConverters(DateConverter.class)
public abstract class UsersDatabase extends RoomDatabase {

    private static UsersDatabase *INSTANCE*;

    public abstract UserDao userDao();
```

因为我们想要保存用户数据，所以我们需要实现一个`[Migration](https://developer.android.com/reference/android/arch/persistence/room/migration/Migration.html)`类，告诉 Room 从版本 1 迁移到版本 2 时应该做什么。在我们的例子中，因为数据库模式没有改变，所以我们将只提供一个空的实现:

```
static final Migration *MIGRATION_1_2* = new Migration(1, 2) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
// Since we didn't alter the table, there's nothing else to do here.
    }
};
```

在`UsersDatabase`类中创建数据库对象，定义数据库名称和迁移:

```
database = Room.*databaseBuilder*(context.getApplicationContext(),
        UsersDatabase.class, "Sample.db")
        .addMigrations(*MIGRATION_1_2*)
        .build();
```

要了解更多关于如何实现数据库迁移以及它们如何在幕后工作的信息，请查看这篇文章:

[](/google-developers/understanding-migrations-with-room-f01e04b07929) [## 了解迁移与空间

### 用 SQLite API 执行数据库迁移总是让我感觉像是在拆除一颗炸弹——好像我就是一颗…

medium.com](/google-developers/understanding-migrations-with-room-f01e04b07929) 

# 步骤 5 —更新存储库以使用空间

我们已经创建了数据库、`Users`表和查询，现在是时候使用它们了！在这一步中，我们将更新`LocalUserDataSource`类来使用`UserDao`方法。为此，我们将首先通过移除`Context`并添加`UserDao`来更新构造函数。当然，任何实例化`LocalUserDataSource`的类也需要更新。

其次，我们将通过调用`UserDao`方法来更新查询数据库的`LocalUserDataSource`方法。例如，获取所有用户的方法现在如下所示:

```
public List<User> getUsers() {
   return mUserDao.getUsers();
}
```

现在:运行时间！

Room 最好的特性之一是，如果您在主线程上执行数据库操作，您的应用程序将会崩溃，并显示以下异常消息:

```
java.lang.IllegalStateException: Cannot access database on the main thread since it may potentially lock the UI for a long period of time.
```

将 I/O 操作移出主线程的一个可靠方法是为每个数据库查询创建一个新的在单线程`Executor`上运行的`Runnable`。因为我们已经在`sqlite`风格中使用了这种方法，所以不需要任何改变。

# 步骤 6 —设备上测试

我们创建了新的类— `UserDao`和`UsersDatabase`，并修改了我们的`LocalUserDataSource`来使用房间数据库。现在，我们需要测试他们！

## **测试** `**UserDao**`

为了测试`UserDao`，我们需要创建一个`AndroidJUnit4`测试类。Room 的一个令人惊叹的特性是它能够创建一个内存数据库。这避免了在每个测试用例之后清理的需要。

```
@Before
public void initDb() throws Exception {
    mDatabase = Room.inMemoryDatabaseBuilder(
                           InstrumentationRegistry.getContext(),
                           UsersDatabase.class)
                    .build();
}
```

我们还需要确保在每次测试后关闭数据库连接。

```
@After
public void closeDb() throws Exception {
    mDatabase.close();
}
```

例如，为了测试一个`User`的插入，我们将插入用户，然后我们将检查我们确实可以从数据库中获得那个`User`。

```
@Test
public void insertAndGetUser() {
    // When inserting a new user in the data source
    mDatabase.userDao().insertUser(*USER*);

    //The user can be retrieved
    List<User> users = mDatabase.userDao().getUsers();
    *assertThat*(users.size(), *is*(1));
    User dbUser = users.get(0);
    *assertEquals*(dbUser.getId(), *USER*.getId());
    *assertEquals*(dbUser.getUserName(), *USER*.getUserName());
}
```

## **测试 LocalUserDataSource** 中 `**UserDao**` **的用法**

确保`LocalUserDataSource`仍然正常工作是很容易的，因为我们已经测试了这个类的行为。我们需要做的就是创建一个内存数据库，从中获取一个`UserDao`对象，并将其用作`LocalUserDataSource`构造函数的参数。

```
@Before
public void initDb() throws Exception {
    mDatabase = Room.inMemoryDatabaseBuilder(
                           InstrumentationRegistry.getContext(),
                           UsersDatabase.class)
                    .build();
    mDataSource = new LocalUserDataSource(mDatabase.userDao());
}
```

同样，我们需要确保在每次测试后都关闭数据库。

## **测试数据库迁移**

在这篇博文中，我们详细讨论了如何实现数据库迁移测试，并解释了`MigrationTestHelper`的工作原理:

[](/google-developers/testing-room-migrations-be93cdb0d975) [## 测试室迁移

### 在之前的一篇文章中，我解释了带房间的数据库迁移是如何工作的。我们看到一个不正确的…

medium.com](/google-developers/testing-room-migrations-be93cdb0d975) 

查看来自[迁移示例应用](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample)的代码，获取更广泛的示例。

# 第 7 步—清理

删除任何未使用的类或代码行，这些类或代码行现在被房间功能所取代。在我们的项目中，我们只需删除`UsersDbHelper`类，这是对`SQLiteOpenHelper`类的扩展。

**I** 如果您有一个更大、更复杂的数据库，并且您想要增量迁移到 Room，以下是方法:

[](/google-developers/incrementally-migrate-from-sqlite-to-room-66c2f655b377) [## 从 SQLite 到 Room 的增量迁移

### 将您复杂的数据库迁移到具有可管理 PRs 的房间。

medium.com](/google-developers/incrementally-migrate-from-sqlite-to-room-66c2f655b377) 

样板文件、易错代码的数量减少了，查询现在在编译时被检查，一切都是可测试的。通过 7 个简单的步骤，我们能够将现有应用迁移到 Room。点击查看示例应用[。请在下面的评论中告诉我们你的房间迁移进展如何。](https://github.com/googlesamples/android-architecture-components/tree/master/PersistenceMigrationsSample)