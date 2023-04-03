# 房间的 7 个小贴士

> 原文：<https://medium.com/androiddevelopers/7-pro-tips-for-room-fbadea4bfbd1?source=collection_archive---------0----------------------->

![](img/0d56fe8d240d547db97de65e47033b54.png)

[Source](https://unsplash.com/photos/PaFUKopuirM)

Room 是 SQLite 之上的一个抽象层，它使持久化数据变得更容易、更好。如果你是新来的，看看这本入门书:

[](/google-developers/7-steps-to-room-27a5fe5f99b2) [## 7 步到房间

### 如何将您的应用程序迁移到 Room 的分步指南

medium.com](/google-developers/7-steps-to-room-27a5fe5f99b2) 

在这篇文章中，我想分享一些关于充分利用房间的专业建议:

*   [通过](#4785) `[RoomDatabase#Callback](#4785)`预先填充您的数据库
*   [使用刀的继承能力](#dd40)
*   [通过](#026b) `[@Transaction](#026b)`用最少的样板代码在交易中执行查询
*   [只读您需要的内容](#829a)
*   [在具有外键的实体之间实施约束](#3e94)
*   [通过](#4f14) `[@Relation](#4f14)`简化一对多查询
*   [避免可观察查询的误报通知](#5e38)

# 1.预填充数据库

您是否需要在创建数据库后或打开数据库时向数据库中添加默认数据？使用`[RoomDatabase#Callback](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.Callback.html)`！构建房间数据库时调用`[addCallback](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.Builder.html#addCallback(android.arch.persistence.room.RoomDatabase.Callback))`方法，并覆盖`[onCreate](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.Callback.html#onCreate(android.arch.persistence.db.SupportSQLiteDatabase))`或`[onOpen](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.Callback.html#onOpen(android.arch.persistence.db.SupportSQLiteDatabase))`。

在创建表之后，第一次创建数据库时，将调用`onCreate`。`onOpen`在数据库打开时被调用。因为 DAO 只能在这些方法返回后才能被访问，所以我们创建了一个新的线程，在这个线程中我们获取对数据库的引用，获取 DAO，然后插入数据。

```
Room.databaseBuilder(context.*applicationContext*,
        DataDatabase::class.java, "Sample.db")
        // prepopulate the database after onCreate was called
        .addCallback(object : **Callback**() {
            override fun **onCreate**(db: SupportSQLiteDatabase) {
                super.onCreate(db)
                // moving to a new thread
                *ioThread* {
                    getInstance(context).dataDao()
                                        .insert(PREPOPULATE_DATA)
                }}
        })
        .build()
```

查看完整示例[此处](https://gist.github.com/florina-muntenescu/697e543652b03d3d2a06703f5d6b44b5)。

**注意**:当使用`ioThread`方法时，如果你的应用在第一次启动时崩溃，在数据库创建和插入之间，数据将永远不会被插入。

# 2.使用 DAO 的继承功能

您的数据库中有多个表，并且发现自己复制了相同的`Insert`、`Update`和`Delete`方法吗？Dao 支持继承，所以创建一个 `BaseDao<T>` 类，并在那里定义你的泛型`@Insert`、`@Update`和`@Delete`方法。让每个 DAO 扩展`BaseDao`并添加特定于它们的方法。

```
interface BaseDao<T> {
@Insert
    fun insert(vararg obj: T)
}@Dao
abstract class DataDao : BaseDao<Data>() {
@Query("SELECT * FROM Data")
    abstract fun getData(): List<Data>
}
```

点击查看更多详情[。](https://gist.github.com/florina-muntenescu/1c78858f286d196d545c038a71a3e864)

Dao 必须是接口或抽象类，因为 Room 在编译时生成它们的实现，包括来自`BaseDao`的方法。

# 3.用最少的样板代码在事务中执行查询

用`[@Transaction](https://developer.android.com/reference/android/arch/persistence/room/Transaction.html)`注释方法可以确保在该方法中执行的所有数据库操作都将在一个事务中运行。当方法体中引发异常时，事务将失败。

```
@Dao
abstract class UserDao {

    @Transaction
    open fun updateData(users: List<User>) {
        deleteAllUsers()
        insertAll(users)
    } @Insert
    abstract fun insertAll(users: List<User>) @Query("DELETE FROM Users")
    abstract fun deleteAllUsers()
}
```

在下列情况下，您可能希望对具有 select 语句的`@Query`方法使用`@Transaction`注释:

*   当查询结果相当大时。通过在一个事务中查询数据库，您可以确保如果查询结果不适合单个游标窗口，它不会由于数据库在[游标窗口交换](/google-developers/large-database-queries-on-android-cb043ae626e8)之间的变化而被破坏。
*   当查询结果是带有`@Relation`字段的 POJO 时。这些字段是单独的查询，因此在单个事务中运行它们将保证查询之间的结果一致。

具有多个参数的`@Delete`、`@Update`和`@Insert`方法在一个事务内部自动运行。

# 4.只读你需要的东西

当您查询数据库时，是否使用了查询中返回的所有字段？注意你的应用程序使用的**内存**的数量，只加载你最终会用到的字段的子集。这也将通过降低 IO 成本来提高查询的速度。Room 将为您进行列和对象之间的映射。

考虑一下这个复杂的`User`物体:

```
@Entity(tableName = "users")
data class User(@PrimaryKey
                val id: String,
                val userName: String,
                val firstName: String, 
                val lastName: String,
                val email: String,
                val dateOfBirth: Date, 
                val registrationDate: Date)
```

在某些屏幕上，我们不需要显示所有这些信息。因此，我们可以创建一个只保存所需数据的`UserMinimal`对象。

```
data class UserMinimal(val userId: String,
                       val firstName: String, 
                       val lastName: String)
```

在 DAO 类中，我们定义查询并从 users 表中选择正确的列。

```
@Dao
interface UserDao {
    @Query(“SELECT userId, firstName, lastName FROM Users)
    fun getUsersMinimal(): List<UserMinimal>
}
```

# 5.在具有外键的实体之间实施约束

尽管 Room[不直接支持关系](https://developer.android.com/topic/libraries/architecture/room.html#no-object-references)，但它允许您定义实体之间的外键约束。

房间有`[@ForeignKey](https://developer.android.com/reference/android/arch/persistence/room/ForeignKey.html)`注释，是`[@Entity](https://developer.android.com/reference/android/arch/persistence/room/Entity.html)`注释的一部分，允许使用 [SQLite 外键](https://sqlite.org/foreignkeys.html)特性。它跨表实施约束，以确保在修改数据库时关系有效。在实体上，定义要引用的父实体、父实体中的列和当前实体中的列。

考虑一个`User`和一个`Pet`类。`Pet`有一个所有者，它是作为外键引用的用户 id。

```
@Entity(tableName = "pets",
        foreignKeys = *arrayOf*(
            ForeignKey(entity = User::class,
                       parentColumns = *arrayOf*("userId"),
                       childColumns = *arrayOf*("owner"))))
data class Pet(@PrimaryKey val petId: String,
              val name: String,
              val owner: String)
```

或者，您可以定义在数据库中删除或更新父实体时要采取的操作。您可以从以下选项中选择一个:`NO_ACTION`、`RESTRICT`、`SET_NULL`、`SET_DEFAULT`或`CASCADE`，其行为与 [SQLite](https://sqlite.org/foreignkeys.html#fk_actions) 中的行为相同。

**注**:在 Room 中，`SET_DEFAULT`作为`SET_NULL`使用，因为 Room 还不允许设置列的默认值。

# 6.通过`@Relation`简化一对多查询

在前面的`User` - `Pet`例子中，我们可以说我们有一个**一对多关系**:一个用户可以有多个宠物。假设我们想获得一个用户及其宠物的列表:`List<UserAndAllPets>`。

```
data class UserAndAllPets (val user: User,
                           val pets: List<Pet> = ArrayList())
```

要手动完成这项工作，我们需要实现两个查询:一个获取所有用户的列表，另一个获取基于用户 id 的宠物列表。

```
@Query(“SELECT * FROM Users”)
public List<User> getUsers();@Query(“SELECT * FROM Pets where owner = :userId”)
public List<Pet> getPetsForUser(String userId);
```

然后，我们将遍历用户列表并查询 Pets 表。

为了更简单，Room 的`[@Relation](https://developer.android.com/reference/android/arch/persistence/room/Relation.html)`注释自动获取相关实体。`@Relation`只能应用于`List`或`Set`的对象。`UserAndAllPets`类必须更新:

```
class UserAndAllPets { @Embedded
   var user: User? = null @Relation(parentColumn = “userId”,
             entityColumn = “owner”)
   var pets: List<Pet> = ArrayList()}
```

在 DAO 中，我们定义了一个查询，Room 将查询`Users`和`Pets`表，并处理对象映射。

```
@Transaction
@Query(“SELECT * FROM Users”)
List<UserAndAllPets> getUsers();
```

# 7.避免可观察查询的误报通知

假设您想在一个可观察的查询中基于用户 id 获得一个用户:

```
@Query(“SELECT * FROM Users WHERE userId = :id)
fun getUserById(id: String): LiveData<User>// or@Query(“SELECT * FROM Users WHERE userId = :id)
fun getUserById(id: String): Flowable<User>
```

每当用户更新时，您将获得一个新的`User`对象的发射。但是当`Users`表上发生与您感兴趣的`User`无关的其他更改(删除、更新或插入)时，您也将获得相同的对象，从而导致误报通知。更重要的是，如果您的查询涉及多个表，那么只要其中任何一个表发生了变化，您就会得到一个新的发射。

以下是幕后发生的事情:

1.  SQLite 支持每当表中发生`DELETE`、`UPDATE`或`INSERT`时触发的[触发器](https://sqlite.org/lang_createtrigger.html)。
2.  Room 创建了一个`[InvalidationTracker](https://developer.android.com/reference/android/arch/persistence/room/InvalidationTracker.html)`，它使用`Observers`来跟踪观察到的表中的任何变化。
3.  `LiveData`和`Flowable`查询都依赖于`[InvalidationTracker.Observer#onInvalidated](https://developer.android.com/reference/android/arch/persistence/room/InvalidationTracker.Observer.html#onInvalidated(java.util.Set<java.lang.String>))`通知。当接收到此消息时，它会触发重新查询。

房间只知道桌子被修改过，但**不知道**和**为什么**变了。因此，在重新查询之后，查询的结果由`LiveData`或`Flowable`发出。由于 Room 在内存中不保存任何数据，也不能假设对象有`equals()`，所以它无法判断这是否是相同的数据。

你需要确保你的 DAO **过滤发射**并且只对不同的物体做出反应。

如果使用`Flowables`实现可观察查询，则使用`[Flowable#distinctUntilChanged](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Flowable.html#distinctUntilChanged--)`。

```
@Daoabstract class UserDao : BaseDao<User>() {/**
* Get a user by id.
* @return the user from the table with a specific id.
*/@Query(“SELECT * FROM Users WHERE userid = :id”)
protected abstract fun getUserById(id: String): Flowable<User>fun getDistinctUserById(id: String): 
   Flowable<User> = getUserById(id)
                          .distinctUntilChanged()}
```

如果您的查询返回一个`LiveData`，您可以使用一个`MediatorLiveData`，它只允许来自一个源的不同对象发射。

```
fun <T> LiveData<T>.getDistinct(): LiveData<T> {
    val distinctLiveData = MediatorLiveData<T>()
    distinctLiveData.addSource(this, object : Observer<T> {
        private var initialized = false
        private var lastObj: T? = null override fun onChanged(obj: T?) {
            if (!initialized) {
                initialized = true
                lastObj = obj
                distinctLiveData.postValue(lastObj)
            } else if ((obj == null && lastObj != null) 
                       || obj != lastObj) {
                lastObj = obj
                distinctLiveData.postValue(lastObj)
            }
        }
    }) return distinctLiveData
}
```

在您的 DAOs 中，使用返回 distinct `LiveData` `public`的方法和查询数据库`protected`的方法。

```
@Dao
abstract class UserDao : BaseDao<User>() {@Query(“SELECT * FROM Users WHERE userid = :id”)
   protected abstract fun getUserById(id: String): LiveData<User>fun getDistinctUserById(id: String): 
         LiveData<User> = getUserById(id).getDistinct()}
```

点击查看更多代码[。](https://gist.github.com/florina-muntenescu/fea9431d0151ce0afd2f5a0b8834a6c7)

**注意**:如果你要返回一个要显示的列表，考虑使用[分页库](https://developer.android.com/topic/libraries/architecture/paging.html)并返回一个 [LivePagedListBuilder](https://developer.android.com/reference/android/arch/paging/LivePagedListProvider.html) ，因为这个库将帮助自动计算列表项之间的差异并更新你的 UI。

新来的[室](https://developer.android.com/topic/libraries/architecture/room.html)？查看我们以前的文章:

[](/google-developers/7-steps-to-room-27a5fe5f99b2) [## 7 步到房间

### 如何将您的应用程序迁移到 Room 的分步指南

medium.com](/google-developers/7-steps-to-room-27a5fe5f99b2) [](/google-developers/room-rxjava-acb0cd4f3757) [## 房间🔗RxJava

### 使用 RxJava 在房间中进行查询

medium.com](/google-developers/room-rxjava-acb0cd4f3757) [](/google-developers/understanding-migrations-with-room-f01e04b07929) [## 了解迁移与空间

### 用 SQLite API 执行数据库迁移总是让我感觉像是在拆除一颗炸弹——好像我就是一颗…

medium.com](/google-developers/understanding-migrations-with-room-f01e04b07929)