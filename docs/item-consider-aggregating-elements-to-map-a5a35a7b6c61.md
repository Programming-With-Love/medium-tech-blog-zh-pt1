# 项目:考虑将元素聚合到一个映射中

> 原文：<https://blog.kotlin-academy.com/item-consider-aggregating-elements-to-map-a5a35a7b6c61?source=collection_archive---------1----------------------->

![](img/146ae6999c4b5b579cc3df68e7eb54ff.png)

我们需要多次访问大量的元素，这种情况并不少见。可能是:

*   缓存——我们从一些服务下载数据，然后保存在本地内存中以便更快地访问它们
*   从某个文件加载数据的存储库
*   用于不同类型测试的内存存储库

这些数据可能代表用户、id、配置等的列表。它们通常以列表的形式提供给我们，在我们的记忆中以同样的方式呈现它们是很有诱惑力的:

```
**class** NetworkUserRepo(**val userService**: UserService): UserRepo {
    **private var users**: List<User>? = **null** **override fun** getUser(id: UserId): User? {
        **if**(**users** == **null**) {
            **users** = **userService**.getUsers()
        }
        **return users**?.*firstOrNull* **{ it**.**id** == id **}** }
}

**class** ConfigurationsRepository(
    **val configurations**: List<Configuration>
) {
    **fun** getByName(name: String) = **configurations** .*firstOrNull* **{ it**.**name** == name **}** }class InMemoryUserRepo: UserRepo {
   private val users: MutableList<User> = mutableListOf() override fun getUser(id: UserId): User?
      = users.firstOrNull { it.id == id }

   fun addUser(user: User) {
      user.add(user)
   }
}
```

这很少是存储这些元素的最佳方式。注意我们加载的数据是如何使用的。我们通常通过一些标识符或名称来访问一个元素(这与我们如何在数据库中设计具有唯一键的数据有关)。在列表中查找元素的复杂度为 O(n)，其中 n 是列表的大小。更具体地说，平均需要 n/2 次比较才能找到一个元素。这对于更大的列表来说尤其成问题。解决这个问题的好办法是用`Map`代替`List`。Kotlin 默认使用哈希映射(具体是`LinkedHashMap`)，正如我们在 [Item 41:尊重 hashCode](https://leanpub.com/effectivekotlin/) 的契约中所描述的，当我们使用哈希映射时，查找元素的性能要好得多。实际上，在 JVM 中，由于所使用的 hash map 的大小被调整为 map 本身的大小，如果`hashCode`被正确实现，查找一个元素应该只需要一次比较。

这是`InMemoryRepo`用地图代替列表:

```
class InMemoryUserRepo: UserRepo {
   private val users: MutableMap<UserId, User> = mutableMapOf() override fun getUser(id: UserId): User? = users[id]

   fun addUser(user: User) {
      user.put(user.id, user)
   }
}
```

大多数其他操作，如修改或迭代这些数据(可能使用集合处理方法，如过滤、映射、平面映射、排序、求和等。)对于标准列表和地图具有或多或少相同的性能。

[![](img/62520e901df6820dab70ac182f9613ec.png)](https://www.kt.academy/workshop/refactoringToPatterns)

问题是我们如何从列表转换到地图，反之亦然。为此，我们使用`associate`功能。最常见的是`associateBy`,它构建一个映射，其中值是列表中的元素，键是在 lambda 表达式上生成的:

```
data class User(val id: Int, val name: String)
val users = listOf(User(1, "Michal"), User(2, "Marek"))val byId = users.associateBy { it.id }
byId == mapOf(1 to User(1, "Michal"), 2 to User(2, "Marek")) val byName = users.associateBy { it.name }
byName == mapOf("Michal" to User(1, "Michal"), 
              "Marek" to User(2, "Marek"))
```

请注意，映射中的键必须是唯一的，否则重复项将被删除。这就是为什么我们应该只通过一个唯一的标识符来关联(要通过不唯一的东西来分组，使用`[groupBy](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html)`)。

```
val users = listOf(User(1, "Michal"), User(2, "Michal"))val byName = users.associateBy { it.name }
byName == mapOf("Michal" to User(2, "Michal"))
```

要反过来转换，可以使用`values`:

```
**val** users = *listOf*(User(1, **"Michal"**), User(2, **"Michal"**))
**val** byId = users.*associateBy* **{ it**.**id }** users == byId.**values**
```

这就是使用 map 实现其他存储库以提高元素访问性能的方式:

```
**class** NetworkUserRepo(**val userService**: UserService): UserRepo {
    **private var users**: Map<UserId, User>? = **null
    override fun** getUser(id: UserId): User? {
        **if**(**users** == **null**) {
            **users** = **userService**.getUsers().*associateBy* **{ it**.**id }** }
        **return users**?.get(id)
    }
}

**class** ConfigurationsRepository(
    configurations: List<Configuration>
) {
    **val configurations**: Map<String, Configuration> = 
        configurations.*associateBy* **{ it**.**name }

    fun** getByName(name: String) = **configurations**[name]
}
```

这项技术很重要，但并不适用于所有情况。当我们经常需要访问这些元素时，它会更有用。这就是为什么它在后端特别重要，因为这些集合可能每秒被访问很多次。在前端(我指的也是 Android 或 iOS)用户最多访问几次这个存储库并不重要。我们还需要记住，从列表到映射也需要一些时间，所以如果我们经常这样做，也可能会影响我们的性能。

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注我们。

如果你需要一个科特林工作室，看看我们如何能帮助你: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)