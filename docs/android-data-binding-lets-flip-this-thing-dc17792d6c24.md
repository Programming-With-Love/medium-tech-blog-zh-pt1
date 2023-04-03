# Android 数据绑定:让我们翻转这个东西

> 原文：<https://medium.com/androiddevelopers/android-data-binding-lets-flip-this-thing-dc17792d6c24?source=collection_archive---------1----------------------->

![](img/f3f725fa9a692999aed54ce95c8d7cf5.png)

## 将用户输入返回到应用程序中

到目前为止，我已经展示了如何使用 Android 数据绑定来[将信息从您的应用程序放入 UI](/google-developers/android-data-binding-adding-some-variability-1fe001b3abcc#.mla11ue7w) 并[将 UI 事件](/google-developers/android-data-binding-the-big-event-2697089dd0d7#.1j9a14hdm)绑定到您的应用程序。我们通常想要一件非常简单的事情:将用户输入的数据返回到应用程序模型中。Android Studio 2.1 引入了双向绑定，使这变得更加容易。

## 双向装订

假设我们有一个供用户填写的表单，其中包含用户名字的编辑文本。单向数据绑定的工作方式如下:

```
<**EditText
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="*@{user.firstName}*"
    android:id="@+id/firstName"** />
```

通常，您会让用户输入数据，然后提交表单。或者，您可能会有额外的想法，安装一个事后修改的监听器来更新您的模型。您可以使用双向绑定操作符“@={}”来用用户的更改自动更新视图模型，而不是尝试这样做:

```
<**EditText
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="*@={user.firstName}*"**/>
```

因为模型会随着用户的输入而更新，所以我不再需要主动从 EditText 中检索名称。我还删除了 ID，因为不再需要它了。

双向绑定并不适用于每一个属性——只适用于那些有变更事件通知的属性。幸运的是，这包括了几乎所有你真正关心的用户输入。请记住，Android 数据绑定旨在适用于从 API 7 开始的所有版本，因此它不依赖于任何新的框架变化。否则，几乎每个属性都支持双向数据绑定，因为我们可以为缺失的属性添加通知。

## 查看属性

现在，您还可以从表达式中访问视图属性，就像数据模型中的属性一样:

```
<**CheckBox
    android:id="@+id/showName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"**/><**TextView
    android:text="@{user.firstName}"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:visibility="*@{showName.checked ? View.VISIBLE : View.GONE}*"** />
```

在上面的布局中，只有当 showName 复选框被选中时，显示用户名字的 TextView 才可见。这在引用双向可绑定属性时有效。它还适用于任何具有数据绑定表达式的属性:

```
<**CheckBox
    android:id="@+id/showName"
    android:focusableInTouchMode="*@{model.allowFocusInTouchMode}*"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"**/><**TextView
    android:text="@{user.firstName}"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:focusableInTouchMode="*@{showName.focusableInTouchMode}*"** />
```

当然，上面的例子是没有意义的，但是它演示了如何使用这种类型的表达式链接。

在幕后，数据绑定框架看到“showName.focusableInTouchMode ”,并将其识别为绑定到“model.allowFocusInTouchMode”的数据，然后进行简单的替换。

## 查看参考

另一件好事是，您还可以在事件处理程序 lambda 表达式中通过 id 引用视图:

```
<**EditText
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:id="@+id/firstName"
    android:text="@={user.firstName}"** /><**CheckBox
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onCheckedChanged="*@{()->handler.checked(firstName)}*"** />
```