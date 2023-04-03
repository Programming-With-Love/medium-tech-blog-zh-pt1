# Android 数据绑定:自定义设置器

> 原文：<https://medium.com/androiddevelopers/android-data-binding-custom-setters-55a25a7aea47?source=collection_archive---------0----------------------->

![](img/4f5d0f7eb05b7415969fc0928287006f.png)

## 让数据绑定做你想做的

我希望那些读过我以前关于 Android 数据绑定的文章的人一直在玩它。您可能已经将 android:text 绑定到 TextViews，将 android:checked 绑定到复选框，甚至可能尝试过在 EditText 上使用 android:text 进行双向数据绑定。内置属性被很好地覆盖了，并且合成了新的标签来绑定到事件。然而，这对自定义视图没有帮助。还有，怎么自定义设置逻辑？

## 绑定到 Setters

我的应用程序有一个自定义视图，颜色选择器:

```
**public class** ColorPicker **extends** View {
    **private int color**;

    **public void** setColor(**int** color) {
        **this**.**color** = color;
        invalidate();
    }

    **public int** getColor() {
        **return color**;
    } **public void** addListener(OnColorChangeListener listener) {
        //...
    } **public void** removeListener(OnColorChangeListener listener) {
        //...
    } //...
}
```

我想用数据绑定来设置颜色。事实证明，这种类型的设置器不需要任何代码。数据绑定系统自动寻找与数据绑定属性同名的 setter。

```
<**com.example.myapp.ColorPicker
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    *app:color="@{color}"***/>
```

## 绑定适配器

有时我们想做一些比简单地在视图上调用 setter 更复杂的事情。我最喜欢的例子是从 UI 线程中加载图像。首先，我需要选择一个自定义属性来将图像分配给 ImageView。

```
<**ImageView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    *app:imageUrl="@{product.imageUrl}"***/>
```

如果我什么都不做，数据绑定系统会在 ImageView 上寻找一个“setImageUrl(String)”而找不到。我必须创建一种方法来设置“app:imageUrl”属性。绑定适配器是任何类中用来做这件事的带注释的方法。通常，您会根据目标视图类型将适配器组织到一个类中。

我不打算推广任何特定的图像库，所以让我们为我们的绑定适配器假设一个通用的图像库:

```
@BindingAdapter(**"imageUrl"**)
**public static void** setImageUrl(ImageView imageView, String url) {
    **if** (url == **null**) {
        imageView.setImageDrawable(**null**);
    } **else** {
        MyImageLoader.loadInto(imageView, url);
    }
}
```

BindingAdapter 注释将属性名作为其参数。应用程序名称空间中的任何内容都不需要参数中的任何名称空间，但是对于 android 名称空间中的属性，您必须给出完整的属性名称，包括“android”

第一个方法参数是目标视图的类型。第二个是设置给属性的值。

MyImageLoader 库将从 UI 线程中加载图像，并在完成后将其设置到 ImageView 中。虽然 UI 线程在图像加载时不再挂起，但是 ImageView 在加载期间仍然是空的，我真的希望在那里有一个占位符。

```
<**ImageView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    *app:imageUrl="@{product.imageUrl}"*
    *app:placeholder="@{@drawable/shadowAvatar}"***/>
```

现在，这两种属性相互作用。如果已经加载了产品图像，我不希望显示占位符图像。当图像加载时，我希望加载程序能够交叉淡入淡出图像。因此，我们的绑定适配器必须处理多个属性:

```
@BindingAdapter(value={**"imageUrl", "placeholder"},** requireAll=**false**)
**public static void** setImageUrl(ImageView imageView, String url,
        Drawable placeHolder) {
    **if** (url == **null**) {
        imageView.setImageDrawable(placeholder);
    } **else** {
        MyImageLoader.loadInto(imageView, url, placeholder);
    }
}
```

当处理多个属性时，属性必须放在一个列表中。该方法的参数与*值*的顺序相同。我还将 *requireAll* 设置为 false。如果我将其保留为默认值 true，那么绑定适配器将要求调用 URL 和占位符。当 *requireAll* 设置为 false 时，如果没有设置 URL 或占位符值，仍然可以使用未设置属性的默认值(在本例中为 null)调用绑定适配器。

在这个绑定适配器中，MyImageLoader 可以完成它需要的所有功能，使用 URL 和占位符图像对 ImageView 进行操作。

## 设置事件处理程序

在设置事件处理程序时，简单的 setter 非常简单——只需使用与 setter 方法相同的属性名称。通常，您希望能够添加任意数量的侦听器。在这种情况下，您有“添加”和“移除”方法，而不是“设置”方法。

我的 ColorPicker 就有这种用于“onColorChange”事件的监听器。最简单的方法是删除旧的侦听器，然后添加新的侦听器。幸运的是，数据绑定为我们提供了确切的功能。为了检索旧值，绑定适配器方法应该为每个属性采用两个参数，首先是所有旧值，然后是所有新值:

```
@BindingAdapter(**"onColorChange"**)
**public static void** setColorChangeListener(ColorPicker view, 
        OnColorChangeListener oldListener, 
        OnColorChangeListener newListener) {
   **if** (oldListener != **null**) {
        view.removeListener(oldListener);
   } **if** (newListener != **null**) {
       view.addListener(newListener);
   }
}
```

您会看到旧值对于这个绑定适配器是多么有用。旧值不仅适用于事件侦听器，这是最常见的用法。任何时候您不能从视图中检索旧值并且需要它，只需为旧值添加参数。

我们的监听器是这样声明的:

```
**public interface** OnColorChangeListener {
    **void** onColorChange(ColorPicker colorPicker, **int** newColor);
}
```

因为 OnColorChangeListener 只有一个抽象方法，所以数据绑定框架可以知道如何处理 lambda 方法和方法引用:

```
<**com.example.myapp.ColorPicker
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    *app:onColorChange="@{(v, color)->handler.colorChanged(color)}"
*    app:color="@{color}"**/>
```

它自动将抽象方法(在本例中是 onColorChange)与声明的 lambda 表达式关联起来。这都是在编译时完成的，所以在应用程序中没有任何反射。

将事件属性命名为与方法名称相同的名称，以防止开发人员混淆。

## 结论

Android 数据绑定在大多数情况下不需要任何代码就能完成您想要的工作——只需调用视图上的 setter。当您想要更复杂的东西时，您还可以使用绑定适配器根据需要进行调整。

人们已经提出了绑定适配器的一些伟大而有趣的用途。例如， [Lisa Wray 使用绑定适配器来设置自定义字体](https://plus.google.com/+LisaWrayZeitouni/posts/LTr5tX5M9mb)。您还可以将值从一种状态变为另一种状态。也许你能想出一些很棒的用途。如果你想到一些聪明的东西，请张贴出来，让我们知道。