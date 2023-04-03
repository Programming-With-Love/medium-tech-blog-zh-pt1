# Android 数据绑定:那个<include>东西</include>

> 原文：<https://medium.com/androiddevelopers/android-data-binding-that-include-thing-1c8791dd6038?source=collection_archive---------1----------------------->

## 当布局<include>其他 <layout>s</layout></include>

在[的上一篇文章](/google-developers/no-more-findviewbyid-457457644885#.fvkldhrbx)中，您看到了在 Android Studio 1.5 和更高版本中避免使用 findViewById 是多么容易。这基本上是视图固定器模式，如下所述:

 [## 使 ListView 滚动平滑

### 平滑滚动 ListView 的关键是保持应用程序的主线程(UI 线程)不被繁重的…

developer.android.com](https://developer.android.com/training/improving-layouts/smooth-scrolling.html) 

我展示了如何使用 Android Studio 生成一个类，作为单个布局文件的视图容器，但是包含的布局呢？合并的包含布局呢？

原来那些也是支持的，但是每个布局文件生成一个不同的类。这里有一个例子:

```
hello_world.xml<**layout xmlns:android="http://schemas.android.com/apk/res/android"**>
    <**LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"**>

        <**TextView
                android:id="@+id/hello"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"**/>
        <**include
                android:id="@+id/included"
                layout="@layout/included_layout"**/>
    </**LinearLayout**>
</**layout**>included_layout.xml*<?***xml version="1.0" encoding="utf-8"***?>* <**layout xmlns:android="http://schemas.android.com/apk/res/android"**>
    <**TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:id="@+id/world"**/>
</**layout**>
```

您可以通过以下方式访问两种不同的文本视图:

```
HelloWorldBinding binding =
    HelloWorldBinding.inflate(getLayoutInflater());
binding.**hello**.setText(**“Hello”**);
binding.**included**.**world**.setText(**“World”**);
```

包含文件的模式遵循与视图相同的模式:<include>标签的 ID 被用作它在类中的字段名。包含的布局已经为其布局中的视图生成了自己的类和自己的字段。不同布局之间共享的任何 id 都可以由开发人员轻松区分。例如，如果您两次包含相同的布局:</include>

```
<**layout xmlns:android="http://schemas.android.com/apk/res/android"**>
    <**LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"**>

        <**TextView
                android:id="@+id/hello"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"**/>
        <**include
                android:id="@+id/world1"
                layout="@layout/included_layout"**/>
        <**include
                android:id="@+id/world2"
                layout="@layout/included_layout"**/>
    </**LinearLayout**>
</**layout**>
```

可以轻松访问两个“世界”文本视图:

```
HelloWorldBinding binding =
    HelloWorldBinding.inflate(getLayoutInflater());
binding.**hello**.setText(**“Hello”**);
binding.**world1**.**world**.setText(**“First World”**);
binding.**world2**.**world**.setText(**“Second World”**);
```

记得给 include 标签一个 ID，否则不会给它任何公共字段。另外，记住使用外部的<layout>来显示您想要生成字段的布局。这触发了预处理步骤，允许它将视图与字段相关联并生成一个类。</layout>

如果您查看生成的类，您会发现它们在被包含的任何时候都使用相同的类。例如，如果您有一个包含 included_layout.xml 的 goodbye_world.xml 类，那么只为它生成一个类。