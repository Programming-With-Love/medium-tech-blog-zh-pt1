# 最终理解引用在 Android 和 Java 中是如何工作的

> 原文：<https://medium.com/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83?source=collection_archive---------0----------------------->

![](img/614f4c967d99c0439a0e9c6857e0abf8.png)

几周前，我参加了 Mobiconf，这是我有幸在波兰参加的最好的移动开发者大会之一。我的朋友兼同事 [Jorge Barroso](https://github.com/flipper83) 在他的折中报告《最佳(良好)实践》中提出了一句话，让我听后反思:

> 如果你是一个 Android 开发者，并且不使用 WeakReferences，那你就有问题了。

举个好时机的例子，几个月前我的确出版了我的上一本书，“Android 高性能”，与[迭戈·格兰西尼](https://www.linkedin.com/in/diegograncini)合著。其中最有激情的一章是关于 Android 内存管理的。在本章中，我们将讨论移动设备中的内存是如何工作的，内存泄漏是如何发生的，为什么这很重要，以及我们可以应用哪些技术来避免它们。自从我开始为 Android 开发以来，我一直观察到一种倾向，即不自觉地避免或降低与内存泄漏和内存管理相关的所有事情的优先级。如果满足了功能标准，为什么还要麻烦呢？我们总是急于开发新的特性，我们宁愿在下一个 Sprint 演示中展示一些可视化的东西，而不是关心那些没人一眼就能看到的东西。

这是一个很好的论点，不可逆转地导致收购技术债务。我甚至要补充一点，技术债务在现实世界中也有一些我们无法用单元测试来衡量的影响:失望、开发伙伴之间的摩擦、交付的低质量软件以及动力的丧失。这种影响难以衡量的原因是，它们往往是长期发生的。这有点像政客:如果我只负责 8 年，我为什么要担心 12 年后会发生什么？除了在软件开发中，一切都进行得更快。

写关于软件开发中采用的体面和适当的心态可能需要几本书，并且已经有许多书籍和文章可供您探索。然而，简要解释不同类型的内存引用，它们的含义以及如何在 Android 中应用将是一个更简短的任务，这也是我在本文中想要做的。

首先:Java 中的引用是什么？

> 引用是被注释的对象的方向，所以你可以访问它。

Java 默认有 4 种类型的引用:**强**、**软**、**弱**和**幻**。有些人认为，只有两种类型的引用，强引用和弱引用，弱引用可以表示两种程度的弱引用。我们倾向于以植物学家的分类毅力对生活中的一切进行分类。无论什么对你更好，但首先你需要理解他们。然后你就可以搞清楚自己的分类了。

每种类型的参考是什么意思？

**强引用:**强引用是 Java 中的普通引用。每当我们创建一个新对象时，默认情况下都会创建一个强引用。例如，当我们:

```
MyObject object = new MyObject();
```

一个新的对象 *MyObject* 被创建，对它的强引用被存储在 *object* 中。简单到目前为止，你还和我在一起吗？好了，现在更有趣的事情来了。这个*对象*是**强可达的**——也就是说，它可以通过一个强引用链来达到。这将阻止垃圾收集者捡起并销毁它，这是我们最想要的。但是现在，让我们来看一个例子，这可能对我们不利。

```
public class MainActivity extends Activity { @Override
    protected void onCreate(Bundle savedInstanceState) {   
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        new MyAsyncTask().execute();
    }

    private class MyAsyncTask extends AsyncTask { @Override
        protected Object doInBackground(Object[] params) {
            return doSomeStuff();
        } private Object doSomeStuff() {
            //do something to get result
            return new MyObject();
        } 
    }
}
```

花几分钟时间，试着找出任何容易出问题的方法。

别担心，如果做不到就多花点时间。

现在吗？

*AsyncTask* 将与*Activity**onCreate()*方法一起被创建和执行。但是这里我们有一个问题:内部类需要在它的整个生命周期中访问外部类。

当*活动*被破坏时会发生什么？ *AsyncTask* 持有对*活动*的引用，而*活动*不能被 GC 收集。这就是我们所说的内存泄漏。

> **旁注**:在我以前的生活中，当我对潜在的候选人进行面试时，我会问他们你如何制造内存泄漏，而不是问什么是内存泄漏的理论问题。它总是更有趣！

内存泄漏实际上不仅发生在*活动*被破坏*本身*的时候，也发生在由于配置改变或需要更多内存等原因而被系统强制破坏的时候。如果 *AsyncTask* 很复杂(即，在*活动*中保持对*视图*的引用，等等)，它甚至会导致崩溃，因为视图引用是空的。

那么如何才能防止这种问题再次发生呢？让我们解释一下另一种类型的引用:

弱引用(weak reference):弱引用是指引用的强度不足以将对象保留在内存中。如果我们试图确定该对象是否被强引用，并且碰巧是通过 *WeakReferences* ，那么该对象将被垃圾收集。就理解而言，最好放弃理论，展示一个实际例子，说明我们如何修改前面的代码以使用 *WeakReference* 并避免内存泄漏:

```
public class MainActivity extends Activity { @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        new MyAsyncTask(this).execute();
    } private static class MyAsyncTask extends AsyncTask {
        private WeakReference<MainActivity> mainActivity;    

        public MyAsyncTask(MainActivity mainActivity) {   
            this.mainActivity = new WeakReference<>(mainActivity);            
        } @Override
        protected Object doInBackground(Object[] params) {
            return doSomeStuff();
        } private Object doSomeStuff() {
            //do something to get result
            return new Object();
        } @Override
        protected void onPostExecute(Object object) {
            super.onPostExecute(object);
            if (mainActivity.get() != null){
                //adapt contents
            }
        }
    }
}
```

注意现在的一个主要区别:内部类中的活动现在引用如下:

```
private WeakReference<MainActivity> mainActivity;
```

这里会发生什么？当活动停止存在时，因为它是通过弱引用的方式持有的，所以它可以被收集。因此不会发生内存泄漏。

> **边注:**现在你有望更好地理解什么是 WeakReferences，你会发现 WeakHashMap 这个职业很有用。除了使用 WeakReferences 引用键(键，而不是值)之外，它完全像 HashMap 一样工作。这使得它们对于实现缓存等实体非常有用。

然而，我们提到了更多的参考文献。让我们看看它们在哪里有用，以及我们如何从中受益:

**软引用**:把一个*软引用*想象成一个更强的*弱引用*。鉴于 *WeakReference* 将被立即收集，除非没有其他选择，否则*软引用*将请求 GC 留在内存中。[垃圾收集器算法](https://plumbr.eu/handbook/garbage-collection-algorithms-implementations)真的很令人兴奋，你可以连续几个小时钻研它而不感到疲倦。但基本上，他们会说“我会永远收回‘弱势’的提法。如果对象是一个软引用，我将根据生态系统条件来决定做什么”。这使得*软引用*对于缓存的实现非常有用:只要内存足够大，我们就不必担心手动移除对象。如果你想看一个实际的例子，你可以查看用*软引用*实现的缓存的[这个例子](http://peters-andoird-blog.blogspot.de/2012/05/softreference-cache.html)。

**幻象参考**:啊，幻象参考！我想我可以用一只手的手指来计算我在生产环境中使用它们的频率。一个只通过幻象引用的对象可以在垃圾收集器需要的时候被收集。没有进一步的解释，没有“给我回电话”。这使得很难定性。为什么我们会喜欢用这样的东西？其他的还不够有问题吗？我为什么选择做程序员？PhantomReference 可以准确地用来检测对象何时被从内存中删除。作为一个完全透明的人，我记得在我整个职业生涯中，有两次我不得不使用幻象参考。所以，如果他们现在很难理解，不要紧张。

希望这能让你对之前关于推荐人的想法有所了解。就像在任何学习问题上一样，你可能想要开始[实际一点](/@enriquelopezmanas/the-theoretical-animal-4f6901aaf571#.5nocvfu4m)，摆弄你的代码，看看你能如何改进它。第一步将是查看您是否有任何内存泄漏，并查看您是否可以使用这里学到的任何经验来消除那些讨厌的内存泄漏。如果你喜欢这篇文章或者它确实帮助了你，请随意分享和/或留下评论。这是给业余作家提供燃料的货币。

感谢我的同事[塞巴斯蒂安](https://twitter.com/semuvex)对这篇文章的投入！