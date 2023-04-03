# 房间+时间

> 原文：<https://medium.com/androiddevelopers/room-time-2b4cf9672b98?source=collection_archive---------0----------------------->

![](img/94e6a3ced380aedff89670a24214cf9c.png)

[Big Ben](https://flic.kr/p/oz1D44) by James Cullen

如果您已经开始使用 [Room](https://developer.android.com/topic/libraries/architecture/room.html) (如果您还没有使用的话，您应该这样做)，那么您很可能需要存储+检索某种日期/时间。Room 没有提供任何现成的支持，而是提供了可扩展的 [@TypeConverter](https://developer.android.com/reference/android/arch/persistence/room/TypeConverter.html) 注释，允许您提供从任意对象到 Room 理解的类型的映射，反之亦然。

该 API 的[文档](https://developer.android.com/topic/libraries/architecture/room.html#type-converters)中的典型示例实际上是日期/时间:

```
public class Converters {
    @TypeConverter
    public static Date fromTimestamp(Long value) {
        return value == null ? null : new Date(value);
    }

    @TypeConverter
    public static Long dateToTimestamp(Date date) {
        return date == null ? null : date.getTime();
    }
}
```

我在我的应用程序中使用了完全相同的代码，虽然它在技术上可行，但它有两个大问题。首先，它使用了 [Date](https://developer.android.com/reference/java/util/Date.html) 类，在几乎所有情况下都应该避免使用[类。`Date`的主要问题是它**不支持时区**。一点也不。](https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/)

第二个问题是它将值作为简单的`Long`持久化，这同样不能存储任何时区信息。

因此，假设我们使用上面的转换器将一个日期实例保存到数据库中，然后再检索它。如何知道原始值来自哪个时区？简单的回答就是你不能知道。您能做的最好的事情是尝试并确保所有的日期实例使用一个共同的时区，比如 UTC。虽然这允许您将不同的检索值相互比较(例如，用于排序)，但您永远无法找到原始时区。

我决定花一个小时尝试修复我的应用程序中的时区问题。

## SQLite +日期/时间

我调查的第一件事是 SQLite 对日期和时间值的支持，事实上它确实支持它们。当您使用 Room 时，它控制您的类值映射到哪些 SQL 数据类型。比如`String`会映射到`TEXT`，`Int`会映射到`INTEGER`等等。但是我们如何告诉 Room 映射我们的对象日期+时间值呢？简单的答案是我们不需要。

SQLite 是一个松散类型的数据库系统，它将所有值存储为:`NULL`、`INTEGER`、`TEXT`、`REAL`或`BLOB`之一。您会注意到，没有其他数据库系统中常见的特殊日期或时间类型。相反，他们提供了以下关于如何存储日期/时间值的[文档](https://sqlite.org/datatype3.html#datetime):

> SQLite 没有专门用于存储日期和/或时间的存储类。相反，SQLite 的内置[日期和时间函数](https://sqlite.org/lang_datefunc.html)能够将日期和时间存储为文本、实数或整数值

正是这些日期和时间函数允许我们以最小/无精度损失存储高保真的日期时间值，特别是使用`TEXT`类型，因为它支持 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 字符串。

因此，我们只需要将我们的值保存为特殊格式的文本，其中包含我们需要的所有信息。如果需要，我们可以使用上面提到的 SQLite 函数将文本转换成 SQL 中的日期/时间。我们唯一需要做的就是确保我们的代码使用正确的格式。

## 回到应用程序

所以我们知道 SQLite 支持我们所需要的，但是我们需要决定如何在我们的应用程序中表现它。

我在我的应用程序中使用的是 ThreeTen-BP，它是 JDK 8 的日期和时间库(JSR-310)的移植，但是可以在 JDK 6+上运行。这个库支持时区，所以我们将使用它的一个类来表示应用程序中的日期+时间: [OffsetDateTime](http://www.threeten.org/threetenbp/apidocs/org/threeten/bp/OffsetDateTime.html) 。这个类是 UTC/GMT 特定偏移量内的时间和日期的不可变表示。

所以当我们看我的一个实体时，我们现在使用 OffsetDateTime 而不是 Date:

```
@Entity(tableName = "users")
data class User(
        @PrimaryKey val id: Long? = null,
        val username: String,
        **val joined_date: OffsetDateTime? = null**
)
```

这是更新的实体，但现在我们必须更新我们的 TypeConverters，以便 Room 了解如何保存/恢复`OffsetDateTime`值:

```
object TiviTypeConverters {
    private val formatter = DateTimeFormatter.***ISO_OFFSET_DATE_TIME***@TypeConverter
    @JvmStatic
    fun toOffsetDateTime(value: String?): OffsetDateTime? {
        return value?.*let* **{** return formatter.parse(value, OffsetDateTime::from)
        **}** }

    @TypeConverter
    @JvmStatic
    fun fromOffsetDateTime(date: OffsetDateTime?): String? {
        return date?.format(formatter)
    }
}
```

我们现在将`OffsetDateTime`映射到`String`，而不是之前的`Date`到/从`Long`的映射。

这些方法看起来非常简单:一个将 OffsetDateTime 格式化为字符串，另一个将字符串解析为 OffsetDateTime。这里的关键难题是确保我们使用正确的字符串格式。谢天谢地，ThreeTen-BP 为我们提供了一个兼容的版本`DateTimeFormatter.**ISO_OFFSET_DATE_TIME**`。

不过您可能没有使用这个库，所以让我们来看一个格式化字符串的例子:`2013-10-07T17:23:19.540-04:00`。希望您能看到这代表着什么日期:UTC-4，2013 年 10 月 7 日 17:23:19.540。只要您格式化/解析成这样的字符串，SQLite 就能够理解它。

至此，我们差不多完成了。如果您运行应用程序，通过适当的数据库版本增加+迁移，您会看到一切都应该工作得很好。

更多关于带房间迁移的信息，请看[弗洛里纳·芒特内斯库](https://medium.com/u/d5885adb1ddf?source=post_page-----2b4cf9672b98--------------------------------)的帖子:

[](/google-developers/understanding-migrations-with-room-f01e04b07929) [## 了解迁移与空间

### 用 SQLite API 执行数据库迁移总是让我感觉像是在拆除一颗炸弹——好像我就是一颗…

medium.com](/google-developers/understanding-migrations-with-room-f01e04b07929) 

## 整理房间

我们还没有解决的一件事是在 SQL 中查询日期列。之前的日期/长整型映射有一个隐含的好处，即数字的排序和查询效率极高。移动到字符串多少会破坏这一点，所以让我们来修复它。

假设我们以前有一个查询，它返回按加入日期排序所有用户。您可能会看到这样的内容:

```
@Dao
interface UserDao {
    @Query("**SELECT * FROM users ORDER BY joined_date**")
    fun getOldUsers(): List<User>
}
```

由于`joined_date`是一个数字(`long`，记住)，SQLite 会做一个简单的数字比较并返回结果。如果您使用新的文本实现运行相同的查询，您可能会注意到结果看起来是一样的，但是它们是一样的吗？

答案是肯定的，大部分时间是这样。对于文本实现，SQLite 进行的是文本排序，而不是数字排序，这在大多数情况下是正确的。让我们来看一些示例数据:

```
**id  |  joined_date**
------------------------------------
1   |  2017-10-17T07:23:19.120+00:00
2   |  2017-10-17T09:36:27.526+00:00
3   |  2017-10-17T11:01:12.972+00:00
4   |  2017-10-17T17:57:01.784+00:00
```

一个简单的从左到右的字符串排序在这里起作用，因为字符串的所有组成部分都是按降序排列的(年、月、日等等)。问题来自字符串的最后一部分，时区偏移量。让我们稍微调整一下数据，看看会发生什么:

```
**id  |  joined_date**
------------------------------------
1   |  2017-10-17T07:23:19.120+00:00
2   |  2017-10-17T09:36:27.526+00:00
3   |  2017-10-17T11:01:12.972**-02:00**
4   |  2017-10-17T17:57:01.784+00:00
```

您可以看到第三行的时区已经从 UTC 更改为 UTC-2。这导致它的加入时间实际上是 UTC 的`09:01:12`,因此它实际上应该被排序为第二行。但是返回的列表包含了和以前一样的顺序。这是因为我们仍然使用字符串排序，它没有考虑时区。

## SQLite 日期时间函数

那我们怎么解决呢？还记得那些 SQLite 日期/时间函数吗？我们只需要确保在与 SQL 中的任何日期/时间列交互时使用它们。SQLite 提供了 5 个[函数](https://sqlite.org/lang_datefunc.html):

1.  `date(...)`只返回日期。
2.  `time(...)`返回正确的时间。
3.  `datetime(...)`返回日期和时间。
4.  `julianday(...)`返回[儒略日](https://en.wikipedia.org/wiki/Julian_day)。
5.  `strftime(...)`返回用给定格式字符串格式化的值。前四个可以被认为是具有预定义格式的`strftime`的变体。

因为我们想对日期和时间进行排序，我们可以使用`datetime(...)`函数。如果我们回到我们的 DAO，查询现在变成了:

```
@Dao
interface UserDao {
    @Query("**SELECT * FROM users ORDER BY datetime(joined_date)**")
    fun getOldUsers(): List<User>
}
```

够简单了吧？完成这一更改后，我们现在得到了正确的语义排序:

```
**id  |  joined_date**
------------------------------------
1   |  2017-10-17T07:23:19.120+00:00
3   |  2017-10-17T11:01:12.972**-02:00** 2   |  2017-10-17T09:36:27.526+00:00
4   |  2017-10-17T17:57:01.784+00:00
```

这就是我工作完成的时间！我们现在支持房间内的时区日期/时间。

[](https://github.com/chrisbanes/tivi/commit/bd517e2b2fb54046a5e3c8bdcd31133ad1db1b19) [## 使用 DateTimeOffset 代替 Date chrisbanes/tivi@bd517e2

### tivi - Tivi 是一个追踪 Android 应用程序的正在进行中的电视节目，它连接到 Trakt.tv。它仍处于早期…

github.com](https://github.com/chrisbanes/tivi/commit/bd517e2b2fb54046a5e3c8bdcd31133ad1db1b19) 

* 1 小时调查+代码，3 小时写这篇博文。🙃

*注意:我在这篇文章中使用了 ThreeTenBP，但是它可以与任何支持时区的时间/日期 API 一起工作，例如* [*ICU 日历*](https://developer.android.com/reference/android/icu/util/Calendar.html) *(在 API 24+上)*[*Joda-Time*](http://www.joda.org/joda-time/)*，甚至是* [*日历*](https://developer.android.com/reference/java/util/Calendar.html) *类。我将把它作为一个练习留给读者，让他们为他们希望使用的 API 找出 TypeConverter 实现。*