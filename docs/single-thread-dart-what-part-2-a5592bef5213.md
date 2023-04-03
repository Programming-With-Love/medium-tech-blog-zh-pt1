# 单线镖，什么？—第二部分

> 原文：<https://medium.com/globant/single-thread-dart-what-part-2-a5592bef5213?source=collection_archive---------1----------------------->

如果您还没有阅读本系列的第一部分，请在继续之前阅读这里。

 [## 单线镖，什么？

### 我知道你们中的一些人可能会对我说有 Async Await 和 RxDart，所以不要担心兄弟。我当时……

medium.com](/@parthdave93/single-thread-dart-what-ccbca2543ae9) 

孤立就是一根线。隔离不共享内存，因为我们已经介绍过，主要隔离有其事件循环，其他隔离也是如此，因此要发送和接收处理过的数据，您需要使用端口来来回回地通信。

> *“孤立”在扑动中不共享内存。就交流而言，不同“隔离”之间的交互是通过“消息”进行的。*

您可以通过两种方式创建隔离:

1.  使用计算功能，它将处理所有数据发送和接收的通信过程。
2.  编写您自己来回发送和接收数据的所有逻辑。

> *隔离是 TCP 套接字的一种。我们需要为此创建一个握手机制。还有另一个原因是，正如我们之前定义的，Isolate 不共享内存，您需要使用端口和消息来来回回地传递数据。这就是为什么我们需要使用某种握手。*

创建隔离有一些规则

1.  隔离需要有一个非静态的顶级函数。
2.  如果你使用`compute`函数或`Isolate.spawn`函数，现在函数必须有一个参数。你可能会问为什么？检查方法签名。

```
external static Future<Isolate> spawn<T>(
    void entryPoint(T message), T message,
    {bool paused: false,
    bool errorsAreFatal,
    SendPort onExit,
    SendPort onError,
    @Since("2.3") String debugName});
```

检查`entryPoint(T message)`方法调用，它需要 T 消息，所以你需要有一个单参数函数。

3.每个隔离区占用大约 2MB，它很轻，所以要小心使用。[隔离内存大小和分配](https://www.youtube.com/watch?v=M8jGSkACneE&t=27m25s) -查看更多详细信息

4.从隔离区传递的每个消息都需要消息大小* 2 的内存，因为隔离区不共享内存，所以在系统上将有两个副本，所以如果您有 1 GB 的文件，并且想要从一个隔离区传递到第二个隔离区，则需要设备上 2Gb 的内存(在设备上，我指的是 RAM -查看参考链接以了解详细信息)。

5.即使您已经完成了工作或从方法中执行了所有语句，隔离和端口也可能是活动的。所以我们有责任杀死隔离者并关闭港口。

代码(这个纯 Dart 代码没有颤振代码):

```
import 'dart:isolate';void main() async{
 startIsolate();
}
```

导入隔离包并调用 startIsolate 函数。我们将从创建用于来回传递消息的`ReceivePort`开始。要记住的是`ReceivePort Stream`只能使用一次，所以如果我们发送一次数据并从`receivePort.first`获取数据，现在当我们再次发送数据并试图从`receivePort.first`获取数据时，它将不起作用，并将抛出一个错误语句:`Unhandled Exception: Bad state: Stream has already been listened to.`最常见的流形式一次只能监听一次。如果您尝试添加多个侦听器，它将抛出一个异常。因此，我们将使用`receivePort.first`来来回回地获取`SendPort`，如果我们想使用一个`ReceivePort`来获得更好的优化，我们需要使用该类的 listen 方法。握手的步骤(将给出虚拟的例子来清楚地了解什么类是用来做什么的)

1.  创建接收端口对象(例如:接收端口有点像公开的服务器)
2.  使用发送端口生成隔离(例如:发送端口有点像套接字 8080 端口，我们在那里启动套接字，其他人可以通过调用网站来访问它……:8080，服务器将在该端口上对该请求做出响应)
3.  在 Isolate 中，我们需要使用握手，我的意思是我们有两个线程，我们需要传递消息，因此我们需要一些流来监听事件的双方，因此我们将首先传递发送端口，以确定我们需要将消息发送到哪里。
4.  因此，在通过 spawn 创建了 Isolate 之后，我们将等待 Isolate 发送其发送端口，我们将向其 ping 消息。
5.  在 Isolate 端，我们将再次创建 ReceiverPort，并向我们在方法参数中传递的发送端口发送一条消息，该消息将是该 Isolate 的发送端口。
6.  记住只使用一次`receiverPort.first`，我们将得到发送端口。
7.  现在，一旦我们获得所有端口，就像套接字开放通道一样，我们将来回发送消息。

```
startIsolate() async{
 //create ReceivePort
 ReceivePort receivePort = ReceivePort();
 //receivePort.sendPort is our destination for receiving message
 var isolate = await Isolate.spawn(calculateFunction, receivePort.sendPort); SendPort sendPort = await receivePort.first;
 // we got Isolate's SendPort or should I say Message destination
}calculateFunction(SendPort sendPort) async{
 var calculateFunctionReceivePort = ReceivePort();
 sendPort.send(calculateFunctionReceivePort.sendPort); await for (var msg in calculateFunctionReceivePort){
  var data = msg[0];
  SendPort replyTo = msg[1];
  replyTo.send("$data from Isolate");
  print("Received inside Isolate: $data");
 }
}
```

一旦我们获得了隔离的发送端口，我们将发送一条消息并监听隔离响应。

```
import 'dart:isolate';void main() async{
 startIsolate();
}startIsolate() async{
 ReceivePort receivePort = ReceivePort();
 var isolate = await Isolate.spawn(calculateFunction, receivePort.sendPort); SendPort sendPort = await receivePort.first;
 receivePort.close(); // closing the port , do not forget to close it.
 var msg = await sendStreamData(sendPort, "Test Data");
 print(msg); Future.delayed(Duration(seconds: 1),()async{
  msg = await sendStreamData(sendPort, "Test Data 2");
  print(msg); isolate.kill(priority: Isolate.immediate);
 });
}sendStreamData(SendPort sendPort, String msg) async{
 ReceivePort sendStreamDataPort = ReceivePort();
 sendPort.send([msg, sendStreamDataPort.sendPort]);
 var response = await sendStreamDataPort.first;
 sendStreamDataPort.close();
 return response;
}calculateFunction(SendPort sendPort) async{
 var calculateFunctionReceivePort = ReceivePort();
 sendPort.send(calculateFunctionReceivePort.sendPort); await for (var msg in calculateFunctionReceivePort){
  var data = msg[0];
  SendPort replyTo = msg[1];
  replyTo.send("$data from Isolate");
  print("Received inside Isolate: $data");
 }
}
```

输出:

```
Received inside Isolate: Test Data
Test Data from Isolate
Received inside Isolate: Test Data 2
Test Data 2 from Isolate
```

# 问题:

1.  我们为什么使用 ReceiverPort？或者孤立的接收器端口是什么？
2.  当我们有异步代码时，为什么我们需要使用 Isolate？
3.  隔离物能存活多久？以及如何杀死或关闭它？
4.  隔离共享内存吗？如果没有，那么如何传递数据？

参考链接:

1.  [隔离性能指标评测器示例和更多深度](https://www.youtube.com/watch?v=M8jGSkACneE)
2.  [颤振渲染流水线](https://www.youtube.com/watch?v=UUfXWzp0-DU&feature=youtu.be)
3.  [颤振隔离和事件循环解释](https://www.didierboelens.com/2019/01/futures---isolates---event-loop/)
4.  [隔离内存大小和分配](https://www.youtube.com/watch?v=M8jGSkACneE&t=27m25s)
5.  [从 Flutter Devs 中分离和事件循环介绍视频](https://www.youtube.com/watch?v=vl_AaCgudcY)
6.  [隔离内存大小和分配](https://www.youtube.com/watch?v=M8jGSkACneE&t=27m25s)

这就结束了颤振单线程镖系列。我希望你喜欢它。

你知道你可以按拍手吗👏按钮 50 次？你走得越高，我就越有动力为你们写更多的东西！

你好，我是帕斯·戴夫。noob 开发者和 noob 摄影师。你可以在 [Linkedin](https://in.linkedin.com/in/parth-dave-907b8177) 上找到我或者在 [GitHub](https://github.com/parthdave93) 上跟踪我或者在 [Twitter](https://twitter.com/the_parth_dave) 上关注我？

祝你有一个愉快的飞行日！