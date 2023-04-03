# 不再有 findViewById

> 原文：<https://medium.com/androiddevelopers/no-more-findviewbyid-457457644885?source=collection_archive---------0----------------------->

使用 Android Studio 开发 Android 应用程序的一个鲜为人知的特性是数据绑定。它带来了许多优秀的特性，我将在以后的文章中介绍，但最基本的是消除了 findViewById。

这不就是个讨厌的家伙吗？

```
TextView hello = (TextView) findViewById(R.id.hello);
```

有一些工具的主要工作是消除这一小部分代码，但现在 Android Studio 1.5 和更高版本有了一个官方的方法。

首先，您必须编辑应用程序的 build.gradle 文件，并将以下内容添加到 android 块中:

```
android {
    …
 ***dataBinding.enabled = true*** }
```

下一件事是通过使用外部标签<layout>而不是您使用的任何视图组来更改布局文件:</layout>

```
<**layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"**>
    <**RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin"
            tools:context=".MainActivity"**>

        <**TextView
                android:id="@+id/hello"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"**/>

    </**RelativeLayout**>
</**layout**>
```

**布局**标签告诉 Android Studio，这个布局应该在编译时进行额外的处理，以找到所有有趣的视图，并为下一步做好记录。所有没有外部**布局**标签的布局都不会得到额外的处理步骤，所以你可以把这些添加到你喜欢的新项目中，而不用改变应用程序的其他部分。

接下来你要做的就是告诉它在运行时以不同的方式加载布局文件。因为这可以追溯到 Eclaire 版本，所以加载这些预处理布局文件不依赖于新的框架更改。因此，你必须对你的装载程序做一点小小的改变。

从活动中，而不是:

```
setContentView(R.layout.hello_world);
TextView hello = (TextView) findViewById(R.id.hello);
hello.setText("Hello World"); // for example, but you'd use
                              // resources, right?
```

你这样加载它:

```
HelloWorldBinding binding = 
    DataBindingUtil.*setContentView*(**this**, R.layout.***hello_world***);
binding.hello.setText("Hello World"); // you should use resources!
```

在这里，您可以看到为 **hello_world.xml** 布局文件生成了一个类 **HelloWorldBinding** ，ID 为“@+id/hello”的视图被分配给一个您可以使用的最终字段 **hello** 。没有 casting，就没有 findViewById。

事实证明，这种访问视图的机制不仅比 findViewById 容易得多，而且还可以更快！绑定过程对布局中的所有视图进行一次遍历，以将视图分配给字段。运行 findViewById 时，每次都会遍历视图层次结构以找到它。

您将看到的一件事是，它改变了您的变量名(就像 hello_world.xml 成为 HelloWorldBinding 类一样)，所以如果您给它一个 ID“@+ID/hello _ text”，那么字段名将是 **helloText** 。

当您为 RecyclerView、ViewPager 或其他不设置活动内容的内容展开布局时，您会希望在生成的类上使用生成的类型安全方法。有几个版本与 LayoutInflater 相匹配，因此请使用最适合您的版本。例如:

```
HelloWorldBinding binding = HelloWorldBinding.inflate(
    getLayoutInflater(), container, attachToContainer);
```

如果您没有将展开的视图附加到包含视图组，您将必须访问展开的视图层次结构。您可以通过绑定的 getRoot()方法实现这一点:

```
linearLayout.addView(binding.getRoot());
```

现在，你可能想知道，如果我有一个布局，它有不同的配置和不同的视图呢？布局预处理和运行时膨胀阶段通过将所有视图 id 添加到生成的类中来处理这个问题，如果它们不在膨胀的布局中，就将它们设置为 null。

很神奇，是吧？最棒的是，在运行时没有使用反射或任何其他高成本的技术。很容易将这一点加入到你当前的应用程序中，让你的生活变得简单一点，你的布局可以加载得更快一点。