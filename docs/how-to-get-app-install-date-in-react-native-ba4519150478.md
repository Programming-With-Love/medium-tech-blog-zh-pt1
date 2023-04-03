# 如何在 React Native 中获取 app 安装日期？

> 原文：<https://medium.com/quick-code/how-to-get-app-install-date-in-react-native-ba4519150478?source=collection_archive---------0----------------------->

我已经在 React native 上工作了一段时间，我喜欢用它来构建应用程序。尽管 React native 带来了很多好处，但有时很难完成一些在 native 中可以轻松完成的琐碎任务。其中一件事就是找出 Android 和 iOS 中应用程序的安装日期。

我在 React Native 中搜索了很多库，想找出这个应用程序是什么时候安装在我的手机上的，但每次都失败了，因为目前没有办法在 React Native 中找到这个问题的解决方案。

然后听说了 React Native 里的原生模块。本机模块有助于我们集成本机代码以实现本机反应。众所周知，react native 是市场上的新技术，因此不能提供我们需要的所有原生功能，所以我们需要自己构建它。我们将通过使用 Android[和 iOS](https://facebook.github.io/react-native/docs/native-modules-android) 的原生模块来做到这一点。

![](img/b1aa58a22cdee87e9580a290c6202ffa.png)

Photo by [Nicole Honeywill](https://unsplash.com/photos/c7LoNDd4nKI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 入门指南

首先，使用 react native cli 创建 react native 项目。

```
react-native init NativeModuleExample
```

现在，我们需要在本机代码和 JavaScript 代码之间建立一座桥梁。React Native 中有两种环境，JavaScript 环境和原生环境。它们两者相互通信，并且这种通信是通过使用“桥”来完成的。该桥为这两种环境之间的双向和异步通信提供了一种方式。

因此，为了在我们的示例应用程序中创建这个桥，我们需要安装[react-native-create-bridge](https://github.com/peggyrayzis/react-native-create-bridge)包。

```
npm install --save react-native-create-bridge
```

然后，从我们项目的根目录，运行

```
react-native new-module
```

将会出现一个提示，询问您:

1.  您的桥接模块名称:`AppInstallDate`
2.  从桥类型中选择`Native Module`。
3.  选择默认平台，即 iOS/Obj-C 和 Android/Java。
4.  您想要存放 JS 文件的目录。如果它不存在，它会为你创建一个。

这将在我们的项目中创建一个文件`AppInstallDateNativeModule.js`。现在，是时候在我们的 react 原生项目中引入原生环境了。所以，为此，我们必须要有 Android studio 和 Xcode 来写原生代码。

## 对于 iOS/Obj-C

打开 Xcode，右键点击你的根项目文件夹名和`Add files to your NativeModuleExample`。选择与您的模块相关的文件并点击`Add`

现在是时候写一些本地代码了，所以对于这个 open `AppInstallDate.m`和 update 方法`RCT_EXPORT_METHOD`它应该看起来像这样。

```
RCT_EXPORT_METHOD(exampleMethod)
{
NSURL* urlToDocumentsFolder = [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];**__autoreleasing** NSError *error;NSDate *installDate = [[[NSFileManager defaultManager] attributesOfItemAtPath:urlToDocumentsFolder.path error:&error] objectForKey:NSFileCreationDate];NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];[dateFormatter setDateFormat:@"dd/mm/yyyy hh:mm:ss"];NSString *stringDate = [dateFormatter stringFromDate:installDate];[**self** emitMessageToRN:@"EXAMPLE_EVENT" :@{ @"date":stringDate }];}
```

## 适用于 Android/Java

打开 Android Studio，在`android/app/src/main/java/com/NativeModuleExample`中查找`MainApplication.java`，然后将您的包添加到 getPackages 函数中，如下所示:

```
**import** com.nativemoduleexample.appinstalldate.AppInstallDatePackage;@Override
  protected List<ReactPackage> getPackages() {
    return Arrays.<ReactPackage>asList(
        new MainReactPackage(),
        new AppInstallDatePackage()
    );
  }
```

打开`java/yourapp`中的`AppInstallDateModule`文件，更新`exampleMethod`函数，如下所示。

```
@ReactMethod
    **public void** exampleMethod () {
        *// An example native method that you will expose to React
        //* [*https://facebook.github.io/react-native/docs/native-modules-android.html#the-toast-module*](https://facebook.github.io/react-native/docs/native-modules-android.html#the-toast-module)**try**{**long** installedTime = *reactContext*.getPackageManager().getPackageInfo(*reactContext*.getPackageName(),0).**firstInstallTime**;*// Format the date time* String dateString = DateFormat.*format*(**"dd/mm/yyyy hh:mm:ss"**, **new** Date(installedTime)).toString();**final** WritableMap event = Arguments.*createMap*();
            event.putString(**"date"**, dateString);
            *emitDeviceEvent*(**"EXAMPLE_EVENT"**, event);}**catch**(PackageManager.NameNotFoundException e){
            e.printStackTrace();
        }
    }
```

# 最后的步骤

打开文件`AppInstallDateNativeModule.js from` 您的项目，即 NativeModuleExample，并将代码更新为:

```
//  Created by react-native-create-bridgeimport { NativeModules, NativeEventEmitter } from "react-native";const { AppInstallDate } = NativeModules;const AppInstallDateEmitter = new NativeEventEmitter(AppInstallDate);export default {
   exampleMethod() {
     return AppInstallDate.exampleMethod();
  },emitter: AppInstallDateEmitter,EXAMPLE_CONSTANT: AppInstallDate.EXAMPLE_CONSTANT};
```

现在，打开`App.js file, and update code`

```
import AppInstallDate from "./AppInstallDateNativeModule";componentWillMount() {
  AppInstallDate.emitter.addListener("EXAMPLE_EVENT", ({ date }) =>   {
  alert(date);
  });
}componentWillUnmount() {
  AppInstallDate.emitter.remove();
}onPress = () => {
  AppInstallDate.exampleMethod();
};render(){
 return(
         <TouchableOpacity onPress={this.onPress}>
           <Text>Click Me!!!</Text>
         </TouchableOpacity>
      );
}
```

# 结论

我们已经成功创建了一个获取应用安装日期的 React 本机应用。

我跳过了这个应用程序的一些样板代码，但你可以在 GitHub repo [这里](https://github.com/meenunegi/react-native-app-install-date)找到。

希望这个回购能帮助你理解 react native app 中的 NativeModule 链接。也许你可以从中获得灵感来建造一些令人惊叹的东西。请随时留下任何反馈，我一直在寻找更好的解决方案！