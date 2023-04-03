# 在 Flutter 和 Android 这两个应用程序之间共享数据。

> 原文：<https://medium.com/globant/share-data-between-two-applications-flutter-and-android-1bb6e2573103?source=collection_archive---------0----------------------->

第一个应用程序基于 Flutter，第二个应用程序基于 Android。

## 目的

第一个应用 *(App1)* 会发送***userId****(say 1111)*给第二个应用 *(App2)* 。第二个应用程序 *(App2)* 将使用一个 ***用户名*** *(比如 Rana Ranvijay Singh)* 向调用应用程序作出响应。

## 这是什么？

1.  在 Flutter 代码和 Android 代码之间建立一座桥梁。
2.  将数据从第一个应用程序发送到第二个应用程序，并读回响应。

## 1.在 Flutter 代码和 Android 代码之间建立一座桥梁。

首先，我们必须在第一个应用程序中使用一个叫做 [*平台通道*](https://flutter.dev/docs/development/platform-integration/platform-channels) 的东西来创建 Flutter 代码和 Android 代码之间的桥梁。

让我们从

> App1 > main.dart

```
import 'package:flutter/material.dart';

import 'app.dart';

void main() {
  runApp(App());
}
```

> App1> app.dart

```
import 'package:flutter/material.dart';

import 'homescreen.dart';

class App extends StatelessWidget {
  build(context) {
    return MaterialApp(
      title: 'First Application',
      home: Scaffold(
        appBar: AppBar(),
        body: HomeScreen(),
      ),
    );
  }
}
```

> App1 >主屏幕. dart

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

class HomeScreen extends StatefulWidget {
  State<StatefulWidget> createState() {
    return HomeState();
  }
}

class HomeState extends State<HomeScreen> {
  static const platform = const
  MethodChannel('going.native.for.userdata'); String _username = 'Data received: No data';
  String _userId = '1111';

  Widget build(BuildContext context) {
    return _body();
  }

  Widget _body() {
    return Container(
      margin: EdgeInsets.only(top: 100.0),
      child: Align(
        alignment: Alignment(0, 0),
        child: Column(
          children: <Widget>[
            Container(margin: EdgeInsets.only(top: 20.0)),
            _textView('Flutter app '),
            Container(margin: EdgeInsets.only(top: 80.0)),
            _textView('User ID: $_userId'),
            Container(margin: EdgeInsets.only(top: 20.0)),
            _textView(_username),
            Container(margin: EdgeInsets.only(top: 20.0)),
            RaisedButton(
              child: Text('Launch App2'),
              onPressed: launchApp2,
            )
          ],
        ),
      ),
    );
  }

  Widget _textView(String text) {
    return Text(text,textScaleFactor: 1.3,);
  }

  Future<void> launchApp2() async {
    String username;
    try {
      final result = await platform.invokeMethod('launchApp2');
      username = 'Data received: $result';
    } on PlatformException catch (e) {
      username = 'Data received: No data';
    }

    setState(() {
      _username = username;
    });
  }
}
```

以上代码是您的最终代码。

出于解释，这是我们创建通道的地方。

```
static const platform = const MethodChannel('going.native.for.userdata');
```

id***going . native . for . user data***用于标识 Android 端的频道。你可以把它改成任何东西，但是要确保 Flutter 和 Android 代码有相同的密钥。

点击**上的*启动 App2* 上的**按钮。我们使用这个通道来运行 Android 代码。

```
final result = await platform.invokeMethod('launchApp2');
```

这等待结果，然后向前移动。

在安卓方面。

> 附录 1 MainActivity.java

```
public class MainActivity extends FlutterActivity {
 private static final String INTENT_ACTION =
  "com.example.app1.userdata";

 private static MethodChannel.Result result;

 @Override
 protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  GeneratedPluginRegistrant.registerWith(this);
  new MethodChannel(
   getFlutterView(), 
   "going.native.for.userdata")
  .setMethodCallHandler(callHandler);
 } private MethodChannel.MethodCallHandler callHandler = new
 MethodChannel.MethodCallHandler() {

  @Override
  public void onMethodCall(MethodCall methodCall,
  MethodChannel.Result result) {
   MainActivity.result = result;
   launchApp2();
  }
 };

 @SuppressLint("NewApi")
 private void launchApp2() {
  Intent sendIntent = new Intent();
  sendIntent.setAction(INTENT_ACTION);
  Bundle bundle = new Bundle();
  bundle.putString("user_id","1111");
  sendIntent.putExtra("data",bundle);
  sendIntent.setType("text/plain");
  if (sendIntent.resolveActivity(getPackageManager())! = null) {
   startActivityForResult(sendIntent,11);
  }
 }

 @Override
 protected void onActivityResult(int req, int res, Intent data) {
  if (req == 11) {
   if (res == Activity.RESULT_OK) {
     Bundle bundle = data.getBundleExtra("data");
     String username = bundle.getString("username");
     result.success(username);
    }
   } else {
    super.onActivityResult(req, res, data);
   }
  }
}
```

以上是你的最终代码。

你需要有一个方法通道来接受 Flutter 代码的调用，并等待响应。通道***going . native . for . user data****将与颤振代码中使用的通道相同。*

```
*new MethodChannel(getFlutterView(), "going.native.for.userdata")
.setMethodCallHandler(callHandler);*
```

*调用处理程序将接收来自 Flutter 的调用，并将结果设置回结果中，以将其返回给 Flutter 代码。*

```
*@Override
 public void onMethodCall(...) {
   MainActivity.result = result;
   launchApp2();
 }

 result.success(username);*
```

## *2.将数据从第一个应用程序发送到第二个应用程序，并读回响应。*

*为了实现这一点，我们简单地使用隐式意图来启动第二个应用程序并发送包中的数据。我们将使用***startActivityForResult****并将在***on activity result****中读取响应。***

**你的***MainActivity.java***来自上面给出的第一个 app。**

**我们使用特定动作来启动第二个应用程序。**

```
**private static final String INTENT_ACTION = "com.example.app1.userdata";**
```

*****需要在第二个应用程序清单文件中针对该活动注册相同的操作。我给了<包名>.用户数据*****

> **App2 > AndroidManifest.xml**

```
**<activity android:name = ".UserLoginActivity">
 <intent-filter>
  <!--The action has to be same as App1-->
   <action android:name = "com.example.app1.userdata" />
   <category android:name = "android.intent.category.DEFAULT" />
   <data android:mimeType = "text/plain" />
  </intent-filter>
</activity>**
```

> **App2 > UserLoginActivity.kt**

```
**class UserLoginActivity : AppCompatActivity() {

 override fun onResume() {
  super.onResume()
  var userId = "No data received"
  val intent = intent
  if (intent != null
      && intent.action != null
      && intent.action.equals("com.example.app1.userdata")) 
  {
   val bundle = intent.getBundleExtra("data")
   if (bundle != null) {
    userId = bundle.getString("user_id")
    userId = " User id is $userId"
   }
  }
  tvMessage.text = "Data Received: $userId"
 }

 fun onClickLoginButton(view: View) {
  val intent = intent
  val bundle = Bundle()
  bundle.putString("username", "Rana Ranvijay Singh")
  intent.putExtra("data", bundle)
  setResult(Activity.RESULT_OK, intent)
  finish()
 }
}**
```

**完成了。**