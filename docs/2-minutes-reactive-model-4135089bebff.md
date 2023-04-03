# 2 分钟:反应式模型

> 原文：<https://medium.com/quick-code/2-minutes-reactive-model-4135089bebff?source=collection_archive---------1----------------------->

为一个新项目尝试了一个有趣的东西——基于 RxJava 的不可变模型。

所以主要的想法是:

```
**public interface** IRxModel<Item **extends** IRxModelItem, Category> {

    */**
     * submitting one item to add/replace
     ** ***@param item*** **/* **void** submitItem(@NonNull Item item);

    */**
     * submitting a few items to add/replace
     ** ***@param items*** **/* **void** submitItems(@NonNull List<? **extends** Item> items);

    */**
     * delete an item
     ** ***@param id*** **/* **void** delete(String id);

    */**
     * delete all items
     */* **void** clear();

    */**
     * get item as a sync operation
     ** ***@param id*** *** ***@return*** *item or null
     */* @Nullable
    Item getItem(String id);

    */**
     * get Observable for item
     ** ***@param id*** *** ***@return*** *observable to emit item updates until item is deleted
     */* @NonNull
    Observable<Item> subscribeItem(String id);

    */**
     * get Observable to emit category items updates until cleared
     ** ***@param category*** *** ***@return*** **/* @NonNull
    Observable<List<Item>> subscribeCategory(Category category);
}
```

模型包含一些项目。项目可以通过它们的 id 以及它们的类别来处理。最重要的是—您可以订阅项目，包括目前在模型中不可用的项目。比如稍后将要创建/下载/解析的空类别或项目 id。

```
**public interface** ICategorizator<Item, Category> {
    Set<Category> categorize(Item item);
}
```

您的 ICategorizator 实现可以根据您的需要将项目分成任意多的类别。例如，对于出租车订单，订单可能同时是 OrderWithCars、RunningOrder、OrderInDowntown、VIP 模型机制是相同的。

```
**public interface** IRxModelItem {
    String getId();
}
```

Item 需要提供字符串 ID，这是唯一的问题——它可能不会在您的代码中以这种方式使用(如果您使用 int 或者根本不使用 ID)。

代码在这里[https://github.com/Cryosleeper/RxModel](https://github.com/Cryosleeper/RxModel)，你可以从 jcenter 中使用

```
implementation **'com.cryosleeper.android:rx-model:1.0.1'**
```

*2 分钟是少于一根烟，而冲泡咖啡的 Android 中不同问题的文章。你可以使用标签* [*2 分钟*](/tag/2-minutes-read/latest) *来阅读其他的。我总是很高兴听到你的反馈，告诉我如何做得更好，以及你想读些什么。*