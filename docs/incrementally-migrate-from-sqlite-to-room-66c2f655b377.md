# 从 SQLite 到 Room 的增量迁移

> 原文：<https://medium.com/androiddevelopers/incrementally-migrate-from-sqlite-to-room-66c2f655b377?source=collection_archive---------3----------------------->

![](img/6c90dee4a58939001c954c4cbf3860c3.png)

Photo by [Mikito Tateisi](https://unsplash.com/photos/bJhT_8nbUA0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

*将您复杂的数据库迁移到配有可管理 PRs 的房间。*

你听说过 Room——也许你查阅了[文档](https://developer.android.com/topic/libraries/architecture/room.html)，看了[视频](https://www.youtube.com/watch?v=H7I3zs-L-1w)或[两个](https://youtu.be/DeIKyVfCvC0)，并决定开始将 Room 整合到你的项目中。如果您的数据库只有几个表和简单的查询，那么您可以通过下面的 7 个步骤，用一个相对较小的 pull 请求轻松地迁移到 Room。

[](/google-developers/7-steps-to-room-27a5fe5f99b2) [## 7 步到房间

### 如何将您的应用程序迁移到 Room 的分步指南

medium.com](/google-developers/7-steps-to-room-27a5fe5f99b2) 

然而，如果您的数据库比较大或者有复杂的查询，那么实现所有的实体、Dao、Dao 的测试以及替换`SQLiteOpenHelper`的用法可能需要很长时间；你最终会得到一个大的拉取请求，需要时间来实现和审查。让我们看看如何使用可管理的 PRs 逐步从 SQLite 迁移到 Room。

## TL；博士:

> ***首先 PR*** *:创建您的* ***实体*** *类，将****room database****，并从您自定义的 SQLiteOpenHelper 更新为*[***SupportSQLiteOpenHelper***](https://developer.android.com/reference/android/arch/persistence/db/SupportSQLiteOpenHelper.html)*。*
> 
> ***跟随*** *:逐渐创建****DAOs****来替换光标和内容值代码。*

# 项目设置

让我们考虑以下情况:

*   我们的数据库有 10 个表，每个表都有一个对应的模型对象。例如，对于`users`表，我们有一个相应的`User`对象。
*   一个`CustomDbHelper`类扩展`SQLiteOpenHelper`
*   使用`CustomDbHelper`访问数据库的类是`LocalDataSource`。
*   我们有`LocalDataSource`的测试。

# 首次公关

您的初始 PR 将包含设置房间数据库所需的最少量的更改。

## 创建实体类

如果已经有了每个表的数据模型对象，只需添加`[@Entity](https://developer.android.com/reference/android/arch/persistence/room/Entity.html)`、`[@PrimaryKe](https://developer.android.com/reference/android/arch/persistence/room/PrimaryKey.html)y`和`[@ColumnInfo](https://developer.android.com/reference/android/arch/persistence/room/ColumnInfo.html)`注释。

```
+ @Entity(tableName = "users")
  public class User {

    + @PrimaryKey
    + @ColumnInfo(name = "userid")
      private int mId;

    + @ColumnInfo(name = "username")
      private String mUserName;

      public User(int id, String userName) {
          this.mId = id;
          this.mUserName = userName;
      }

      public int getId() { return mId; }

      public String getUserName() { return mUserName; }
}
```

## 创建房间数据库

创建一个扩展`[RoomDatabase](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.html)`的抽象类。在`[@Database](https://developer.android.com/reference/android/arch/persistence/room/Database.html)`注释中，列出您已经创建的所有实体类。目前，我们不需要创建 DAO 类。

增加数据库版本号并实施迁移。如果您没有更改数据库模式，您仍然需要实现一个空迁移来告诉空间保留现有的数据。

```
**@Database**(**entities** = {<all entity classes>}, 
          version = <**incremented_sqlite_version**>)
public abstract class AppDatabase extends RoomDatabase { private static UsersDatabase INSTANCE; static final **Migration**      MIGRATION_<sqlite_version>_<incremented_sqlite_version> 
= new Migration(<sqlite_version>, <incremented_sqlite_version>) { @Override public void migrate(
                    SupportSQLiteDatabase database) {
           // Since we didn’t alter the table, there’s nothing else 
           // to do here.
         }
    };
```

# 更新使用 SQLiteOpenHelper 的类

最初，我们的`LocalDataSource`与一个`CustomOpenHelper`类一起工作。我们将对此进行更新，以使用来自`[RoomDatabase.getOpenHelper()](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.html#getOpenHelper())`的`**SupportSQLiteOpenHelper**`。

```
public class LocalUserDataSource {
    private SupportSQLiteOpenHelper mDbHelper; LocalUserDataSource(@NonNull SupportSQLiteOpenHelper helper) {
       mDbHelper = helper;
    }
```

因为`SupportSQLiteOpenHelper`不直接扩展`SQLiteOpenHelper`，而是对其进行包装，我们需要更新调用以获得可写和可读的数据库，并使用`SupportSQLiteDatabase`而不是`SQLiteDatabase`。

```
**SupportSQLiteDatabase** db = mDbHelper.getWritableDatabase();
```

`[SupportSQLiteDatabase](https://developer.android.com/reference/android/arch/persistence/db/SupportSQLiteDatabase.html)`是一个数据库抽象，提供了与`SQLiteDatabase`相似的方法。因为它为插入和查询数据库提供了一个更简洁的 API，所以需要对代码进行一些修改。

对于插入，Room 删除可选的`nullColumnHack`参数。用`[SupportSQLiteDatabase.insert](https://developer.android.com/reference/android/arch/persistence/db/SupportSQLiteDatabase.html#insert(java.lang.String,%20int,%20android.content.ContentValues))`代替`[SQLiteDatabase.insertWithOnConflict](https://developer.android.com/reference/android/database/sqlite/SQLiteDatabase.html#insertWithOnConflict(java.lang.String,%20java.lang.String,%20android.content.ContentValues,%20int))`。

```
@Override
public void insertOrUpdateUser(User user) {
    **SupportSQLiteDatabase** db = mDbHelper.getWritableDatabase();

    ContentValues values = new ContentValues();
    values.put(*COLUMN_NAME_ENTRY_ID*, user.getId());
    values.put(*COLUMN_NAME_USERNAME*, user.getUserName());

    - db.insertWithOnConflict(*TABLE_NAME*, null, values,
    -        SQLiteDatabase.*CONFLICT_REPLACE*); + db.**insert**(TABLE_NAME, SQLiteDatabase.CONFLICT_REPLACE,
    + values); db.close();
}
```

对于查询，`SupportSQLiteDatabase`提供了 4 种方法:

```
Cursor query(String query);
Cursor query(String query, Object[] bindArgs);
Cursor query(SupportSQLiteQuery query);
Cursor query(SupportSQLiteQuery query, CancellationSignal cancellationSignal);
```

如果您使用原始查询，那么不需要任何更改。如果您的查询更复杂，您必须通过`[SupportSQLiteQueryBuilder](https://developer.android.com/reference/android/database/sqlite/SQLiteQueryBuilder.html)`创建一个`[SupportSQLiteQuery](https://developer.android.com/reference/android/arch/persistence/db/SupportSQLiteQuery.html)`。

例如，我们有一个`users`表，我们只想得到表中的第一个用户，按姓名排序。下面是使用`SQLiteDatabase`和`SupportSQLiteDatabase`时该方法的样子。

```
public User getFirstUserAlphabetically() {
        User user = null;
        SupportSQLiteDatabase db = mDbHelper.getReadableDatabase();
        String[] projection = {
                *COLUMN_NAME_ENTRY_ID*,
                *COLUMN_NAME_USERNAME* };

        // Get the first user from the table ordered alphabetically
        - Cursor cursor = db.query(*TABLE_NAME*, projection, null,
        - null, null, null, *COLUMN_NAME_USERNAME* + “ ASC “, “1”);

        + SupportSQLiteQuery query =
        +  SupportSQLiteQueryBuilder.builder(*TABLE_NAME*)
        +                           .columns(projection)
        +                           .orderBy(*COLUMN_NAME_USERNAME*)
        +                           .limit(“1”)
        +                           .create();

        + Cursor cursor = db.query(query);

        if (c !=null && c.getCount() > 0){
            // read data from cursor
              ... }
        if (c !=null){
            cursor.close();
        }
        db.close();
        return user;
    }
```

> 如果您没有针对 SQLiteOpenHelper 实现用法的**测试**，我强烈建议您首先编写测试，然后迁移到 Room，以降低回归问题的风险。

# 遵循 PRs

现在您的数据层正在使用 Room，您可以开始逐步创建 DAO(通过测试)并用 DAO 调用替换`Cursor`和`ContentValue`代码。

从按姓名排序的`users`表中获取第一个用户的查询将在`UserDao`类中定义。

```
@Dao
public interface UserDao { @Query(“SELECT * FROM Users ORDERED BY name ASC LIMIT 1”)
    User getFirstUserAlphabetically();}
```

该方法将在`LocalDataSource`中使用。

```
public class LocalDataSource { private UserDao mUserDao; public User getFirstUserAlphabetically() {
        return mUserDao.getFirstUserAlphabetically();
     }
}
```

在单个 PR 中将大型数据库从 SQLite 移动到 Room 将包含大量新的和更新的文件；它可能需要相当长的时间来实施，从而使公关更难审查。使用`RoomDatabase`公开的 OpenHelper 对初始 PR 的代码进行最小的修改，然后逐渐添加 Dao 来替换后续 PR 中的`Cursor`和`ContentValue`代码。

有关房间的更多信息，请查看以下文章:

[](/google-developers/7-pro-tips-for-room-fbadea4bfbd1) [## 房间的 7 个小贴士

### 了解如何充分利用空间

medium.com](/google-developers/7-pro-tips-for-room-fbadea4bfbd1) [](/google-developers/understanding-migrations-with-room-f01e04b07929) [## 了解迁移与空间

### 用 SQLite API 执行数据库迁移总是让我感觉像是在拆除一颗炸弹——好像我就是一颗…

medium.com](/google-developers/understanding-migrations-with-room-f01e04b07929) [](/google-developers/testing-room-migrations-be93cdb0d975) [## 测试室迁移

### 在之前的一篇文章中，我解释了带房间的数据库迁移是如何工作的。我们看到一个不正确的…

medium.com](/google-developers/testing-room-migrations-be93cdb0d975) [](/google-developers/room-rxjava-acb0cd4f3757) [## 房间🔗RxJava

### 使用 RxJava 在房间中进行查询

medium.com](/google-developers/room-rxjava-acb0cd4f3757)