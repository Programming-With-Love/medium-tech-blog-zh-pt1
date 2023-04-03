# Android 数据绑定系列

> 原文：<https://medium.com/globant/android-databinding-series-84a90e706a35?source=collection_archive---------3----------------------->

![](img/be086d8d4b1d1399f5b25d36021ce930.png)

Android Databinding

我们已经经历了太多的 android 架构变化。首先，我们有 MVC，它把所有的控制器逻辑放在活动文件中。然后，我们想出了一个拥有 MVP 的好主意，按照 SOLID 原则将所有逻辑分成单独的文件，因此我们将业务逻辑放在 presenter 中，并使用接口将数据传递给 UI。出于某种原因，我们需要一个自动发生的反应，所以我们不需要每次都通知屏幕，我们想出了 MVVM 和数据绑定。还有更多的架构，如 BOB 叔叔模式和 viper 等。但是现在让我们专注于数据绑定。

首先，我们将从基础开始，了解它是如何工作的，然后我们如何通过让注释处理器生成代码来更快更好地开发应用程序。

请密切关注粗体项目，因为我们将使用它作为参考。

@copyrights with [https://giphy.com](https://giphy.com/)

1.  **设置**

```
apply plugin: 'kotlin-kapt' //If you have kotlin configured
android {
    dataBinding {
        enabled = true
    }
}
```

2.让我们创建一个模型

```
data class User(
    var age: String = "0",
    var property: String = "property"
)
```

这里我们有一个用户类，它有年龄和属性变量。我们将使用 XML 中的变量

3.对布局文件的更改

```
<?xml version="1.0" encoding="utf-8"?>
**<layout** xmlns:android="http://schemas.android.com/apk/res/android">

    **<data**>
        **<variable**
            name="user"
            type="com.parthdave93.databindingdemo.User" />
    **</data**>
    <androidx.constraintlayout.widget.ConstraintLayout
        .. name space and all...>

        <TextView
            android:id="@+id/tvAge"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            **android:text='@{user.age}'**
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <TextView
            android:id="@+id/tvProperty"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            **android:text='@{user.property}'**
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>
**</layout**>
```

注释处理器的工作方式是检查 **<布局**标签，如果任何文件包含带有 **<数据**标签的布局，这意味着这是数据绑定布局，它需要生成对在屏幕上设置数据很重要的文件。
这里我们使用了两个文本视图来显示我们之前创建的用户模型中的年龄和属性。
**< data** 标签包含所有你想要的变量和导入，以便在屏幕上设置数据。

**@{}** 用于单向数据绑定意味着视图/布局本身不会改变数据，但会改变代码。我们使用 **@={}** 让视图/布局像 edittext 一样改变对象的数据，如果用户在编辑文本中输入，我们不需要从编辑文本中获取细节并手动设置它，这可以由生成的代码在内部完成。

4.活动类的变化

```
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        **val binding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.*activity_main*)**

        val user = User() // Initialise Data class object as needed

        binding.*user* = user
    }
}
```

因此 **DataBindingUtil** 将设置该活动的内容，我们将获得绑定对象，我们将设置布局文件中声明的用户变量。屏幕将会更新。

@copyrights with [https://giphy.com](https://giphy.com/)

让我们深入了解它在幕后是如何运作的…

当我们构建项目时，数据绑定的注释处理器将运行并生成 **ActivityMainBinding** 类的**ActivityMainBinding impl**实现。(注释处理器如何工作是一个不同的话题，但作为参考，您可以查看我在**[**链接**](https://github.com/parthdave93/DemoAptProcessor) 上的旧回购)**

**剖析类文件是用绑定作为后缀颠倒布局。我们有 activity_main 所以 ActivityMainBinding。你可以在 android studio 中通过快速搜索双转移(或者其他快捷键)找到这个类。**

**于是就有了两个类一个 **ActivityMainBinding (** 类 ActivityMainBindingImpl 扩展 ActivityMainBinding **)** 和 **ActivityMainBindingImpl (** 抽象类 ActivityMainBinding 扩展 ViewDataBinding **)。****

```
val binding = DataBindingUtil.setContentView**<ActivityMainBinding>**(this, R.layout.*activity_main*)
```

**当我们编写上述代码行时，Databinding util 的 setContentView 方法将被调用，它最终将调用 bind 方法。**

```
static <T extends **ViewDataBinding**> T bind(DataBindingComponent bindingComponent, View root,
        int layoutId) {
    return **(T) *sMapper*.getDataBinder(bindingComponent, root, layoutId)**;
}
```

**看看 ***sMapper* 。getDataBinder** sMapper 基本上是一个映射器，它将布局 id 映射到代码生成的实现。**

**还记得我们在 DataBindingUtil 中使用 setContentView 方法时，如何使用 layout id(因为 R.layout.activity_main 是一个整数)知道它需要将哪个绑定对象返回给我们吗？这个**映射器**负责处理它。**

```
private static DataBinderMapper *sMapper* = new DataBinderMapperImpl();addMapper(new com.parthdave93.databindingdemo.**DataBinderMapperImpl**());
```

**那个 **DataBinderMapperImpl** 类拥有将布局 id 映射到绑定实现的所有逻辑。**

```
@Override
public ViewDataBinding getDataBinder(DataBindingComponent component, View view, int layoutId) {
  ...
    switch(localizedLayoutId) {
      case  ***LAYOUT_ACTIVITYMAIN*:** {
        if ("layout/activity_main_0".equals(tag)) {
          return new **ActivityMainBindingImpl**(component, view);
        }
        throw new IllegalArgumentException("The tag for activity_main is invalid. Received: " + tag);
      }
    }
  }
  return null;
}
```

**现在我们有了 ActivityMainBindingImpl，它将在布局文件中声明变量，如用户变量。**

```
binding.*user* = user//internal method of ActivityMainBindingImpl
public void **setUser**(@Nullable com.parthdave93.databindingdemo.User User) {
    this.mUser = User;
    synchronized(this) {
        mDirtyFlags |= 0x1L;
    }
    notifyPropertyChanged(BR.*user*);
    super.requestRebind();
}
```

**一旦我们使用它，该类将更新 dirtyFlags 并请求重新绑定，这将最终调用执行挂起的绑定并在屏幕上更新视图。**

**到目前为止，我们在布局中设置用户变量，当我们以异步方式设置用户时，绑定将更新 textview。假设在设置了用户之后，我们改变了用户对象变量，比如属性，视图不会更新，为什么？**

**值得注意的是，它不是反应式的，它是异步的，或者我应该说它会等待，当 setUser 方法被调用时，它会更新那些视图。当我们改变一个对象的属性时，我们没有设置更新 UI 的逻辑。**

**为此，让我们稍微修改一下模型:**

```
import androidx.databinding.BaseObservable
import androidx.databinding.Bindable
//BaseObservable classclass User: **BaseObservable**() {
    @**get:Bindable**
    var age: String = "0"

    @get:Bindable
    var property: String = "property"
    **set**(value) {
        field = value
        notifyPropertyChanged(BR.property)
    }
}
```

**这里，我们用基本可观察对象扩展了类，当我们更新属性时，我们用该属性的整数通知基本可观察对象，只有当我们用 Bindable(@**get:Bindable**)注释属性时才会生成该属性。**

**因此，让我们来看看这些变化是如何改变 activityMainBindingImpl 中的 setUser 方法的。**

```
public void setUser(@Nullable com.parthdave93.databindingdemo.User User) {
    **updateRegistration(0, User);**
    this.mUser = User;
    synchronized(this) {
        mDirtyFlags |= 0x1L;
    }
    notifyPropertyChanged(BR.*user*);
    super.requestRebind();
}
```

**当我们调用 notifyPropertyChanged 方法时，更新注册方法使用 weakObservables 来监听用户类的更改，因此更新会自动推送到屏幕上。**

**让我们来看案例 2，我们的用户类已经被 Person 类扩展了，我们需要数据绑定类 BaseObservable 来扩展，在这种情况下我们应该怎么做？**

**此时，我们需要通过实现一个*可观察的*接口来编写更多的代码来做同样的事情。**

```
import androidx.databinding.Bindable
import androidx.databinding.Observable
import androidx.databinding.PropertyChangeRegistry

//Observable interface
class User: Person(), Observable {

    private val registry = **PropertyChangeRegistry**()

    override fun **removeOnPropertyChangedCallback**(callback: Observable.OnPropertyChangedCallback?) {
        registry.remove(callback)
    }

    override fun **addOnPropertyChangedCallback**(callback: Observable.OnPropertyChangedCallback?) {
        registry.add(callback)
    }

    @get:Bindable
    var age: String = "0"

    @get:Bindable
    var property: String = "property"
    set(value) {
        field= value
        **registry.notifyChange(this, BR.property)**
    }
}
```

**PropertyChangeRegistry 是用于 PropertyChangeCallbacks 通知更改的类。好了**

**@copyrights with [https://giphy.com](https://giphy.com/)**

****TL'DR****

**数据绑定帮助我们利用注释处理器的能力，比以往任何时候都更快地构建 UI。我们已经看了在幕后生成的类以及它是如何工作的。有许多其他的东西可以帮助我们超越常规的适配器和类文件生成，这将在下一篇文章中讨论。**

**感谢阅读，我希望你喜欢它。如果你有什么建议或者我写错了什么，我很乐意修改它，请评论。**