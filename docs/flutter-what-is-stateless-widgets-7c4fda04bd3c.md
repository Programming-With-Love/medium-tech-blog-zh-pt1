# Flutter:什么是无状态部件？

> 原文：<https://medium.com/globant/flutter-what-is-stateless-widgets-7c4fda04bd3c?source=collection_archive---------0----------------------->

![](img/a4883dc9d7cac86492eef21d93016b1c.png)

在开始无状态小部件之前，我们应该知道状态在 Flutter 中意味着什么？

*   状态是在小部件构建时可以同步读取的信息，并且在小部件的生命周期中可能会改变。
*   从不改变并且外观保持不变的小部件是无状态小部件。
*   没有需要管理的内部状态，也没有直接的用户交互。无状态窗口小部件 Text、IconButton、RaisedButton 的一些例子。
*   我们必须覆盖 build()方法并返回小部件。
*   无状态小部件不是动态的。
*   无状态小部件的 build()函数只被调用一次，任何变量、值或事件的改变都不能再次调用它。
*   为了重绘无状态小部件，我们需要创建小部件的一个新实例。
*   当用户界面依赖于对象本身的信息时使用。
*   无状态小部件的代码:

```
class MyApp extends StatelessWidget {
  [@override](http://twitter.com/override)
  Widget build(BuildContext context) {
    return MaterialApp(

      home: new Scaffold(
        appBar: AppBar(
          title: Text("Flutter App"),
        ),
        body: new Center(
          child: new Row(
            children: <Widget>[
              Text("Hello Flutter"),

        ),
       ), 
    );
  }
}
```

*   无状态小部件在显示静态内容或需要在应用程序中多次重新加载的页面时非常有用。

快乐阅读:)