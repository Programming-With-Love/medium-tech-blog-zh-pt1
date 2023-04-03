# 下跨跨度

> 原文：<https://medium.com/androiddevelopers/underspanding-spans-1b91008b97e4?source=collection_archive---------2----------------------->

![](img/2a9a5047f8abb44cc95342eefd90939f.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

跨度是强大的概念，通过提供对 TextPaint 和 Canvas 等组件的访问，允许在字符或段落级别对文本进行样式化。在以前的文章中，我们讨论了如何使用跨度，开箱即用的跨度是什么，如何轻松地创建自己的跨度，以及如何测试它们。

[](/google-developers/spantastic-text-styling-with-spans-17b0c16b4568) [## 带跨度的文本样式

### 要在 Android 中样式化文本，使用 spans！改变一些字符的颜色，使它们可以点击，缩放…

medium.com](/google-developers/spantastic-text-styling-with-spans-17b0c16b4568) 

让我们看看在处理文本时，可以使用哪些 API 来确保特定用例的最佳性能。我们将探索更多关于跨度的内幕，以及框架如何使用它们。最后，我们将看到如何在同一个流程中或流程之间传递跨度，并基于此，在决定创建您自己的定制跨度时，您需要注意什么样的警告。

# 引擎盖下:跨度如何工作

Android 框架在几个类中处理文本样式和跨度:`[TextView](https://developer.android.com/reference/android/widget/TextView.html)`、`[EditText](https://developer.android.com/reference/android/widget/EditText.html)`、布局类(`[Layout](https://developer.android.com/reference/android/text/Layout.html)`、`[StaticLayout](https://developer.android.com/reference/android/text/StaticLayout.html)`、`[DynamicLayout](https://developer.android.com/reference/android/text/DynamicLayout.html)`)和`TextLine`(在`Layout`中使用的一个包私有类)，它取决于几个参数:

*   文本类型:可选、可编辑或不可选择
*   `[BufferType](https://developer.android.com/reference/android/widget/TextView.BufferType.html)`
*   `TextView`的`LayoutParams`型
*   等等

框架检查`Spanned`对象是否包含不同框架跨度的实例，并触发不同的动作。

文本布局和绘制背后的逻辑是复杂的，并分布在不同的类中；在这一节中，我们只能简单地介绍文本是如何处理的，而且只限于某些情况。

每次跨度改变时，`[TextView](https://github.com/aosp-mirror/platform_frameworks_base/blob/master/core/java/android/widget/TextView.java).spanChange`检查跨度是否是`[UpdateAppearance](https://developer.android.com/reference/android/text/style/UpdateAppearance.html)`、`[ParagraphStyle](https://developer.android.com/reference/android/text/style/ParagraphStyle.html)`或`[CharacterStyle](https://developer.android.com/reference/android/text/style/CharacterStyle.html)`的实例，如果是，则使其自身无效，触发视图的新绘制。

`[TextLine](https://github.com/aosp-mirror/platform_frameworks_base/blob/master/core/java/android/text/TextLine.java)`类表示一行样式化的文本，它专门用于扩展`CharacterStyle`、`[MetricAffectingSpan](https://developer.android.com/reference/android/text/style/MetricAffectingSpan.html)`和`[ReplacementSpan](https://developer.android.com/reference/android/text/style/ReplacementSpan.html)`的跨度。这是触发`[MetricAffectingSpan.updateMeasureState](https://developer.android.com/reference/android/text/style/MetricAffectingSpan.html#updateMeasureState(android.text.TextPaint))`和`[CharacterStyle.updateDrawState](https://developer.android.com/reference/android/text/style/CharacterStyle.html#updateDrawState(android.text.TextPaint))`的类。

管理屏幕上可视元素中文本布局的基类是`[android.text.Layout](https://developer.android.com/reference/android/text/Layout.html)`。`Layout`和它的两个子类`[StaticLayout](https://developer.android.com/reference/android/text/StaticLayout.html)`和`[DynamicLayout](https://developer.android.com/reference/android/text/DynamicLayout.html)`检查文本上设置的跨度，以计算行高和布局边距。除此之外，每当显示在`[DynamicLayout](https://developer.android.com/reference/android/text/DynamicLayout.html)`中的范围被更新时，布局检查该范围是否是`UpdateLayout`范围，并为受影响的文本生成新的布局。

# 设置文本以获得最佳性能

根据您的需要，有几种节省内存的方式来设置`TextView`中的文本。

# 1.设置在`TextView`上的文本从不改变

如果您只在 TextView 上设置了一次文本，并且从未更新过它，那么您可以创建一个新的`SpannableString`或`SpannableStringBuilder`实例，设置所需的跨度，然后调用`[textView.setText(spannable)](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence))`。因为您不再处理文本，所以没有更多性能需要改进。

# 2.通过添加/移除跨度来更改文本样式

让我们考虑这样一种情况，文本没有改变，但是附加在它上面的跨度改变了。例如，假设每当点击一个按钮时，您希望文本中的一个单词变成灰色。所以，我们需要给文本添加一个新的跨度。要做到这一点，您很可能会尝试调用两次`[textView.setText(CharSequence)](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence))`:第一次是设置初始文本，第二次是在单击按钮时。一个更好的解决方案是调用`[textView.setText(CharSequence, BufferType)](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence,%20android.widget.TextView.BufferType))`并在点击按钮时更新`Spannable`对象的范围。

以下是这些选项背后的情况:

**选项 1:多次调用**[**textview . settext(char sequence)**](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence))**—次优**

当调用`[textView.setText(CharSequence)](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence))`时，`TextView`会创建一个你的`Spannable`的副本作为`SpannedString`，并将其作为`CharSequence`保存在内存中。这样做的结果是您的**文本和跨度是不可变的**。因此，当您需要更新文本样式时，您将不得不创建一个新的`Spannable`，带有文本和跨度，再次调用`textView.setText`，这将依次创建对象的新副本。

**选项二:调用**[**textview . settext(char sequence，BufferType)**](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence,%20android.widget.TextView.BufferType)) **一次，更新一个 Spannable 对象— optimal**

当调用`[textView.setText(CharSequence, BufferType)](https://developer.android.com/reference/android/widget/TextView.html#setText(java.lang.CharSequence,%20android.widget.TextView.BufferType))`时，`BufferType`参数告诉`TextView`设置了什么类型的文本:静态(调用`textView.setText(CharSequence)`时的默认类型)、可样式化/可扩展文本或可编辑文本(将由`EditText`使用)。

因为我们正在处理可以设置样式的文本，所以我们可以调用:

```
textView.setText(spannableObject, **BufferType.SPANNABLE**)
```

在这种情况下，`TextView`不再创建一个`SpannedString`，但是它将在`[Spannable.Factory](https://developer.android.com/reference/android/text/Spannable.Factory.html)`类型的成员对象的帮助下创建一个`SpannableString`。因此现在，`TextView`保存的`CharSequence`副本有**可变标记和**不可变文本。

为了更新跨度，我们首先获取文本作为`Spannable`，然后根据需要更新跨度。

```
// if setText was called with BufferType.SPANNABLE
textView.setText(spannable, BufferType.SPANNABLE)// the text can be cast to Spannable
val spannableText = textView.text as Spannable// now we can set or remove spans
spannableText.setSpan(
     ForegroundColorSpan(color), 
     8, spannableText.length, 
     SPAN_INCLUSIVE_INCLUSIVE)
```

使用这个选项，我们只创建初始的`Spannable`对象。`TextView`将保存它的副本，但是当我们需要修改它时，我们不需要创建任何其他对象，因为我们将直接处理由`TextView`保存的`Spannable`文本的实例。然而，`TextView`将仅被告知跨度的添加/移除/重新定位。如果您更改了 span 的一个内部属性，您将不得不调用`invalidate()`或`requestLayout()`，这取决于更改的类型。详见下面的“*奖金绩效提示*”。

# 3.文本更改(重用文本视图)

假设我们想要重用一个`TextView`并多次设置文本，就像在一个`RecyclerView.ViewHolder`中一样。默认情况下，独立于`BufferType`集合，`TextView`创建一个`CharSequence`对象的副本并保存在内存中。这确保了当开发人员出于其他原因更改`CharSequence`值时，所有的`TextView`更新都是有意的，而不是偶然的。

在上面的选项 2 中，我们看到当通过`textView.setText(spannableObject, BufferType.SPANNABLE)`设置文本时，`TextView`通过使用`[Spannable.Factory](https://developer.android.com/reference/android/text/Spannable.Factory.html)`实例创建一个新的`SpannableString`来复制`CharSequence`。所以每次我们设置一个新的文本，它就会创建一个新的对象。如果您想对这个过程进行更多的控制，并避免额外的对象创建，请实现您自己的`Spannable.Factory`，覆盖[newSpannable(char sequence)](https://developer.android.com/reference/android/text/Spannable.Factory.html#newSpannable(java.lang.CharSequence))，并将工厂设置为`TextView`。

在我们自己的实现中，我们希望避免创建新的对象，所以我们可以将`CharSequence`转换为`Spannable`。请记住，为了做到这一点，您必须调用`textView.setText(spannableObject, BufferType.SPANNABLE)`，否则，源`CharSequence`将是`Spanned`的一个实例，它不能被强制转换为`Spannable`，从而产生一个`ClassCastException`。

```
val spannableFactory = object : Spannable.Factory() {
    override fun newSpannable(source: CharSequence?): Spannable {
        return source **as Spannable** }
}
```

设置**扳手。在你得到对你的`TextView`的引用后，工厂**对象一旦出现。如果你使用的是`RecyclerView`，那么在你第一次放大你的视图时就这样做。

```
[textView.setSpannableFactory](https://developer.android.com/reference/android/widget/TextView.html#setSpannableFactory(android.text.Spannable.Factory))(spannableFactory)
```

这样，您就可以避免每次您的`RecyclerView`向您的`ViewHolder`绑定一个新项目时创建额外的对象。

为了在处理文本和`RecyclerViews`时获得更好的性能，不要从`ViewHolder`中的`String`中创建`Spannable`对象，而是在将列表传递给 `**Adapter**`之前创建**。这允许您在后台线程上构造`Spannable`对象，以及您对列表元素所做的任何其他工作。然后你的`Adapter`可以保存一个对`List<Spannable>`的引用。**

# 额外绩效提示

如果你只需要改变一个跨度的内部属性(例如，一个自定义项目符号跨度的项目符号颜色)，你不需要再次调用`TextView.setText`，只需要调用`invalidate()`或者`requestLayout()`。再次调用`setText`会导致不必要的逻辑被触发和对象被创建，当视图需要重新绘制或重新测量时。

您需要做的是保存对可变跨度的引用，并根据您在视图中更改的属性类型，调用:

*   `**TextView.invalidate()**` 如果你只是改变**文本外观**，触发**重绘**并跳过重做布局。
*   `**TextView.requestLayout()**`如果你做了一个改变，使**影响到文字的尺寸**，那么视图就可以照顾到**的测量、排版和绘图**。

假设您有自己的自定义项目符号实现，其中默认的项目符号颜色是红色。每当您按下一个按钮时，您都希望将项目符号的颜色更改为灰色。实现如下所示:

```
class MainActivity : AppCompatActivity() {
    // keeping the span as a field
    val bulletSpan = BulletPointSpan(color = Color.*RED*) override fun onCreate(savedInstanceState: Bundle?) {
        …
        val spannable = SpannableString(“Text is spantastic”)
        // setting the span to the bulletSpan field
        spannable.setSpan(
            bulletSpan, 
            0, 4, 
            Spanned.*SPAN_INCLUSIVE_INCLUSIVE*)
        styledText.setText(spannable)
        button.setOnClickListener( {// change the color of our mutable span
            bulletSpan.*color* = Color.*GRAY* // color won’t be changed until invalidate is called
            **styledText.invalidate()**
        }}
```

# 幕后:在进程内和进程间传递文本

*TL；博士:*

*当跨越对象在进程内或进程间传递时，将不使用自定义跨越属性。如果仅仅使用框架跨度就可以实现想要的样式，那么* ***更喜欢应用多个框架跨度*** *来实现自己的跨度。否则，最好实现扩展一些基本接口或抽象类的自定义范围。*

在 Android 中，文本可以在同一进程中传递(进程内)，例如通过意图从一个活动传递到另一个活动，以及当文本从一个应用程序复制到另一个应用程序时在进程之间传递(进程间)。

自定义 span 实现不能跨越流程边界，因为其他流程不知道它们，也不知道如何处理它们。Android 框架跨度是全局对象，但是**只有从** `**ParcelableSpan**` **延伸的跨度可以在进程内和进程间传递。**该功能允许打包和解包框架中定义的范围的所有属性。`[TextUtils.writeToParcel](https://developer.android.com/reference/android/text/TextUtils.html#writeToParcel(java.lang.CharSequence,%20android.os.Parcel,%20int))`方法负责将跨度信息保存在`Parcel`中。

例如，您可以在同一个流程中通过一个意图在`Activities`之间传递跨度:

```
// start Activity with text with spans
val intent = Intent(this, MainActivity::class.java)
intent.putExtra(TEXT_EXTRA, mySpannableString)
startActivity(intent)// read text with Spans
val intentCharSequence = intent.getCharSequenceExtra(TEXT_EXTRA)
```

> 所以，即使你在同一个过程中传递跨度，只有框架`ParcelableSpans`能够通过意图传递。

`ParcelableSpans`还允许从一个流程到另一个流程复制文本和跨度。复制和粘贴文本的过程通过`ClipboardService`进行，它在幕后使用相同的`TextUtil.writeToParcel`方法。因此，即使您从您的应用程序中复制跨度并将其粘贴到同一个应用程序中，这也是一个进程间操作，需要打包，因为文本要经过`ClipboardService`。

默认情况下，任何实现`Parcelable`的类都可以从`Parcel`编写和恢复。当在进程间传递一个`Parcelable`对象时，唯一能保证被正确恢复的类是框架类。如果试图从`Parcel`恢复数据的进程由于数据类型在不同的应用程序中定义而无法构建对象，那么进程将崩溃。

这里有两大警告:

1.  当带有 span 的文本被传递时，无论是在同一个进程中还是在进程间，**只有框架的** `**ParcelableSpans**` **引用被保留**。因此，自定义跨度样式不会传播。
2.  **不能自己创建** `**ParcelableSpan**` **。**为了避免未知数据类型导致的崩溃，框架不允许实现自定义的`ParcelableSpan`，通过定义两个方法`[getSpanTypeIdInternal](https://github.com/aosp-mirror/platform_frameworks_base/blob/master/core/java/android/text/ParcelableSpan.java#L39)`和`[writeToParcelInternal](https://github.com/aosp-mirror/platform_frameworks_base/blob/master/core/java/android/text/ParcelableSpan.java#L47)`为隐藏的。两个都是`TextUtils.writeToParcel`用的。

假设您想要定义一个允许自定义项目符号大小的项目符号跨度，因为现有的`BulletSpan`定义了 4px 的固定半径大小。以下是如何实现它以及每种方法的后果:

1.  **创建一个** `**CustomBulletSpan**` **，它扩展了** `**BulletSpan**`，但也允许设置子弹大小的参数。当跨度从一个活动传递到另一个活动或通过复制文本时，附加到文本的跨度将是`**BulletSpan**`。这意味着当文本被绘制时，它将具有框架的默认项目符号半径，而不是在`CustomBulletSpan`中设置的半径。
2.  **创建一个** `**CustomBulletSpan**` **，它只是从** `**LeadingMarginSpan**`扩展而来，并重新实现了项目符号功能。当跨度从一个`Activity`传递到另一个`Activity`或通过复制文本时，附加到文本的跨度将是`LeadingMarginSpan`。这意味着当文本被绘制时，它将失去所有的样式。

如果仅仅使用框架跨度就可以实现想要的样式，那么最好应用多个框架跨度来实现您自己的框架跨度。否则，最好实现扩展一些基本接口或抽象类的自定义范围。这样，当对象在进程内或进程间传递时，可以避免框架的实现被应用到 spannable。

通过了解 Android 如何使用 spans 渲染文本，希望你可以在你的应用程序中有效地使用它。下一次您需要样式化文本时，决定您是否应该应用多个框架范围或者创建您自己的自定义范围，这取决于您对该文本所做的进一步工作。

在 Android 中处理文本是如此常见的任务，以至于调用正确的`TextView.setText`方法可以帮助你减少应用程序的内存使用并提高其性能。

> *多多感谢* [*斯亚梅德*](https://twitter.com/siyamed) *，克拉拉【巴亚里】 [*尼克*](https://medium.com/u/22c02a30ae04?source=post_page-----1b91008b97e4--------------------------------) *。**