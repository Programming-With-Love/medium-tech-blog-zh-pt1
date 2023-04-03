# 将 Firebase Cloud Firestore for JavaScript 中的查询与 RxFire 结合起来

> 原文：<https://medium.com/google-developer-experts/combine-queries-in-firebase-cloud-firestore-for-javascript-with-rxfire-6e07e95ca5b3?source=collection_archive---------3----------------------->

![](img/a93c8a0758543e2394f39b54981aeca5.png)

RxFire makes things so much easier

这是为云 Firestore 编写组合查询的更简单的方法。如果您喜欢只使用 RxJS，那么请使用 RxJS 查看使用 Firebase Cloud Firestore 执行或查询 JavaScript。

# RxFire 是什么？

RxFire 是一个独立于框架库，它帮助从 Firebase 项目通常包含的各种 Firebase 操作中创建 RxJS Observables，例如身份验证、实时数据库、Firestore、存储等。在本文中，我们将介绍如何使用 RxFire 将多个 Firestore 查询组合在一起，作为单个可观察的流。我将使用我以前的文章[中提到的相同示例，使用 RxJS](/google-developer-experts/performing-or-queries-in-firebase-cloud-firestore-for-javascript-with-rxjs-c361671b201e) 执行或查询 Firebase Cloud Firestore for JavaScript，以便您能够看到 RxFire 有多简单。

# 用 RxFire 创建 Firestore Observables

让我们看看在没有 RxFire 的帮助下是如何做到的，这样我们就可以做一个比较

现在有了 RxFire，我们可以简化这一点，因为我们不需要手动创建一个`Subject`并在收到新值时手动更新可观察值:

RxFire 包括可观察物生成功能，如`collection`，可用于直接从 Firestore `Query`创建可观察物。这看起来更简单和干净，对不对？

# 将可观测量组合成一个单一的流

由于我们在`RxFire/firestore`提供的`collection`的帮助下轻松地创建了可观测量，我们可以轻松地将可观测量组合在一起:

# 摘要

如果您看到自己一直在组合查询，我会推荐这个额外的依赖项来帮助您和您的代码活得更快乐！如果你担心额外的捆绑大小，RxFire 是树摇！

对于 Firebase SDK 的其他常用部分，RxFire 有其他可观察的创建方法，特别是在认证、存储和实时数据库方面。查看一下 [RxFire 的 GitHub。](https://github.com/firebase/firebase-js-sdk/tree/master/packages/rxfire)