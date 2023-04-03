# 将新的视图模型添加到您的 MVVM 中

> 原文：<https://medium.com/google-developer-experts/add-the-new-viewmodel-to-your-mvvm-36bfea86b159?source=collection_archive---------0----------------------->

![](img/f7d72b3babf36b3cf91a013ab6618b07.png)

[https://www.flickr.com/photos/75487768@N04/16741949632](https://www.flickr.com/photos/75487768@N04/16741949632)

Google 在 I/O 上宣布了一组[架构组件](https://developer.android.com/topic/libraries/architecture/index.html)。其中包括一个`[ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel.html)`，一个视图将绑定到的类，这样视图模型包含的数据将在屏幕旋转后继续存在。对于使用 MVVM 的开发者来说，这听起来棒极了。获得我们无论如何都需要的视图模型，并且能够经受住方向变化，这听起来好得不像是真的。

但是，它能被轻松地应用到真实的 word 应用程序中吗？

我把我们在维亚康姆的 Playplex 应用程序带到了我们的 shows screen，一个电视节目列表，它是 Comedy Central 等成功应用程序的基础。该实现已经基于 MVVM，片段有一个 ShowsViewModel，每个 RecyclerView 项有另一个视图模型。

## 台阶

我们需要做什么？
如果您需要应用程序上下文，首先通过`ViewModel`或`AndroidViewModel`扩展您现有的视图模型类。

```
public class ShowsViewModel **extends ViewModel** {
```

接下来找到创建视图模型的地方，可能是在`Fragment`或`Activity`中，用对`ViewModelProviders`的方法调用替换模式的`new`

```
ShowsViewModel createViewModel() {
    **return** ViewModelProviders.*of*(**this**).get(ShowsViewModel.**class**);
}
```

这将为您创建一个或重用一个现有的。

## 但是如果…

但是您的构造函数可能不为空:

```
ShowsViewModel(ShowsUseCase useCase) {
   this.useCase = useCase;
}
```

怎么办？
您可以实现一个知道如何创建视图模型的提供者工厂。这个人甚至可以使用依赖注入来获得你需要的一切。通过这种方式，您将创建视图模型的责任转移到一个单独的类中，这已经使您的代码变得更好了([单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle))。

```
class ShowsViewModelFactory **implements ViewModelProvider.Factory** {

    private final ShowsUseCase useCase;

    @Inject
    public ShowsViewModelFactory(ShowsUseCase useCase) {
       this.useCase = useCase;
    }

   ** @Override
    public ShowsViewModel create(Class modelClass) {
       ** return new ShowsViewModel(useCase); **}**
}
```

并采用对`ViewModelProviders`的调用

```
@Inject **ShowsViewModelFactory factory;**
...
ShowsViewModel createViewModel() {
    returnViewModelProviders.*of*(this**,factory**)
                             .get(ShowsViewModel.class);
}
```

就这样，你基本完成了！

## 不再有生命周期转发

但是可能你有一些清理的方法。如果您将生命周期事件从片段或活动转发到视图模型，这些可能会被删除。整个想法是，您不再关心这些，因为视图模型不会在配置更改时被破坏。

不要担心，当活动被完全破坏时，您仍然有可能释放资源。简单地覆盖`onCleared()`来取消长时间运行的操作，例如:

```
@Override
protected void onCleared() {
    super.onCleared();
    cleanupSubscriptions();
}
```

## 保持流向正确

我希望您从未将任何`View`或`Activity`传递到您的视图模型中。这会导致泄露，所以在任何情况下都要尽量避免，因为这也不是 MVVM 应该做的！

## 数据绑定

那么用安卓数据绑定呢？有用吗？
的确如此，来自 Google 的[基本样本已经建立在它的基础上。
在数据绑定中，您有不同的实现方式来通知视图它所绑定到的项目的变化。
您要么公开`ObservableField`的公共实例，要么将您的视图绑定到方法而不是字段，并手动使用`notifyPropertyChanged`来通知变更。如果你使用第一种方法，你不需要改变什么。第二种方法有点问题。这里你的类已经为通知代码扩展了`BaseObservable`一个很好的便利类。因为 Java 不允许多重继承，所以你不能同时拥有`ViewModel`和`BaseObservable`。但是不要担心，](https://github.com/googlesamples/android-architecture-components/tree/master/BasicSample) [Eric Richardson](https://medium.com/u/3c04ed009476?source=post_page-----36bfea86b159--------------------------------) 在这里提出了一个(不那么)好的解决方法:

从`BaseObservable`复制代码，让您的版本扩展`ViewModel`:

```
public class BaseObservableViewModel **extends ViewModel implements Observable** {

    private transient PropertyChangeRegistry callbacks;

    public BaseObservableViewModel() {
    }

   ...
}
```

这样你就得到两个世界。这就是你需要的所有变化。( [Jose Alcérreca](https://medium.com/u/e0a4c9469bb5?source=post_page-----36bfea86b159--------------------------------) 承诺将来会修复它，所以把它看作是临时的解决方法)

## 所有的事情？

那么您现在必须将所有的视图模型转移到新的类结构中吗？其实不是。让我们假设您在`RecyclerView`中为每个项目使用视图模型。这些可能没有保存单独的数据，但有一个更广泛的收集。这些小视图模型可以保持原样，只要它们没有被绑定到活动或片段的生命周期中。所以在这里要小心。

## 结论

视图模型是我们 MVVM 实现中缺少的一环吗？我认为是这样的，迁移到 it 非常简单。