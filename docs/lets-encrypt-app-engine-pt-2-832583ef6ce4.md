# 我们来加密& App Engine，pt。2

> 原文：<https://medium.com/square-corner-blog/lets-encrypt-app-engine-pt-2-832583ef6ce4?source=collection_archive---------8----------------------->

## 更简单的续订方法让我们在应用引擎站点上加密证书

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

*关于使用* [的更多背景，让我们用](https://letsencrypt.org/) [谷歌的应用引擎](https://cloud.google.com/appengine/) *加密 *，阅读原文* [为你的网站设置 https](/square-corner-blog/setting-up-https-for-your-e-commerce-website-with-lets-encrypt-and-google-app-engine-465a866790b5)*

到了更新我的谷歌应用引擎加密证书的时候，我想看看我是否能改进这个过程。对于我的第一次尝试，我尝试了 DNS 验证方法。我为我的域更新了适当的`TXT`记录，但是要么不能正确地完成，要么没有足够的耐心让缓存完全刷新，所以我回到了良好的 ol' https 验证。当我决定在过程中增加更多的自动化时，这是一个很好的选择，现在你可以从我的工作中受益🙂。

# 配置文件

如果您在同一台机器上有几个不同的项目，您可能想要做的第一件事就是为每个项目创建一个关注点分离。一个简单的开始是向您的项目添加一个文件夹，以帮助维护每个项目的所有相关项。

```
project/
--...
--letsencrypt/
----logs/
----config/
----work/
```

每个文件夹都可以用来将“让我们加密”过程的不同方面登记到我的存储库中。我们还希望开始将我们的项目与一个配置文件相关联，而不是记住所有需要的不同标志和命令。最初，我的配置文件如下所示:

现在我们可以运行`certbot --config ./cli.ini certonly`并直接进入将挑战响应上传到我的服务器的步骤。不幸的是，所有这些工作并没有使更新证书的过程变得更容易，只是使命令变得更短了。幸运的是，certbot 有一些新的锦囊妙计。

## **带验证挂钩的自动化**

Certbot 有几个新特性，可以帮助在不同于服务于您的网站的机器上自动完成`manual`证书请求过程。这些特征是`--manual-auth-hook`和`--manual-cleanup-hook`标志。它们允许您指定 shell 脚本，这些脚本将使用作为环境变量包含的相关细节来运行。你可以在[官方用户指南](https://certbot.eff.org/docs/using.html#pre-and-post-validation-hooks)中阅读关于 Certbot 钩子的所有信息。

因为我们设置了对所有东西使用一个配置文件，所以让我们为我的新的前后验证挂钩添加几行代码。

```
#pre & post validation hook:
manual-auth-hook ../scripts/auth.sh
manual-cleanup-hook ../scripts/cleanup.sh
```

## 授权. sh

这些脚本中的每一个都非常简单。auth 脚本将添加适当的文件来满足 url 挑战(& deploy my application)，而清理脚本将删除生成的文件。先来看`auth.sh`:

第一行只是创建了一个小输出，告诉我文件在哪里被创建，第二行将`CERTBOT_VALIDATION`环境变量的内容放入一个以`CERBOT_TOKEN`环境变量命名的文件中。然后部署指定项目的 App Engine 应用程序。`--quiet`标志防止在最大程度自动化的过程中需要任何确认。

## app.yaml

如果在项目的`app.yaml`中设置了正确的文件处理程序，就很容易应对 URL 挑战:

```
- url: /.well-known/acme-challenge/(.*)
  static_files: letsencrypt/\1.txt
  upload: letsencrypt/(.*\.txt)
```

任何在你的域名下发送的请求。众所周知的/acme-challenge，App Engine 将寻找适当命名的文本文件并返回内容。

## cleanup.sh

```
rm ../letsencrypt/*
```

验证之后，这个脚本只是清理文件，这样它们就不会破坏你的回购。

## 上传证书

一旦成功，您将获得一个`.pem`文件中的证书。您需要将它添加到应用引擎`PEM encoded X.509 public key certificate.`的[证书页面](https://console.cloud.google.com/appengine/settings/certificates)中。对于`Unencrypted PEM encoded RSA private key`，您首先需要修改私钥文件。您可能有一个 RSA 私钥，这对于 openssl 来说非常简单:

```
sudo openssl rsa -inform pem -in ./config/live/yourdomain.com/privkey.pem -outform pem > ./config/live/yourdomain.com/rsaprivatekey.pem
```

现在你可以上传你的新`rsaprivatekey.pem`并等待三个月直到你需要再次更新。😉

我希望你喜欢阅读我的应用引擎 SSL 冒险与让我们加密的第二部分！如果你有任何想法，或者在评论中提出改进的建议，请告诉我。