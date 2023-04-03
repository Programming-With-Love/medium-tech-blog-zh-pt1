# DeepLinkDispatch

> 原文：<https://medium.com/airbnb-engineering/deeplinkdispatch-778bc2fd54b7?source=collection_archive---------2----------------------->

## 一个简单的基于注释的库，用于在 Android 上更好地处理深层链接

由[克里斯蒂安·德奥尼耶](https://medium.com/u/290ddc158e63?source=post_page-----778bc2fd54b7--------------------------------)和[菲利佩·利马](https://medium.com/u/4aea84179d43?source=post_page-----778bc2fd54b7--------------------------------)

![](img/6138c17d632ace8b2b75cb5d24a460de.png)

深层链接提供了一种链接到网站或应用程序上特定内容的方式。这些链接是可索引的和可搜索的，并且可以向用户提供比典型的主页或屏幕更相关的信息。在移动上下文中，链接是链接到应用程序中特定位置的 URIs。

在 Airbnb，我们经常使用这些深层链接来链接房源、预订或搜索查询。例如，一个列表的典型深层链接可能如下所示:

```
airbnb://rooms/8357
```

这个深层链接直接绕过应用程序的主屏幕，打开蘑菇圆顶小屋的列表信息。其他深层链接指向其他非内容屏幕，如注册屏幕或关于应用程序如何工作的信息屏幕。

Android 通过清单中的声明支持深度链接。您可以添加定义深度链接模式和活动之间映射的意图过滤器。随后，任何具有注册方案、主机和路径的 URI 都将在应用程序中打开该活动。

虽然这种传统方法对于简单的深层链接使用来说很方便，但是对于更复杂的应用来说就变得很麻烦了。例如，您可以使用意图过滤器来指定路径模式，但这有一定的局限性。您不能轻易地指出在您要过滤的 URI 中预期的参数。对于复杂的深层链接，您可能必须编写一个解析机制来提取参数，或者更糟，将类似的代码分布在许多活动中。

[DeepLinkDispatch](https://github.com/airbnb/DeepLinkDispatch) 旨在帮助开发人员轻松处理深度链接，而无需编写大量样板代码，并允许您提供更复杂的解析逻辑来决定如何处理深度链接。您可以简单地用 URI 对活动进行注释，就像对其他库一样。看看上面的深度链接 URI 示例，您可以像这样注释一个活动，并声明一个您希望应用程序解析的“id”参数:

```
@DeepLink(“rooms/{id}”)
public class SomeActivity extends Activity {
  ... 
}
```

在用活动应该处理的深度链接 URI 注释特定活动之后，DeepLinkDispatch 将自动路由深度链接，并解析来自 URI 的参数。然后，您可以确定深层链接是否触发了 intent，并提取注释中声明的参数。这里有一个例子:

```
if (getIntent().getBooleanExtra(DeepLink.IS_DEEP_LINK, false)) { 
  Bundle parameters = getIntent().getExtras();
  String someParameter = parameters.getString("id");
  ...
}
```

在其核心，DeepLinkDispatch 生成一个简单的 Java 类作为注册中心，记录哪些活动注册到哪个 URIs，以及应该提取哪些参数。DeepLinkDispatch 还会生成一个 shim 活动，该活动会尝试将任何深度链接与注册表中的条目进行匹配——如果找到匹配项，它会提取参数，并使用填充了参数的 Intent 启动相应的活动。

此外，DeepLinkDispatch 旨在提供对深层链接使用的更深入的了解。默认情况下，Android 不会给出太多关于正在使用的深层链接或失败的深层链接的信息。DeepLinkDispatch 在应用程序类中为任何成功或不成功的深度链接调用提供回调，允许开发人员跟踪和纠正应用程序中任何有问题的链接。

这种回调的一个例子是:

```
public class SomeApp extends App implements DeepLinkCallback {
  @Override public void onSuccess(String uri) {
    // Handle or track a successful deep link here
  } @Override public void onError(DeepLinkError error) {
    // Handle or track and error here
  }
}
```

总之，如果你想要一个简单的方法来管理深层链接，使用 DeepLinkDispatch。使用注释声明深层链接和参数很简单，它将处理更复杂的解析并路由到您的活动，而无需您编写大量额外的代码。DeepLinkDispatch 还通过提供深度链接事件的简单回调让您更好地了解如何使用深度链接。

更多信息请看我们的 Github 页面:[https://github.com/airbnb/DeepLinkDispatch](https://github.com/airbnb/DeepLinkDispatch)

![](img/3913f6470a7657e02386189e67b4eb30.png)

## 在 [airbnb.io](http://airbnb.io) 查看我们所有的开源项目，并在 Twitter 上关注我们:[@ Airbnb eng](https://twitter.com/AirbnbEng)+[@ Airbnb data](https://twitter.com/AirbnbData)

*原载于 2015 年 6 月 30 日 nerds.airbnb.com**的* [*。*](http://nerds.airbnb.com/deeplinkdispatch/)