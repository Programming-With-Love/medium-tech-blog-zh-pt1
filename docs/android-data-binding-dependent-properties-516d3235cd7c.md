# Android 数据绑定:依赖属性

> 原文：<https://medium.com/androiddevelopers/android-data-binding-dependent-properties-516d3235cd7c?source=collection_archive---------1----------------------->

![](img/c462e754a0734abb38ba882e6d20c058.png)

## 声明而不是编码依赖项

[Android Studio 2.3 已经发布](https://android-developers.googleblog.com/2017/03/android-studio-2-3.html)，我可以谈一点关于数据绑定的特性，你将能够使用它。您可以做的一件很酷的事情是创建从属属性。

## 名字，名字，名字

在我的应用程序中，我有一个表示用户的视图模型。它包含名、姓、用户名和显示名。每当这些值改变时，UI 应该更新，所以[我已经使它可观察到](/google-developers/android-data-binding-observability-9de4ff3fe038#.6dnmytszy)。

```
**public class** User **extends** BaseObservable {
    **private** String **firstName**;
    **private** String **lastName**;
    **private** String **userName**;
    **private** Resources **resources**; **public** User(Resources resources) {
        **this.**resources = resources**;**
    } @Bindable
    **public** String getFirstName() {
        **return firstName**;
    }

    **public void** setFirstName(String firstName) {
        **this**.**firstName** = firstName;
        notifyPropertyChanged(BR.firstName);
        notifyPropertyChanged(BR.displayName);
    }

    @Bindable
    **public** String getLastName() {
        **return lastName**;
    }

    **public void** setLastName(String lastName) {
        **this**.**lastName** = lastName;
        notifyPropertyChanged(BR.lastName);
        notifyPropertyChanged(BR.displayName);
    }

    @Bindable
    **public** String getUserName() {
        **return userName**;
    }

    **public void** setUserName(String userName) {
        **this**.**userName** = userName;
        notifyPropertyChanged(BR.userName);
        notifyPropertyChanged(BR.displayName);
    }

    @Bindable
    **public** String getDisplayName() {
        **if** (**firstName** == **null**) {
            **if** (**lastName** == **null**) {
                **return userName**;
            } **else** {
                **return lastName**;
            }
        } **else if** (**lastName** == **null**) {
            **return firstName**;
        } **else** {
            **return resources**.getString(R.id.name**,
                firstName**, **lastName**);
        }
    }
}
```

您可以看到显示名称没有支持字段。它检查其他字段并根据其他字段生成一个值。

使用我的用户类，可以针对所有属性编写数据绑定表达式。每当 firstName、lastName 和 userNames 发生变化时，包含 displayName 的表达式也会更新，因为我已经通知了 displayName 在相应的 setter 方法中发生了变化。

这是一个常见的场景:一个属性依赖于其他属性。作为依赖项的属性(例如 setFirstName())也必须通知它的依赖属性发生了变化，这并不好。

## 声明的依赖属性

在 [Android Studio 2.3](https://android-developers.googleblog.com/2017/03/android-studio-2-3.html) 中，你现在可以声明一个依赖于其他属性的可绑定属性:

```
**public class** User **extends** BaseObservable {
    //...

    @Bindable
    **public** String getFirstName() { ... }
    **public void** setFirstName(String firstName) {
        **this**.**firstName** = firstName;
        notifyPropertyChanged(BR.firstName);
    }

    @Bindable
    **public** String getLastName() { ... }
    **public void** setLastName(String lastName) {
        **this**.**lastName** = lastName;
        notifyPropertyChanged(BR.lastName);
    }

    @Bindable
    **public** String getUserName() { ... }

    **public void** setUserName(String userName) {
        **this**.**userName** = userName;
        notifyPropertyChanged(BR.userName);
    }

    @Bindable({**"firstName"**, **"lastName"**, **"userName"**})
    **public** String getDisplayName() { ... }
}
```

可绑定注释现在采用一个可选参数来声明依赖关系。displayName 属性被声明为依赖于 firstName、lastName 和 userName 属性。每当这些属性中的任何一个发生通知更改时，包含 displayName 的表达式也将被刷新。

依赖关系在生成的绑定类中被完全评估。这意味着，如果您将一个 Observable 附加到 User，那么当其他属性发生更改时，您将不会收到 displayName 的通知。

使用从属属性有助于清理视图模型。虽然底层数据模型是相对规范化的——数据不会在多个属性中重复——但视图模型是根据 UI 中显示的内容定制的。这意味着属性经常相互影响。

例如，假设您的 UI 显示一张发票，您的商店根据购买的数量提供折扣。折扣取决于小计。以后，如果您将折扣更改为也依赖于运输州，则只更新折扣属性，而不更新州属性。

从属属性应该有助于保持视图模型的整洁和易于维护。