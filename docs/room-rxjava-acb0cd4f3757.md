# 房间🔗RxJava

> 原文：<https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757?source=collection_archive---------1----------------------->

![](img/5414fdb8d24f521c1c8447ba7ee2c326.png)

Room and RxJava — easily linked together ([Source](https://unsplash.com/photos/PDxYfXVlK2M))

使用 RxJava 在房间中进行查询

更少的样板代码，编译时检查的 SQL 查询，最重要的是，异步和可观察查询的能力——听起来怎么样？所有这些都可以通过 [Room](https://developer.android.com/topic/libraries/architecture/room.html) 、来自[架构组件](https://developer.android.com/topic/libraries/architecture/index.html)的持久性库来实现。异步查询返回 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata.html) 或 RxJava 的[可能是](http://reactivex.io/RxJava/javadoc/io/reactivex/Maybe.html)、 [Single](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html) 或[flowered](http://reactivex.io/RxJava/javadoc/io/reactivex/Flowable.html)。返回 LiveData 和 Flowable 的查询是可观察的查询。它们允许您在数据发生变化时获得自动更新，以确保您的 UI 反映数据库中的最新值。如果你已经在你的应用中使用了 RxJava 2，那么将 Room 与`[Maybe](http://reactivex.io/RxJava/javadoc/io/reactivex/Maybe.html)`、`[Single](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html)`和`[Flowable](http://reactivex.io/RxJava/javadoc/io/reactivex/Flowable.html)`一起使用将会轻而易举。

> 后期编辑:从' 2.0.0-beta01 '开始，Room 也支持`[Observable](http://reactivex.io/RxJava/javadoc/io/reactivex/Observable.html)`

> 后期编辑 2:从 2.1.0-alpha01 室开始，用`@Insert`、`@Delete`或`@Update`标注的 DAO 方法支持 Rx 返回类型`Completable`、`Single<T>`和 `Maybe<T>`。

让我们考虑下面的 UI:用户能够看到和编辑他们的用户名。这些信息和其他关于用户的信息都保存在数据库中。下面介绍如何插入、更新、删除和查询用户。

# 插入

与 RxJava 的房间集成允许插入以下相应的返回类型:

*   `Completable` —插入完成后立即调用`onComplete`
*   `Single<Long>`或`Maybe<Long>`—`onSuccess`上发出的值是插入项目的行 id
*   `Single<List<Long>>`或`Maybe<List<Long>>` —其中`onSuccess`上发出的值是插入项目的行 id 列表

如果数据插入错误，`Completable`、`Single`和`Maybe`将在`onError`中发出异常。

```
@Insert
Completable insert(User user);// or
@Insert
Maybe<Long> insert(User user);// or
@Insert
Single<Long> insert(User[] user);// or
@Insert
Maybe<List<Long>> insert(User[] user);// or
@Insert
Single<List<Long>> insert(User[] user);
```

用`observeOn`操作器指定`Observer`观察`Observable`的`Scheduler`和`subscribeOn`指定`Observable`操作的`Scheduler`。

# 更新/删除

房间与 RxJava 的集成允许更新/删除以下相应的退货类型:

*   `Completable` —更新/删除完成后立即调用`onComplete`
*   `Single<Integer>`或`Maybe<Integer>` —其中 onSuccess 上发出的值是受更新/删除影响的行数

```
@Update
Completable update(User user);// or
@Update
Single<Integer> update(User user);// or
@Update
Single<Integer> updateAll(User[] user);// or
@Delete
Single<Integer> deleteAll(User[] user);// or
@Delete
Single<Integer> deleteAll(User[] user);
```

用`observeOn`操作器指定`Observer`观察`Observable`的`Scheduler`和`subscribeOn`指定`Observable`操作的`Scheduler`。

# 询问

为了从数据库中获取用户，我们可以在数据访问对象类(`UserDao`)中编写以下查询:

```
@Query(“SELECT * FROM Users WHERE id = :userId”)
User getUserById(String userId);
```

这种方法有两个缺点:

1.  这是一个阻塞的同步调用
2.  每次修改用户数据时，我们都需要手动调用这个方法

Room 提供观察数据库中数据的选项，并借助 RxJava [Maybe](http://reactivex.io/RxJava/javadoc/io/reactivex/Maybe.html) 、 [Single](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html) 和[flow](http://reactivex.io/RxJava/javadoc/io/reactivex/Flowable.html)对象执行异步查询。

如果你担心线程，Room 会让你放松，并确保**可观察的查询是在主线程**之外完成的。通过在`observeOn`方法中设置`Scheduler`，由您决定在哪个线程上向下游发出事件。

对于返回`Maybe`或`Single`的查询，确保您使用不同于`AndroidSchedulers.mainThread()`的`Scheduler`调用`subscribeOn`。

要开始在 RxJava 2 中使用 Room，只需将以下依赖项添加到您的`build.gradle`文件中:

```
// RxJava support for Room
implementation “android.arch.persistence.room:rxjava2:1.0.0-alpha5”// Testing support
androidTestImplementation “android.arch.core:core-testing:1.0.0-alpha5”
```

## **也许是**

```
@Query(“SELECT * FROM Users WHERE id = :userId”)
**Maybe**<User> getUserById(String userId);
```

事情是这样的:

1.  当数据库中没有用户并且查询没有返回行时，`Maybe`将**完成**。
2.  当数据库中有**用户时，`Maybe`将触发`**onSuccess**` **并完成**。**
3.  如果在`Maybe`完成后**用户被更新**，则**不会发生任何事情**。

## **单身**

```
@Query(“SELECT * FROM Users WHERE id = :userId”)
**Single**<User> getUserById(String userId);
```

以下是一些场景:

1.  当**数据库**中没有用户并且查询没有返回行时，`Single`将触发`**onError(EmptyResultSetException.class)**`
2.  当数据库中有**用户时，`Single`会触发`**onSuccess**`。**
3.  如果在`Single`完成后**用户被更新**，则**不会发生任何事情**。

## **可流动/可观察**

```
@Query(“SELECT * FROM Users WHERE id = :userId”)
**Flowable**<User> getUserById(String userId);
```

下面是`Flowable` / `Observable`的行为方式:

1.  当**数据库**中没有用户，查询没有返回行时，`**Flowable**` **不会发出**、`onNext`和`onError`。
2.  当数据库中有**用户时，`Flowable`将触发`**onNext**`。**
3.  每次**用户数据更新**时，`Flowable`对象会自动**发出**，让你根据最新数据更新 UI。

# 测试

测试一个返回`Maybe` / `Single` / `Flowable`的查询与测试它的同步对等项没有太大的不同。在`UserDaoTest`中，我们确保使用内存数据库，因为当进程被终止时，存储在这里的信息会被自动清除。

```
@RunWith(AndroidJUnit4.class)
public class UserDaoTest {
…
private UsersDatabase mDatabase;@Before
public void initDb() throws Exception {
    mDatabase = Room.*inMemoryDatabaseBuilder*(
                     InstrumentationRegistry.*getContext*(),
                     UsersDatabase.class)
            // allowing main thread queries, just for testing
            .allowMainThreadQueries()
            .build();
}

@After
public void closeDb() throws Exception {
    mDatabase.close();
}
```

将`InstantTaskExecutorRule`规则添加到您的测试中，以确保 Room 立即执行所有数据库操作。

```
@Rule
public InstantTaskExecutorRule instantTaskExecutorRule = 
                                      new InstantTaskExecutorRule();
```

让我们实现一个测试，订阅`getUserById`的发射，并检查当用户被插入时，正确的数据是否由`Flowable`发射。

```
@Test
public void insertAndGetUserById() {
    // Given that we have a user in the data source
    mDatabase.userDao().insertUser(*USER*); // When subscribing to the emissions of user
    mDatabase.userDao()
             .getUserById(*USER*.getId())
             .test()
             // assertValue asserts that there was only one emission
             .assertValue(new Predicate<User>() {
                @Override
                public boolean test(User user) throws Exception {
                    // The emitted user is the expected one
                    return user.getId().equals(*USER*.getId()) &&
                      user.getUserName().equals(*USER*.getUserName());
                }
            });
}
```

就是这样！如果您在应用程序中使用 RxJava 2，也要让您的数据库具有反应性，并确保您的 UI 总是显示最新的数据。点击查看使用 Room 和 RxJava [的示例应用程序。](https://github.com/googlesamples/android-architecture-components/tree/master/BasicRxJavaSample)