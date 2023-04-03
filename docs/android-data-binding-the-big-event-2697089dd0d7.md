# Android 数据绑定:大事件

> 原文：<https://medium.com/androiddevelopers/android-data-binding-the-big-event-2697089dd0d7?source=collection_archive---------0----------------------->

## 你甚至不需要打扮

在以前的文章中，我写了如何从 AndroId 应用程序中消除 findViewById，以及在某些情况下完全消除对视图 id 的需求的 T2。我在那些文章中没有明确提到的一件事是如何处理事件监听器，比如[视图的 OnClickListener](https://developer.android.com/reference/android/view/View.OnClickListener.html) 和[文本视图的 TextWatcher](https://developer.android.com/reference/android/text/TextWatcher.html) 。

Android 数据绑定提供了三种机制来在布局文件中设置事件监听器，您可以选择最方便的一种。与标准的 Android onClick 属性不同，没有一个事件数据绑定机制使用反射，因此无论您选择哪种机制，性能都很好。

## 监听器对象

对于任何带有使用 set*调用(与 add*调用相反)的侦听器的视图，您可以将侦听器对象绑定到属性。例如:

```
<**View *android:onClickListener="@{callbacks.clickListener}"* ...**/>
```

其中侦听器是用 getter 或公共字段定义的，如:

```
**public class** Callbacks {
    **public** View.OnClickListener **clickListener**;
}
```

这里也有一个捷径，在这里“监听器”被剥离了:

```
<**View *android:onClick="@{listeners.clickListener}"* ...**/>
```

当您的应用程序已经使用侦听器对象时，就使用与侦听器对象的绑定，但是在大多数情况下，您将使用另外两种方法中的一种。

## 方法引用

通过方法引用，您可以将一个方法单独挂接到任何事件侦听器方法。任何静态或实例方法都可以使用，只要它与侦听器中的参数和返回类型相同。例如:

```
<**EditText
    *android:afterTextChanged="@{callbacks::nameChanged}"* ...**/>
```

其中回调有一个如下声明的 nameChanged 方法:

```
**public class** Callbacks {
    **public void** nameChanged(Editable editable) {
        *//...* }
}
```

所使用的属性位于“android”名称空间中，并且与侦听器中的方法名称相匹配。

虽然不建议这样做，但是您也可以在绑定中执行一些逻辑操作:

```
<**EditText android:afterTextChanged=
    "@{user.hasName?callbacks::nameChanged:callbacks::idChanged}"
    ...**/>
```

在大多数情况下，最好将逻辑放在被调用的方法中。当您可以向方法传递额外的信息时，这就变得容易多了(就像上面的 user)。你可以用 lambda 表达式做到这一点。

## λ表达式

您可以提供一个 lambda 表达式，并向您的方法传递您想要的任何参数。例如:

```
<**EditText
 *android:afterTextChanged="@{(e)->callbacks.textChanged(user, e)}"*
 ...** />
```

textChanged 方法接受传递的参数:

```
**public class** Callbacks {
    **public void** textChanged(User user, Editable editable) {
        **if** (user.hasName()) {
            *//...* } **else** {
            *//...* }
    }
}
```

如果您不需要来自侦听器的任何参数，可以使用以下语法删除它们:

```
<**EditText
 *android:afterTextChanged="@{()->callbacks.textChanged(user)}"*
 ...** />
```

但是你不能只拿走其中的一部分——要么全部拿走，要么一个都不拿走。

在方法引用和 lambda 表达式之间，表达式求值的时间也不同。对于方法引用，表达式在绑定时计算。对于 lambda 表达式，它在事件发生时进行计算。

例如，假设还没有设置回调变量。使用方法引用时，表达式的计算结果为 null，并且不会设置任何侦听器。对于 lambda 表达式，总是设置一个侦听器，并在事件引发时计算表达式。通常这没多大关系，但是当有返回值时，将返回默认的 Java 值，而不是没有调用。例如:

```
**<View android:onLongClick=”@{()->callbacks.longClick()}” …/>**
```

当回调为空时，返回 false。在这种错误情况下，您可以使用更长的表达式来返回您希望返回的类型:

```
**<View android:onLongClick=”@{()->callbacks == null ? true : callbacks.longClick()}” …/>**
```

通过确保没有空表达式求值，您通常可以完全避免这种情况。

Lambda 表达式可以在与方法引用相同的属性上使用，因此您可以轻松地在它们之间切换。

## 用哪个？

最灵活的机制是 lambda 表达式，它允许您为回调提供与事件侦听器不同的参数。

在许多情况下，您的回调将采用与 listener 方法中给出的参数完全相同的参数。在这种情况下，方法引用提供了更短的语法，也更容易阅读。

在您正在转换以使用 Android 数据绑定的应用程序中，您可能已经在视图上设置了监听器对象。您可以将侦听器作为变量传递给布局，并将其分配给视图。