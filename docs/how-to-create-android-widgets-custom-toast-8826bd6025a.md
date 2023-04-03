# 如何创建 Android 小部件:自定义吐司

> 原文：<https://medium.com/edureka/how-to-create-android-widgets-custom-toast-8826bd6025a?source=collection_archive---------0----------------------->

![](img/282832fa9161a6d2ea8ddf8390f03bca.png)

在这篇“如何创建 Android 小部件”的文章中，我们将帮助你学习如何定制一个祝酒词。

![](img/63ef831805a9de6a493475d6d79deb65.png)

你对总是用同一个吐司感到厌烦吗？

这篇文章将告诉你如何创建自己的定制吐司。很酷不是吗？；)

使用定制吐司，您可以真正重新定义吐司的外观。你甚至可以在你的吐司里放上图片。在上面的图片中，文字“没有互联网连接”旁边的图片是为了让这个吐司看起来更好。改变文本的颜色和大小非常容易。所以让我们开始吧！

# 如何创建 Android Widgets:

## 自定义吐司:

创建一个 XML 文件，将所有想要显示在 Toast 中的 android 小部件拖放到其中。

重要的关键点:

*   布局 id 是必需的，因为它将在活动中使用。
*   布局的宽度和高度应该设置为“match_parent ”,以便覆盖整个 Toast。

# 创建一个名为 custom_toast.xml 的 xml 文件:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="[http://schemas.android.com/apk/res/android](http://schemas.android.com/apk/res/android)" android:id="@+id/toast_custom" android:layout_width="match_parent" android:layout_height="match_parent" android:background="[@drawable/custom_textview](http://twitter.com/drawable/custom_textview)" >

    <ImageView android:layout_width="match_parent" android:layout_height="match_parent" android:layout_margin="4dp" android:contentDescription="[@string/android](http://twitter.com/string/android)" android:src="[@drawable/img2](http://twitter.com/drawable/img2)" />

    <TextView android:id="@+id/tvtoast" android:layout_width="match_parent" android:layout_height="match_parent" android:paddingRight="6dp" android:paddingTop="6dp" />

</LinearLayout>
```

创建另一个 XML 文件来设置文本视图的外观，该文本视图将在上面的布局中用作背景。这个文件应该放在一个可提取的文件夹里。

# 创建另一个名为 custom_textview.xml 的 xml 文件:

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="[http://schemas.android.com/apk/res/android](http://schemas.android.com/apk/res/android)" android:shape="rectangle" >

    <solid android:color="#98E8F9" />

    <stroke android:width="1dip" android:color="#4fa5d5" />

    <corners android:radius="4dp" />

</shape>
```

# 在 onCreate()方法中:

我们创建的自定义布局在这个方法中是膨胀的，使用它我们可以创建自定义的 Toast。

```
// Inflating the layout for the toast
LayoutInflater inflater = getLayoutInflater();
View layout = inflater.inflate(R.layout.custom_toast,
        (ViewGroup) findViewById(R.id.toast_custom));

// Typecasting and finding the view in the inflated layout
TextView text = (TextView) layout.findViewById(R.id.tvtoast);

// Setting the text to be displayed in the Toast
text.setText("No internet connection");

// Setting the color of the Text to be displayed in the toast
text.setTextColor(Color.rgb(0, 132, 219));

// Creating the Toast
Toast toast = new Toast(getApplicationContext());

// Setting the position of the Toast to centre
toast.setGravity(Gravity.CENTER_VERTICAL, 0, 0);

// Setting the duration of the Toast
toast.setDuration(Toast.LENGTH_LONG);

// Setting the Inflated Layout to the Toast
toast.setView(layout);

// Showing the Toast
toast.show();
```

[dl URL = " # " class = ' e model e model-11 ' title = "下载代码" desc = " " type = " " align = " " for = " Download "]

我们希望，这个 Android 适配器教程对你有用！敬请关注 Android 中的更多教程！快乐学习！

如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=what-are-adapters-in-android/)

请留意本系列中解释 Android 其他各方面的其他文章。

> [*如何使用 Kotlin 开发 Android App？*](/edureka/kotlin-android-tutorial-cea896d0ae18)
> 
> [*如何与科特林土著合作？*](/edureka/basic-kotlin-native-app-a22febf0f6d7)

*原载于 2020 年 10 月 25 日*https://www.edureka.co