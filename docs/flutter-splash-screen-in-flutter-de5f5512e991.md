# 颤动:闪屏

> 原文：<https://medium.com/globant/flutter-splash-screen-in-flutter-de5f5512e991?source=collection_archive---------8----------------------->

![](img/b5769239bc85628ccfc4b17f50137a44.png)

*   闪屏基本上是应用程序打开时出现的第一个屏幕。
*   它显示了一些介绍性的信息，如任何公司的标志。
*   它出现在应用程序完全加载之前。
*   要在 Flutter 应用程序中添加闪屏，我们应该遵循以下步骤
*   假设我们想在闪屏中显示一个图像 3 秒钟。
*   我们应该首先添加一个图像到我们的应用程序代码中
*   在 pubspec.yaml 中，我们应该添加一个图像，例如 flutterimage.png

```
assets:
 - images/flutterimage.png
```

*   现在，随着图像的添加，我们应该为闪屏创建一个 dart 类。

```
class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => new _SplashScreenState();
}
```

*   并且用于显示持续时间

```
startTimefun() async {
  var _duration = new Duration(seconds: 3);
  return new Timer(_duration, navigateToPage);
}
```

*   通过增加 3 秒时间，图像将显示 3 秒，它将导航到新的屏幕。
*   现在，从闪屏导航到主屏幕后显示新屏幕，将显示文本**“欢迎来到主屏幕！！!"**

```
class HomeScreen extends StatefulWidget {
  @override
  _HomeScreenState createState() => new _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text('HomeScreen Flutter'),
      ),
      body: new Center(
        child: new Text('Welcome to Home Screen!!!'),
      ),
    );
  }
```

![](img/2d68fdf2d273b7e868ff41fb426aaba3.png)

*   正如你在这个输出中看到的，第一个图像将显示 3 秒钟，然后主屏幕将出现。

谢谢大家！！

快乐阅读:)