# 在 RecyclerView 中处理单击事件

> 原文：<https://medium.com/androiddevelopers/for-my-next-trick-i-will-write-about-onclick-45e0a6881c8a?source=collection_archive---------1----------------------->

![](img/38a783b350f16546090f206941ac8454.png)

## 对于我的下一个技巧，我将写关于 onClick()

这是[系列文章](https://medium.com/androiddevelopers/tagged/recycler-view-series)的第三篇，涵盖了创建和使用`RecyclerView`的基础知识。如果你已经对如何创建一个`RecyclerView`有了坚实的理解，那么继续吧。否则，考虑从[这篇帖子](/androiddevelopers/getting-to-know-recyclerview-ea14f8514e6)开始。

当使用`RecyclerView`显示数据列表时，您可能希望在项目被点击时有一个响应。这个响应可以打开一个包含更多数据的新页面，呈现一个祝酒词，删除一个项目，等等。可能性是无限的，但它们都是使用`onClick()`完成的。

# 定义点击动作

在创建监听器之前，在`Activity`类中创建一个函数，该函数执行点击时的动作。

接下来，更新`Adapter’s`构造函数以接受`onClick()`函数。

在`Activity`类中，当初始化`Adapter`时，传入新创建的函数。

# 添加 onClickHandler()

既然已经定义了动作，是时候将它附加到`Adapter`中的`ViewHolder`了。

更新`ViewHolder`以将`onClick()`作为一个参数。

在初始化器中，调用`itemView`上的`setOnClickListener{}`。

就是这样！你的`RecyclerView`现在有反应了，是时候点击了！

编码快乐！

# 后续步骤

包括`onClick()`在内的完整代码示例可以在这里找到[。](https://github.com/android/views-widgets-samples/tree/main/RecyclerViewKotlin)

感谢您阅读我的`RecyclerView`系列的第三部分！请继续关注我写的更多`RecyclerView`特性。

如果你想了解更多关于`onClick()`的信息，请查阅[文档](https://developer.android.com/reference/android/view/View.OnClickListener)。