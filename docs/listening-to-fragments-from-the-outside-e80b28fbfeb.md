# 听着片段…从外面

> 原文：<https://medium.com/google-developer-experts/listening-to-fragments-from-the-outside-e80b28fbfeb?source=collection_archive---------4----------------------->

![](img/9bc9e96063df1443e647860eae18327b.png)

Foto di [Adrien Olichon](https://www.pexels.com/it-it/@adrien-olichon-1257089?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) da [Pexels](https://www.pexels.com/it-it/foto/bicchiere-cristallo-distrutto-in-frantumi-2663255/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

大多数 Android 开发者在他们的职业生涯中至少与`Application.ActivityLifecycleCallbacks`打过一次交道，但是你知道同样的事情也可以用`Fragments`来做吗？

## 为什么的原因

让我们后退一步。通常，您不需要从外部了解一个`Fragment`的生命周期:例如，您可能需要监听多个`Fragment`实例，而不需要改变每个实例的代码。

假设你正在开发一个 SDK，你需要对每个被创建的`Fragment`做出反应。你可以要求你的用户注册他们的每一个类，或者你可以创建一个必须扩展的基类，但是这并不总是可能的。下面几段我们就把自己放在这种情况下吧。

## 它是如何工作的？

撇开具体的生命周期事件不谈，在`FragmentLifecycleCallbacks`和它的对应产品`Activity`之间有很多相似之处。第一个是，即使它们实际上都被声明为`abstract classes`，前者带有**默认的无操作实现**，而后者没有。

当然，这与每种情况下可以发送的事件数量有关。另一个区别是`FragmentLifecycleCallbacks`必须连接到`FragmentManager`上，而`ActivityLifecycleCallbaks`通常连接到`Application`上。当然，这是一个逻辑上的区别，因为它们都绑定到将启动`Fragment`或`Activity`的组件。

## 该结构

正如我们在上一段中提到的，`FragmentLifecycleCallbacks`提供了广泛的回调，覆盖了`Fragment`的整个生命周期，但是您可以只覆盖您的案例中需要的方法。

在示例中，我们只对几个函数感兴趣，只是为了测试功能:

既然我们已经创建了监听的主入口点，我们需要将对象附加到`FragmentManager`:我们可以用一个接受 2 个参数的 API 调用来完成。第一个是听众，而第二个是一个叫做`recursive`的`Boolean`。当后者被设置为`true`时，同一个监听器被连接到每个`childFragmentManager`，这样层次结构中的每个`Fragment`都可以触发事件。侦听器可以很容易地连接，只需一行代码:

## 结论

虽然大多数人可能不熟悉这个 API，但在您的职业生涯中可能会有需要监听`Fragment`实例及其生命周期的情况，这些函数会派上用场。

如果你想试试这个功能，你可以在 Github 上的一个[游乐场项目中找到它。](https://github.com/tiwiz/AndroidXSharedTest/tree/master/app/src/main/java/net/orgiu/tests/fragmentslifecycle)

*感谢*[*Sebastiano Gottardo*](https://twitter.com/rotxed)*[*克里·约翰逊*](https://twitter.com/johnson_cor) *和*[*Omar Miatello*](https://twitter.com/OmarMiatello)*对本帖进行校对。**