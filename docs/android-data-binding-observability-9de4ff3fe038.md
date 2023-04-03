# Android 数据绑定:可观察性

> 原文：<https://medium.com/androiddevelopers/android-data-binding-observability-9de4ff3fe038?source=collection_archive---------0----------------------->

![](img/983f4b7717fe7ccf5a457e769de51aa0.png)

## 保持用户界面最新

Android 数据绑定是将数据插入 UI 的一种非常简单的方法。到目前为止，[我只展示了如何使用普通 java 对象(POJO)](/google-developers/android-data-binding-adding-some-variability-1fe001b3abcc#.hrbxwmacc)来实现这一点。然而，当数据更新时，没有更新 UI 的通知。当服务器发送了一个更新，而你想让用户看到这个变化时，这就不好了。Android 数据绑定为您提供了几种方法来保持数据与 UI 同步。

## 波乔

我有一个普通的老式 Java 对象“Foo ”,有一个属性“bar ”,我将它绑定到我的 UI:

```
**public class** Foo {
    **private int bar**;

    **public int** getBar() {
        **return bar**;
    }

    **public void** setBar(**int** bar) {
        **this**.**bar** = bar;
    }
}
```

我希望每次调用“setBar”时 UI 都发生变化，所以让我们看看如何使数据绑定跟上变化:

## 可观察量

最灵活的机制是让您的对象实现 Observable 接口，它有以下两种方法:

```
**void** addOnPropertyChangedCallback(OnPropertyChangedCallback c);
**void** removeOnPropertyChangedCallback(OnPropertyChangedCallback c);
```

当您的字段发生更改时，必须通知 OnPropertyChangedCallback:

```
**public abstract void** onPropertyChanged(Observable sender,
                                       **int** propertyId);
```

该类还必须用“@Bindable”注释任何应该观察到的 getters。

```
@Bindable
**public int** getBar() {
    **return bar**;
}
```

这告诉数据绑定框架该属性是可观察的，并在应用程序包的“BR”类中生成一个标识符。这个类类似于“R”类，但是用于 propertyId 的绑定资源。

Android 数据绑定为您提供了 PropertyChangeRegistry 类，使跟踪侦听器变得更加容易:

```
**public class** Foo **implements** Observable {
    **private** PropertyChangeRegistry **registry** = 
        **new** PropertyChangeRegistry();
    **private int bar**;

    @Bindable
    **public int** getBar() {
        **return bar**;
    }

    **public void** setBar(**int** bar) {
        **this**.**bar** = bar;
        **registry**.notifyChange(**this**, BR.bar);
    }

    @Override
    **public void** addOnPropertyChangedCallback(
                   OnPropertyChangedCallback callback) {
        **registry**.add(callback);
    }

    @Override
    **public void** removeOnPropertyChangedCallback(
                   OnPropertyChangedCallback callback) {
        **registry**.remove(callback);
    }
}
```

## 基本可观察的

如果您的模型对象可以扩展基类，BaseObservable 通过为您实现 Observable 接口使它变得更容易。您只需要注释属性并通知更改:

```
**public class** Foo **extends** BaseObservable {
    **private int bar**;

    @Bindable
    **public int** getBar() {
        **return bar**;
    }

    **public void** setBar(**int** bar) {
        **this**.**bar** = bar;
        notifyPropertyChanged(BR.bar);
    }
}
```

## 可观察视野

Observable 和 BaseObservable 对于简单的用法来说有点复杂，所以我们引入了 ObservableField 和它的原始对应物。要使用 ObservableFields，只需在类中声明公共的 final 字段。这次我将使用两个属性来演示基本类型和引用类型:

```
**public class** Foo {
    **public final** ObservableInt **bar** = **new** ObservableInt();
    **public final** ObservableField<String> **baz** = 
        **new** ObservableField<>();
}
```

您可以在绑定表达式中使用这些作为普通属性:

```
<**TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="*@{foo.baz}*"**/>
```

虽然绑定表达式仍然吸引人，但是 Java 需要调用 setters 和 getters:

```
foo.**bar**.set(age);
String name = foo.**baz**.get();
```

## 可观察地图

有些时候，数据没有很好地定义。您可能仍在构建原型，来自服务器的数据格式仍在不断变化，您不想为它们创建对象。ObservableMap 及其实现 ObservableArrayMap 就是为这些更加模糊的数据结构而创建的。

使用 ObservableMap，您可以像访问任何普通地图一样访问地图中的值，并且当发生变化时，UI 将会更新。例如:

```
<**data**>
    <**variable name="product"
    type="*android.databinding.ObservableMap&lt;String, Object&gt;*"**/>
</**data**>

<!-- ... -->
<**TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text='*@{product["name"]}*'**/>
```

你可以在代码中使用 ObservableMap:

```
ObservableMap<String, Object> product = **new** ObservableArrayMap<>();
binding.setProduct(product);
product.put(**"name"**, **"Golf Ball"**);
```

任何时候产品对象发生变化，它都会更新 UI。

## 结论

许多用户界面不需要被告知数据的变化，所以你不需要为你的整个模型增加可观察性。另一方面，当您的服务器发送新数据或用户对 UI 的一部分进行更改，而 UI 的其余部分自动更新时，让 UI 自动更新会很好。

选择你喜欢的观察方法。大多数会扩展 BaseObservable 或使用 ObservableFields，但其他的也有它们的用途。