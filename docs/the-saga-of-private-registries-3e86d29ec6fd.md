# 私人注册的传奇

> 原文：<https://medium.com/compendium/the-saga-of-private-registries-3e86d29ec6fd?source=collection_archive---------5----------------------->

轻松认证私人 NPM 注册中心

![](img/94a27ce2c9c98c2239b905c8089afe00.png)

你曾经不得不使用私人 NPM 软件包吗？如果您有，您可能知道如果它们不在默认的 NPM 注册表上托管会有多大的麻烦。设置 NPM 指向注册表；处理认证；确保每个人都有正确的访问权限…这可能需要一段时间，而且每次有人需要访问或您使用新设备时，您都必须重新开始。

我几乎放弃了尝试使用 NPM 以外的注册表，直到我发现你可以使用 [Google Artifact Registry](https://cloud.google.com/artifact-registry) 作为 NPM 注册表，并决定再试一次，因为我是 GCP 产品的绝对粉丝。

在设置注册中心之后，我得到了一个关于如何认证和将我们的范围指向这个注册中心的逐步说明。*“又来了……”*，当我开始想象我的同事为了使用我们的内部软件包而不得不经历同样的设置时，我心想。*“我们为什么不用 git 子模块呢？”*；*“我没有。我的回购中的 npmrc 文件！”*；*“这个为什么没有合流页面？”*。

考虑到可怕的预测，我开始研究自动化这一点，因为它基本上只是运行 NPM 配置命令并从 gcloud 命令复制粘贴令牌。我们在我的工作场所 Computas Denmark 使用 yarn，所以我开始查看 Yarn 配置文件以摆脱 scope 设置。没过多久，我就找到了这样做的方法:

```
npmScopes:
  <org>:
    npmAlwaysAuth: true
    npmPublishRegistry: "https://<location>-npm.pkg.dev/<org>/<repository>/"
    npmRegistryServer: "https://<location>-npm.pkg.dev/<org>/<repository>/"
```

这是一件与众不同的事情，它需要最少的文档。

现在来看认证。我试着通过配置寻找使用谷歌认证的方法，但是一无所获。我在谷歌文档中搜寻神器注册表，希望找到一个金块，推动我走完最后一英里，把它带回家。唉，到了圣诞节，我还是没有找到这样做的方法。

它每天都困扰着我，我仍然没有想出如何自动化认证，以至于我不能享受圣诞节。当我的家人在雪地里玩耍，烤姜饼，在壁炉旁喝热巧克力的时候，我被粘在屏幕上，脑子里只有两件事:肉汁和认证问题。按照这个顺序。

总之，我写了一个插件:[https://github.com/andyclausen/yarn-plugin-gcp-auth](https://github.com/andyclausen/yarn-plugin-gcp-auth)。您也可以通过运行以下命令来使用它:

```
yarn plugin import [https://github.com/AndyClausen/yarn-plugin-gcp-auth/releases/latest/download/plugin-gcp-auth.js](https://github.com/AndyClausen/yarn-plugin-gcp-auth/releases/latest/download/plugin-gcp-auth.js)
```

它将使用来自 gcloud 的令牌进行身份验证，如果您从 GCP 运行，则使用来自 Google 元数据服务器的令牌进行身份验证。

最后，有了这个设置，我们只需要对每个存储库进行一次配置，插件会处理剩下的事情。通过谷歌的 IAM 政策，我们可以控制谁可以访问我们的内部包，无论是唠叨的同事还是安静的服务帐户。