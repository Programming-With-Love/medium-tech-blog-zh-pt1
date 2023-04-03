# 单线镖，什么？—第一部分

> 原文：<https://medium.com/globant/single-thread-dart-what-ccbca2543ae9?source=collection_archive---------0----------------------->

我知道你们中的一些人可能会对我说有 Async Await 和 RxDart，所以不要担心兄弟。我完全投入其中，但是在阅读了很多为什么应用程序性能在 Flutter 中是强制性的之后，我知道 Dart 是一个单线程框架。

Dart 是一个单线程系统。有时我们很难使用它，因为现在每种语言都使用多线程系统，dart 使用旧的概念，但它还没有结束，Dart 仍在发展。采取这一点的主要原因是开始颤振隔离，但让我们首先检查为什么我们需要有隔离。

> *要点 Dart 一次执行一个操作，一个接一个，这意味着只要一个操作正在执行，它就不能被任何其他 Dart 代码中断。*

创建一个默认的新 flutter 应用程序，它将创建一个增量计数器模板。

```
@override
 Widget build(BuildContext context) {
  return Scaffold(
   appBar: AppBar(
    title: Text(widget.title),
   ),
   body: Center(
    child: Column(
     mainAxisAlignment: MainAxisAlignment.center,
     children: <Widget>[
      CircularProgressIndicator(),
      Text(
       'You have pushed the button this many times:',
      ),
      Text(
       '$_counter',
       style: Theme.of(context).textTheme.display1,
      ),
     ],
    ),
   ),
   floatingActionButton: FloatingActionButton(
    onPressed: _incrementCounter,
    tooltip: 'Increment',
    child: Icon(Icons.add),
   ), // This trailing comma makes auto-formatting nicer for build methods.
  );
 }
```

我们增加了循环进度指示器，只是为了检查用户界面何时卡住或跳帧。让我们更新 incrementCounter 函数。

```
void _incrementCounter() {
  setState(() {
   var string = Constants.str;
   for(int index = 0;index <10;index++) {
    for (int i = 0; i < string.length; i++) {
     string += string[i];
    }
   } _counter++;
  });
 }
```

点击 FAB 按钮，圆形进度条停留一会儿，然后再次加载。这是由于主线程(或者我可以说主隔离，叶的主线程在这里颤振是主隔离)的沉重负担。嗯，你可能会说“啊！！这很简单，只需将这段代码放入一个异步函数中，然后从那里调用它,“嗯，这是正确的，但它不会工作。为什么？因为 Dart 基于事件循环机制工作，这意味着它为主线程或任何隔离线程创建事件，并基于事件优先级运行。

## 事件循环

事件循环有点像无限队列或循环，永远运行。示例:创建一个异步函数并调用它。在这个时间点上，事件循环包含的是这个信息序列。

1.  返回该方法的输出，即 Future
2.  同步运行异步函数，直到第一个 await 关键字。
3.  等待异步函数完成并执行剩余代码。

> *异步方法不是并行执行的，而是遵循事件的常规顺序，也由事件循环处理。*

所以如果我们有

```
main(){
  method1();
  method2();
}
method1() async{
  print("1");
}
method2() async{
  Future((){
    print("Future 2"); 
  });
  print("2");
}
```

输出:

```
1
2
Future 2
```

所以在主隔离中，事件循环包含的内容是这样的

1.  呼叫方法 1
2.  呼叫方法 2
3.  从方法 2 添加未来事件
4.  执行打印语句
5.  执行方法 2 中的未来事件

为什么方法 2 的未来不在打印 2 之前运行？因为每个入口点都在事件循环内部，并且因为在那个时间点不需要期货，所以下一个序列将在期货之前执行。如果我们稍微调整一下代码，它会显示一个不同的结果。

```
method2() async{
  await Future((){
    print("Future 2"); 
  });
  print("2");
}
```

现在可以打印了

```
1
Future 2
2
```

因为事件循环知道它需要在执行下一条语句之前等待结果。

> *在执行 main 方法后，每次更改都必须通过事件循环来更新数据。因此，在构建 Flutter 应用程序时，你可能会感到 UI 滞后。像做 IO 操作或位图操作是一种 CPU 或磁盘处理，它需要在事件循环中花费大量时间，所以下一帧不会得到更新，用户界面将被卡住一段时间。检查* `*--profile*` *标志以获得更多细节来检查 flutter 中的轮廓工具(将在后面的文章中介绍如何检查)。*

参考链接:

1.  [从 Flutter Devs 中分离和事件循环介绍视频](https://www.youtube.com/watch?v=vl_AaCgudcY)

TLDR；

Flutter 使用 dart，dart 使用事件循环，所以 flutter 也是单线程系统。这就是为什么我们必须避免在主线程上进行一些密集工作，比如写入文件或获取数据以及操作大型计算操作。

继续阅读下面的第 2 部分。

 [## 单线镖，什么？—第二部分

### Dart 对异步功能使用隔离和事件循环，不要错过引导异步将在主线程上运行…

medium.com](/@parthdave93/single-thread-dart-what-part-2-a5592bef5213) 

你知道你可以按拍手吗👏按钮 50 次？你走得越高，我就越有动力为你们写更多的东西！

你好，我是帕斯·戴夫。noob 开发者和 noob 摄影师。你可以在 [Linkedin](https://in.linkedin.com/in/parth-dave-907b8177) 上找到我，或者在 [GitHub](https://github.com/parthdave93) 上跟踪我，或者在 [Twitter](https://twitter.com/the_parth_dave) 上关注我？

祝你有一个愉快的飞行日！