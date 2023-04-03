# 服务人员生命周期

> 原文：<https://medium.com/walmartglobaltech/service-worker-lifecycle-20333ccd570a?source=collection_archive---------1----------------------->

![](img/cf63e0ccf770cf2c0ace6408f630b921.png)

Service Worker is symbolized with Gear (Source: [https://www.deviantart.com](https://www.deviantart.com/) )

看 gear GIF 不要害怕。这不是一个机械工程相关的博客。相信我，这个博客是一个软件工程博客。

![](img/dd4e4fd5420424cfe007944886327b92.png)

The motto of this blog series

# 什么是服务人员？

It 是**JavaScript 程序，可以在您的 web 应用程序中使用，使其能够在客户离线时为客户请求提供服务，并提供各种功能，使您的 Web 应用程序成为 [**渐进式 Web 应用程序**](https://developers.google.com/web/updates/2015/12/getting-started-pwa) 。**

**为了更好地了解服务人员，请阅读[这篇](/walmartglobaltech/mobile-first-an-introduction-to-service-workers-61da5cb5780c)博客。**

**在这篇博客中，我们将讨论服务人员的生命周期。**

# **生命周期**

## **注册服务工作者脚本**

**为了启动服务工作者生命周期，我们首先需要**在 JavaScript( *main.js* )中注册**我们的服务工作者脚本( *serviceWorker.js)* ，该脚本链接到您的应用程序的登录页面(*index.html*)。**

**下面给出的代码用于检查 ServiceWorker API 在您的浏览器中是否可用。如果 API 可用，我们可以注册我们的服务工作者脚本。**

**Code that Registers your Service Worker script**

**![](img/8c3e84610d6c17b2f69b205c7b98a2e1.png)**

**Service Worker Lifecycle (Source: [https://developers.google.com/web/fundamentals/primers/service-worker](https://developers.google.com/web/fundamentals/primers/service-workers)s)**

## *****安装事件*****

**这是服务工作者得到的第一个事件，每个服务工作者调用一次。这意味着，一旦注册了新的或更新的服务人员，它将被视为新的服务人员，并将获得自己的安装事件。
在安装步骤中，我们可以缓存静态资产。创建缓存存储后，可以缓存静态文件，如媒体资产(图像、音频文件)、CSS 文件、静态 HTML 页面。这一步是必不可少的，因为离线请求将被服务人员截获，并在此事件期间使用缓存的文件提供服务。**

**Code for Install Event**

> **在安装事件中，我们将一个 [**承诺**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 传递给 **event.waitUntil()** 。我们可以记录与缓存和安装事件完成相对应的消息。使用**open(cache name)***创建缓存，并使用**cache . addall(cache assets)***将文件添加到缓存中。****

## ***激活事件***

***在安装事件之后，必须调用激活事件**，以使服务工作器拦截来自该时间点的请求。注册服务工作者指令码的目前页面将不受已安装的服务工作者控制。我们需要重新加载该页面，以便将其置于服务工作者的控制之下。
Activate event，是我们可以管理由老服务工作者创建的缓存的地方。我们可以删除旧版本的 Service Worker 创建的以前的缓存。
***注意*** :为每个脚本更新创建新的缓存通常由开发人员完成。因此，我们需要为每个服务工作者脚本更新更改缓存的名称。*****

*****Code for Activate Event*****

> *****在这里，我们向执行缓存管理的**event . wait 直至】(**)传递一个承诺。在上述代码中，如果存在当前 **cacheNam** e 以外名称的缓存，将被删除。*****

*****激活事件后，Service Worker 将控制其 [**范围**](#283f) 内的所有页面。如果加载的站点处于离线状态，服务工作者将处理“**获取**”和“**消息**事件。如果连接处于空闲状态，则请求将由服务器提供服务，而 Service Worker 将被终止。*****

*****Handle Fetch Event by Serving file from Cache*****

## *****服务范围员工*****

*****我们的服务工作者的默认范围是相对于服务工作者脚本 URL 的`./`。这意味着，如果脚本位于根目录中，范围将是根目录中的所有文件、根目录中的包以及这些包下的所有文件。
对于范围之外的页面的获取请求不会被 Service Worker 截获。因此，我们应该将 Service Worker 脚本放在目录中的某个位置，在该位置下存在大多数必须脱机可用的页面。*****

## *****服务工作者更新*****

*****如果服务工作者指令码有任何变更，下次使用者连线至网站时，浏览器将会在背景中重新下载指令码。它将与现有的一款产品同时推出，并会有自己的 **Install** 活动。
如果旧的服务工作者正在控制页面，则新的服务工作者将进入**等待**状态。安装事件成功后，新的服务工作者将不会获得激活事件，直到它完全控制客户端(即旧的服务工作者不再服务任何客户端请求)。如果所有加载的页面都关闭，旧的服务工作进程将被终止，而新的工作进程将获得控制权。此时，新的服务人员会收到 **Activate** 事件。*****

*****为了避免**等待**状态，我们可以在 Install 事件中调用`self.skipWaiting()`。因此，新的服务工作人员会在安装完成后立即激活。*****

# *****结论*****

*****了解服务工作者生命周期非常重要。它可以帮助我们决定在哪里执行缓存和缓存管理的代码是正确的。当客户处于离线状态时，服务人员在服务客户请求方面非常有用。它还提供了 [***【推送通知】***](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web)[***后台同步***](https://developers.google.com/web/updates/2015/12/background-sync) 等多种功能，可以帮助网络开发者给客户提供丰富的线下体验。*****

# *****下一步是什么？*****

*****在了解了生命周期之后，我们应该看到一个实现服务工作者的例子。本系列的下一篇博客[将展示 Service Worker。它将帮助您更好地了解 Service Worker 的有用特性以及如何在 web 应用程序中实现这些特性。希望对你有所帮助🙂。](#)*****