# 您不必使用 WeakReference 来避免内存泄漏

> 原文：<https://medium.com/google-developer-experts/weakreference-in-android-dd1e66b9be9d?source=collection_archive---------3----------------------->

![](img/10cd373cc5bcc9422523da9656e198c3.png)

我的一位同事最近提到，他们看到一个演示文稿说:

> 如果你是一个 Android 开发者，并且不使用 WeakReferences，那你就有问题了。

我个人认为，这不仅是一个错误的论点，而且完全是误导。WeakReference 应该是修复内存泄漏的最后手段。

然后今天，我在谷歌开发者专家出版物上看到了由[Enrique lópez Maas](https://medium.com/u/f08187f6a023?source=post_page-----dd1e66b9be9d--------------------------------)写的帖子

[](/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83) [## 最终理解引用在 Android 和 Java 中是如何工作的

### 几周前，我参加了 Mobiconf，这是我有幸参加的最好的移动开发者大会之一…

medium.com](/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83) 

这是一篇很好的文章，用例子总结了引用在 Java 中是如何工作的。

这篇文章没有说我们必须使用 *WeakReference* ，但也没有给出任何替代方案。我觉得我必须给出替代方案，以表明使用 *WeakReference 并不是**必须**的。*

## 如果你不使用 WeakReference，你真的不会有任何问题。

我认为，在所有可能的地方使用 *WeakReference* 并不是最佳做法。使用 *WeakReference* 来修复内存泄漏表明缺乏建模或架构。

尽管本文中给出的示例修复了潜在的内存泄漏，但是还有其他方法。我想给出几个实现的例子来避免当你有一个长时间运行的后台任务时的内存泄漏。

使用 *AsyncTask* 来避免内存泄漏的一个简单示例如下:

下面是我的*活动:*

```
public class MainActivity extends Activity { private MyAsyncTask task; @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    task = new MyAsyncTask();
    task.setListener(createListener());
    task.execute();
  } @Override
  protected void onDestroy() {
    task.setListener(null);
    super.onDestroy();
  } private MyAsyncTask.Listener createListener() {
    return new MyAsyncTask.Listener() { @Override
      public void onSuccess(Object object) {
        // adapt contents
      }
    };
  }
}
```

这里是 *AsyncTask:*

```
class MyAsyncTask extends AsyncTask { private Listener listener; @Override
  protected Object doInBackground(Object[] params) {
    return doSomeStuff();
  } private Object doSomeStuff() {
    //do something to get result
    return new Object();
  } @Override
  protected void onPostExecute(Object object) {
    if (listener != null) {
      listener.onSuccess(object);
    }
  } public void setListener(Listener listener) {
    this.listener = listener;
  } interface Listener {
    void onSuccess(Object object);
  }
}
```

这种实现当然是非常基本的，但我认为它足以展示另一种解决方案。

这里是另一个非常简单的 RxJava 实现，我们仍然没有 WeakReference。

```
public class MainActivity extends Activity {

  private Subscription subscription;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    subscription = Observable
        .*fromCallable*(new Callable<Object>() {
          @Override
          public Object call() throws Exception {
            return doSomeStuff();
          }
        })
        .subscribeOn(Schedulers.*io*())
        .observeOn(AndroidSchedulers.*mainThread*())
        .subscribe(new Action1<Object>() {
          @Override
          public void call(Object o) {
            // adapt contents
          }
        });
  }

  private Object doSomeStuff() {
    //do something to get result
    return new Object();
  }

  @Override
  protected void onDestroy() {
    subscription.unsubscribe();
    super.onDestroy();
  }

}
```

请注意，如果我们不从*订阅中*取消*订阅，我们可能仍然会有内存泄漏。*

最后，我想举几个诺沃达的例子。它们是很好的学习资源。你可以猜到，他们没有任何 *WeakReference :)*

[](https://github.com/novoda/bonfire-firebase-sample/) [## 诺沃达/篝火-燃烧基地-样本

### 一个讨论你最喜欢的表情符号的应用。这是一个用 Firebase 构建的示例应用程序。

github.com](https://github.com/novoda/bonfire-firebase-sample/) [](https://github.com/novoda/spikes/tree/master/Architecture/TodoApp/android) [## 诺沃达/斯派克

### 尖峰——想法和概念诞生和孵化的地方

github.com](https://github.com/novoda/spikes/tree/master/Architecture/TodoApp/android) 

我相信经验法则(对我来说)是，如果类**是内部类，就让它们成为静态的**。尤其是在后台长时间运行时。更好的是，我们可以将这些类移动到一个完整的类中。

对长时间运行的操作使用非静态内部类总是不好的做法，不仅仅是在 *Android* 中。