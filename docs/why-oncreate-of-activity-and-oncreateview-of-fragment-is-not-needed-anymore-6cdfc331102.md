# 为什么不再需要 Activity 的 OnCreate()和 Fragment 的 OnCreateView()

> 原文：<https://medium.com/globant/why-oncreate-of-activity-and-oncreateview-of-fragment-is-not-needed-anymore-6cdfc331102?source=collection_archive---------0----------------------->

![](img/2bc2c0be2e4f1081b790d4d8d6817653.png)

从我们开始用 android 编程的那一天起，我们就一直在使用`setContentView`来扩展 Activity 类中的布局。当片段被引入时，我们不得不覆盖`onCreateView`并使用一个布局生成器从一个布局 ID 获取我们的视图。他们感觉有点像样板，对不对？这是解决问题的方法

# 活动

如果我们使用 OnCreate()方法只是为了扩大视图，那么我们可以删除活动的 OnCreate()方法。

**以前的**:

```
class MainActivity : AppCompatActivity() { override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.*activity_main*)
    }   
}
```

**现在来自 AndroidX :**

我们可以使用将布局作为参数的构造函数。首先，我们需要定义应用程序`build.gradle`文件的依赖关系。

```
implementation 'androidx.appcompat:appcompat:1.1.0'
```

然后在活动课上。

```
class MainActivity : AppCompatActivity(R.layout.*activity_main*) {}
```

**注意**:我们可以通过 Kotlin android 扩展访问视图。

Tada，布局膨胀了。

# 碎片

**以前的**:

调用 OnCreateView()来放大视图。

```
class MyFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?, savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.*my_fragment*, container, false)
    } override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        val textView = view.findViewById(R.id.*tv*) as TextView
        textView.*text* = "myText";
    }
}
```

**注意** : `onViewCreated`只有当 onCreateView()返回的视图非空时才会被调用。

**现在来自 AndroidX :**

我们可以使用将 layout 作为参数的构造函数，并消除 OnCreateView()。

首先，我们需要定义应用程序`build.gradle`文件的依赖关系。

```
implementation 'androidx.appcompat:appcompat:1.1.0'
```

然后在片段类里。

```
class MyFragment : Fragment(R.layout.*my_fragment*) {

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        val textView = view.findViewById(R.id.*tv*) as TextView
        textView.*text* = "myText";
    }
}
```

嘭，版面膨胀了。

所以去掉这些生命周期方法，保持代码干净🙂