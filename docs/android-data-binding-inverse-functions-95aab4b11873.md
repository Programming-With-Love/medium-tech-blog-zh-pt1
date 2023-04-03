# Android 数据绑定:反函数

> 原文：<https://medium.com/androiddevelopers/android-data-binding-inverse-functions-95aab4b11873?source=collection_archive---------0----------------------->

![](img/52858ab58aa42c4aef7506b968eb20d7.png)

## 双向转换

正如我之前所写的，[你可以绑定数据来自动设置用户输入到视图模型中](/google-developers/android-data-binding-lets-flip-this-thing-dc17792d6c24#.d315my6dm)。例如，您可能希望绑定一个用户名，以便当用户更改它时，它可以立即在视图模型中使用:

```
<**EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="@={data.firstName}"
    android:textSize="16sp"**/>
```

当视图模型的类型与属性类型匹配时，将属性直接绑定到属性会很有效。不幸的是，情况并非总是如此。如果用户在编辑文本中输入一个数值，它不会自动转换成整数。

## 单向转换函数

使用单向数据绑定，从视图模型转换到视图的属性类型非常容易——只需使用静态方法。例如:

```
<**TextView android:text="@{Integer.toString(user.age)}" ...**/>
```

或者，您可以使用字符串串联作为快捷方式:

```
<**TextView android:text="@{`` + user.age}" ...**/>
```

最好的方法可能是使用字符串格式:

```
<**TextView android:text="@{@string/ageFormat(user.age)}" ...**/>
```

这允许格式化考虑本地化。

## 双向转换

双向转换更加困难，因为静态方法没有逆方法，当然也没有任意字符串格式的转换。然而，这是一个非常常见的需求，所以增加了一些转换工具。

字符串到原语的转换非常常见，可以使用双向数据绑定表达式的字符串串联语法来完成，但只能使用空字符串:

```
<**EditText android:text="@={`` + user.age}" ...**/>
```

这对于最简单的转换非常有效。有时用户输入了无效的内容(例如，空文本)，这些简单的转换通过简单地不使用无效的转换更新视图模型来处理这个问题。

很多时候，应用程序需要不同的转换。例如，您可能需要将选择整数转换为枚举。对于单向绑定，这类似于:

```
<**Spinner
    android:selectedItemPosition="@{Conv.toInt(user.numberType)}"
    .../**>
```

这里，`Conv`类包含:

```
**public class** Conv {
    **public static int** toInt(PhoneNumberType phoneNumberType) {
        **return** phoneNumberType.**ordinal()**;
    }
}
```

但是当您想要使用双向转换时，您需要提供从整数转换回枚举的函数。在 [Android Studio 2.3](https://developer.android.com/studio/index.html) 中新增，你现在可以使用`@InverseMethod`注释来声明一个转换方法的逆:

```
**public class** Conv {
    @InverseMethod(**"toPhoneNumberType"**)
    **public static int** toInt(PhoneNumberType phoneNumberType) {
        **return** phoneNumberType.**ordinal**();
    }

    **public static** PhoneNumberType toPhoneNumberType(**int** ordinal) {
        **return** PhoneNumberType.***values***()[ordinal];
    }
}
```

这个表达式现在可以双向表达:

```
<**Spinner
    android:selectedItemPosition="@={Conv.toInt(user.numberType)}"
    .../**>
```

这里有一个稍微复杂一点的例子。假设您想要使用主语言环境来显示和解析来自 EditText 的 double。转换函数是:

```
**public class** Converter {
    @InverseMethod(**"toDouble"**)
    **public static** String toString(TextView view, **double** oldValue,
            **double** value) {
        NumberFormat numberFormat = *getNumberFormat*(view);
        **try** {
            *// Don't return a different value if the parsed value
            // doesn't change* String inView = view.getText().toString();
            **double** parsed = 
                numberFormat.parse(inView).doubleValue();
            **if** (parsed == value) {
                **return** view.getText().toString();
            }
        } **catch** (ParseException e) {
            *// Old number was broken* }
        **return** numberFormat.format(value);
    }

    **public static double** toDouble(TextView view, **double** oldValue,
            String value) {
        NumberFormat numberFormat = *getNumberFormat*(view);
        **try** {
            **return** numberFormat.parse(value).doubleValue();
        } **catch** (ParseException e) {
            Resources resources = view.getResources();
            String errStr = resources.getString(R.string.***badNumber***);
            view.setError(errStr);
            **return** oldValue;
        }
    }

    **private static** NumberFormat getNumberFormat(View view) {
        Resources resources= view.getResources();
        Locale locale = resources.getConfiguration().**locale**;
        NumberFormat format = 
            NumberFormat.*getNumberInstance*(locale);
        **if** (format **instanceof** DecimalFormat) {
            DecimalFormat decimalFormat = (DecimalFormat) format;
            decimalFormat.setGroupingUsed(**false**);
        }
        **return** format;
    }
}
```

您会注意到转换方法有三个参数，而不是只有一个。可以传递任意数量的参数，逆方法必须接受相同数量的参数。除了转换的最后一个参数之外，所有参数及其倒数都是相同的。转换的最终参数类型(这里是 double)和它的反函数的返回类型必须匹配。同样，反函数的最终参数类型(这里是 String)必须与转换方法的返回类型相匹配。

对于双向转换，传递的其他参数是相同的。从 double 转换成 String 应该相当简单，但是`toString()`方法多做了一步。转换时，如果解析值没有变化，字符串值也不会变化。当文本为“10.0”并且用户删除“0”以使文本为“10”时解析后的值是“10 ”,并且您不希望文本在用户编辑期间更改为“10”。

当在`toString()`方法中没有使用时，传递`oldValue`似乎也有点奇怪。但是，该值用于逆向方法，因此也必须传递给转换方法。

逆方法`toDouble()`必须将字符串解析为 double。值可能是无效的，我们必须警告用户。该转换器在无法解析 double 时在 EditText 上显示错误。转换器还必须返回一个 double 值，因此当它无法解析文本时，将返回旧的 double 值。

使用此转换器的绑定表达式为:

```
<**EditText
    android:id="@+id/total"
    android:inputType="numberDecimal"
    android:text="@={Converter.toString(total, user.val, user.val)}"
    ...**/>
```

## 摘要

Android 数据绑定提供了几种使用双向数据绑定表达式进行转换的方法。使用空字符串和一个原语连接将为您提供一个快速和肮脏的双向绑定，用于 EditText。另一种方法是提供你自己的转换方法，用`@InverseMethod`标注。如上所示，逆向方法允许您在转换期间做一些非常强大的事情，包括设置错误文本。双向转换功能应该使应用程序开发更容易从用户那里获取数据。