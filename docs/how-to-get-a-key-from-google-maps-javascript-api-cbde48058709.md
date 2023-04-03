# 如何从谷歌地图 JavaScript API 获取密钥

> 原文：<https://medium.com/quick-code/how-to-get-a-key-from-google-maps-javascript-api-cbde48058709?source=collection_archive---------3----------------------->

## 如果您收到这个错误消息(Google Maps API 错误:MissingKeyMapError)，您将需要一个密钥。

1.  **这里是可以设置你的 key 的链接:**[https://developers . Google . com/maps/documentation/JavaScript/get-API-key？hl=en](https://developers.google.com/maps/documentation/javascript/get-api-key?hl=en)

> 您必须在 Google API 控制台上注册您的应用程序项目，并获得一个可以添加到您的应用程序中的 Google API 密钥。

![](img/a3f48ba6ef3c51e6adb6ee2af7832cb1.png)

接了一个项目就拿到了钥匙。

![](img/ed7b5e9864abb2300e2cf4c7de325557.png)

**2。将 API 密钥添加到您的应用程序中**

点击 **YOUR_API_KEY**

![](img/1126505a6d522d9c0afd7e6f304b2647.png)

它会自动创建一个凭证。

![](img/a0efe3d9e7d2c6fa0b0f026b769210f7.png)

太好了，你已经得到你想要的了！带钥匙的谷歌地图！！从编程社区推荐的[最佳 JavaScript 教程](https://coursesity.com/free-tutorials-learn/javascript)中在线学习 JavaScript。

```
<script async defer src="https://maps.googleapis.com/maps/api/js?key=**AIzaSyB27IflEAH4CLf825AvmiMbXE7NTK9obeA**&callback=initMap"
  type="text/javascript"></script>
```

> 然而，你可以为标准谷歌地图 JavaScript API 的用户做更多的工作

1.  转到[谷歌 API 控制台](https://console.developers.google.com/flows/enableapi?apiid=maps_backend,geocoding_backend,directions_backend,distance_matrix_backend,elevation_backend,places_backend&reusekey=true)。这里有您创建的所有凭据。

![](img/c99a7678e8c4c10e61f2d777ed310bad.png)

我建议你应该设置关键限制，以避免被滥用。

例如:

***/your-production-domain/***=>这将需要一个通配符。如果您的域没有通配符，您应该使用**your-production-domain/***来代替。

***。stage/your-staging-domain/***

![](img/446102edaf30d78a681325a90144cd9b.png)

太棒了，你做得很好，你会看到美丽的地图。

![](img/daa7705afb3e47704b8d5cca7fac02cd.png)

请点击👏按钮下面几下，以示支持！⬇⬇

谢谢！不要忘记**遵循下面的**快速代码。

> 在[快速代码](http://www.quickcode.co/)上找到各种编程语言的免费课程。获取 [Messenger](https://www.messenger.com/t/1493528657352302) 的新更新。