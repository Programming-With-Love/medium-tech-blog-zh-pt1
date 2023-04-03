# 使用云功能 DIY Google 云存储复制

> 原文：<https://medium.com/google-developer-experts/diy-google-cloud-storage-replication-using-cloud-functions-51ae3a7124a7?source=collection_archive---------1----------------------->

![](img/6b14515c405ed44f761eca67885172ac.png)

Image from Google

9 月 14 日，谷歌云开放了多伦多地区，作为一名多伦多人，我欣喜若狂——期待已久的第二个加拿大地区终于到来了！但是，虽然这提供了更多的解决方案，但其中一些解决方案需要更多的创造力和对构成谷歌云平台(GCP)的基本构件的理解。

# 介绍

我是一名云顾问，我们的客户主要是受监管的行业，如银行、卫生或政府。大多数都有必须遵守的法规遵从性，最常见的是数据必须保留在加拿大境内，因为通常涉及个人身份信息(PII)和/或个人健康信息(PHI)的处理。拥有第二个区域意味着备份和灾难恢复(DR)有了一条可行的前进道路。然而，在谷歌云存储(GCS)的情况下，加拿大地区桶还没有双区域或多区域复制选项(从我与一些谷歌员工的交谈中，这似乎没有出现在他们的路线图上)。正如你可能从我的文章标题中猜到的那样，我将展示一个解决方案，通过使用[云功能](https://cloud.google.com/functions)来完成你自己的复制。

# 先决条件

在 GCP 端，您需要启用 API:

*   **云函数 API**
*   **云构建 API**

我还设置了两个区域 GCS 存储桶——蒙特利尔的源存储桶和多伦多的复制存储桶。我构建 GCS buckets 的方式是在相同的名称上添加一个位置后缀(即 *my-bucket-mtl* 和 *my-bucket-tor* )。云函数将从源存储桶中去除源位置，并附加复制的存储桶位置。这绝不是一个硬性要求，但是您应该以某种方式组织您的存储桶的命名，以允许云函数很容易地重用，几乎不需要修改。

云存储事件只会在同一个项目中触发云函数，所以要确保你的源 GCS bucket 和云函数在同一个项目中。对于这个例子，复制的 GCS bucket 也将在同一个项目中。

您的云功能使用的服务帐户(默认:*PROJECT _ ID*@ appspot . gserviceaccount . com)将需要**roles/storage . object admin**权限，因为它将在复制的 GCS 存储桶中创建/删除对象。

# 代码

为这两个云函数分别创建一个文件夹。它们都应该包含一个`requirements.txt`和`main.py`(我使用的是 Python 运行时):

*   `requirements.txt`(两种功能通用)

```
google-cloud-storage==1.42.3
```

*   `main.py` ( *copy_to_tor_gcs* 函数)

用`gcloud`部署函数，指定源桶为触发资源，触发事件为`google.storage.object.finalize`，一旦创建(或覆盖)对象就会触发:

```
gcloud functions deploy copy_to_tor_gcs \
  --runtime python39 \
  --entry-point copy_to_gcs \
  --trigger-resource my-bucket-mtl \
  --trigger-event google.storage.object.finalize \
  --region northamerica-northeast1 \
  --memory 128MB
```

*   `main.py`(*delete _ from _ tor _ GCS*函数)

对于 *delete_from_tor_gcs* 函数，触发事件将改为`google.storage.object.delete`。如果您想了解更多可用的其他类型的触发器，请参见 [GCS 触发器](https://cloud.google.com/functions/docs/calling/storage)文档页面。

# 为什么此解决方案是合规的

云功能是一种区域性资源，但是利用 HTTP 触发器的可能不是，因为数据/有效负载将通过谷歌前端(GFE)传递，这可能在您的地区或国家之外。在我们的例子中，我们使用的是通过[发布/订阅通知](https://cloud.google.com/storage/docs/pubsub-notifications)发送的 GCS 事件触发器。Pub/Sub 可能是一项全球服务，但它会选择最近的允许区域，并且随着您的 GCS 存储桶和云功能本地化到加拿大区域，Pub/Sub 也应该使用这些区域之一。

# 局限性/陷阱

1.  本文给出的解决方案假设源和复制的 bucket 都属于同一个项目，但是从技术上来说，复制到不同项目中的 GCS bucket 也是可能的，但是这需要更多的技巧，我不会在本文中讨论。也许是未来文章的主题。
2.  只有在部署云功能后添加或删除的对象才会发生复制(和删除)。如果您正在将此应用于预先存在的 GCS 铲斗，请确保首先运行`gsutil rsync`。
3.  如果源(蒙特利尔)GCS 存储桶被删除，它将首先删除其内容，这将触发复制(多伦多)GCS 存储桶中每个相应对象的删除。如果您有一个双区域或多区域存储桶，那么存储桶删除会让您得到相同的结果，但是在您决定删除源存储桶并希望复制的存储桶及其所有内容保持不变的情况下，注意这种行为是很重要的。

# 放弃

虽然我在这里描述了一种使用云功能复制 GCS 存储桶的方法，并提供了关于事件和数据如何传输的见解，但我自己没有经历过任何云存储区域故障，因此我不知道它是否会触发删除事件——常识表明，区域范围的网络丢失(例如)不应在受影响的区域中注册为对象删除，但我也不知道我不知道什么。

# 奖金！

我创建了一个 [GitHub repo](https://github.com/Neutrollized/pulumi-diy-gcs-replication) ，它使用 [Pulumi](https://www.pulumi.com/) (另一个类似于 Terraform 的基础设施代码工具)部署我上面描述的设置。