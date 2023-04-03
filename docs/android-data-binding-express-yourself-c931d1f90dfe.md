# Android 数据绑定:表达自己

> 原文：<https://medium.com/androiddevelopers/android-data-binding-express-yourself-c931d1f90dfe?source=collection_archive---------1----------------------->

## 不仅仅是现场访问

正如在[上一篇文章](/google-developers/android-data-binding-adding-some-variability-1fe001b3abcc#.o3qd00bpq)中所讨论的，你可以在 Android Studio 的布局文件中分配变量值。在该示例中，用户名字的文本是用表达式设置的:

```
**android:text="@{user.firstName}"**
```

用户类被声明为一个简单的普通旧 Java 对象(POJO):

```
**public class** User {
    **public** String **firstName**;
    **public** String **lastName**;
    **public** Bitmap **image**;
}
```

你的大部分类不使用公共字段(我希望如此)，并且你会为它们提供访问器。布局中的表达式应该简短易读，我们不希望开发人员必须在表达式中添加所有这些 getFirstName()和 getLastName()，这会降低表达式的可读性。表达式解析器自动尝试为您的属性查找 Java Bean 访问器名称(getXxx()或 isXxx())。当您的类有访问器方法时，同样的表达式也能很好地工作:

```
**public class** User {
    **private** String **firstName**;
    **private** String **lastName**;
    **private** Bitmap **image**;

    **public** String getFirstName() { **return firstName**; }
    **public** String getLastName() { **return lastName**; }
    **public** Bitmap getImage() { **return image**; }
}
```

如果它找不到像 getXxx()这样名为的方法，它也会寻找名为 Xxx()的方法，所以你可以用 user.hasFriend 来访问方法 hasFriend()。

Android 数据绑定表达式语法也支持使用括号的数组访问，就像 Java 一样:

```
android:text="@{user.friends[0].firstName}"
```

您还可以使用列表和地图的括号作为“get”方法的快捷方式。

它还允许几乎所有的 java 语言表达式，包括方法调用、三元运算符和数学运算。但是不要发疯:

```
android:text='@{user.adult ? ((user.male ? "Mr. " : "Ms. ") + user.lastName) : (user.firstName instanceof String ? user.firstName : "kid") }'
```

没人看得懂。除了硬编码的字符串之外，维护起来将会非常困难。帮你自己一个忙，把你的复杂表达式放回你的模型中，这样你就不必试图解开正在发生的事情。

此外，还有一个零合并运算符？？要缩短简单的三元表达式:

```
android:text=”@{user.firstName ?? user.userName}”
```

本质上与以下内容相同:

```
android:text=”@{user.firstName != null ? user.firstName : user.userName}”
```

使用绑定表达式可以做的一件很酷的事情是使用资源:

```
android:padding=”@{@dim/textPadding + @dim/headerPadding}
```

这样可以省去一堆单独的值声明。有多少次你只是想增加或减少尺寸？唯一的问题是它(还)不支持样式。

您还可以按照资源方法 [getString](https://developer.android.com/reference/android/content/res/Resources.html#getString(int, java.lang.Object...)) 、 [getQuantityString](https://developer.android.com/reference/android/content/res/Resources.html#getQuantityString(int, int, java.lang.Object...)) 和 [getFraction](https://developer.android.com/reference/android/content/res/Resources.html#getFraction(int, int, int)) 中的语法使用字符串、数量和分数格式。您只需将参数作为参数传递给资源:

```
android:text=”@{@string/nameFormat(user.firstName, user.lastName)}”
```

## NullPointerException

一个非常方便的事情是，数据绑定表达式在求值期间总是检查空值。这意味着如果你有这样一个表达式:

```
android:text=”@{user.firstName ?? user.userName}”
```

如果 user 为 null，user.firstName 和 user.userName 的计算结果将为 null，文本将设置为 null。无 NullPointerException。

这并不意味着不可能获得 NullPointerException。例如，如果你有一个表达式:

```
android:text=”@{com.example.StringUtils.capitalize(user.firstName)}”
```

你的 StringUtils 有:

```
**public static** String capitalize(String str) {
    **return** Character.*toUpperCase*(str.charAt(0)) + str.substring(1);
}
```

当空值被传递给大写时，您肯定会看到一个 NullPointerException。

## 进口

在上面的例子中，大写名称的表达式非常长。我们真正想要的是能够导入类型，这样它们就可以被用作一个简化的表达式。您可以通过在数据部分导入它们来实现:

```
<**data**>
    <**variable
        name="user"
        type="com.example.myapp.model.User"**/>
 *<****import
        type="com.example.StringUtils"****/>* </**data**>
```

现在我们的表达式可以简化为:

```
android:text=”@{StringUtils.capitalize(user.firstName)}”
```

## 还有什么？

除了上面提到的一些例外，表达式基本上是 Java 语法。如果你认为它会起作用，它很可能会起作用，所以试一试吧。

如果您有任何详细的问题，请查看数据绑定指南的绑定语法部分:

 [## 数据绑定库

### 这篇文档解释了如何使用数据绑定库来编写声明性的布局并最小化粘合代码…

developer.android.com](https://developer.android.com/topic/libraries/data-binding/index.html#expression_language)