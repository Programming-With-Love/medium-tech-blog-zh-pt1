# Python 中的 AES 实现

> 原文：<https://medium.com/quick-code/aes-implementation-in-python-a82f582f51c2?source=collection_archive---------0----------------------->

用 Python 实现 AES 的演练

![](img/3769600082c52d93de2e99a454fa1efe.png)

Photo by [chris panas](https://unsplash.com/@chrispanas?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

你可能听说过在隔离期间发生的**家庭聚会**丑闻。在漫长的隔离期间，Houseparty 是一个非常棒的消磨时间的应用程序，它聚集了朋友，让他们在用手机视频聊天的同时玩小游戏，所有这些都是由 Houseparty 自己免费提供的。在大量网飞和 Spotify 账户开始遭到黑客攻击之前，一切都是娱乐和游戏。

有传言称，Houseparty 遭遇了数据泄露，他们的数据被第三方窃取，并被黑客用来访问个人账户。Houseparty 认为他们与黑客潜入人们帐户的浪潮无关。我们可能永远无法确定这里发生了什么，但是伤害已经造成了。我认为我们都可以从整个事件中学到一些东西，以防止和减少未来数据泄露的影响:

## 我们不够负责

我们没有认真对待我们的互联网隐私。普通用户在几个应用程序中重复使用密码和电子邮件，这些应用程序可能具有不同程度的信誉和安全性，却没有意识到其中隐含的危险。但是用户只能占这么多。作为开发人员，我们必须确保从我们的用户那里获得的所有数据都得到负责任的处理:数据不仅应该存储在安全的环境中，还应该用安全的算法**加密**，例如 **AES** 。

# 用 Python 实现 AES

幸运的是，我们不必从零开始实现 AES，但是如果您觉得辣，可以尝试一下。为了避免这样做，我们首先需要安装 **pycrypto** 库，这可以通过 pip 使用以下命令来完成:

```
pip install pycrypto
```

运行时应该没有错误。一旦安装了 **pycrypto** ，创建一个 python 文件并编写以下代码来导入我们需要的一切:

现在，我们将使用以下构造函数为我们的 AES 密码创建一个类:

让我们快速浏览一下构造函数，它接收一个任意长度的密钥。然后，我们继续从该密钥生成 256 位散列。散列基本上是一个给定长度的唯一标识符，在本例中是 32 个字符，任何输入的长度都一样。这使得您可以向构造函数传递您的姓名或短语，它将为您的密码生成一个唯一的 256 位密钥。我们还将`block_size`设置为 128，这是 AES 的块大小。

在我们继续为 AESCipher 类定义`encrypt` 和`decrypt`方法之前，让我们首先创建`__pad`和`__unpad` 方法。此外，Python 初学者可以从[最佳 Python 教程](https://blog.coursesity.com/pep8-python-code-writing-guide/)中学习，以加强学习。

## 衬垫

`__pad`方法接收要加密的`plain_text`，并为文本添加一个数字字节，使其成为 128 位的倍数。这个数字存储在`number_of_bytes_to_pad`中。然后在`ascii_string`中，我们生成填充字符，并且`padding_str` 将包含该字符乘以`number_of_bytes_to_pad`。因此，我们只需在我们的*纯文本*的末尾添加`padding_str`，这样它现在就是 128 位的倍数。

## 邮管处

以相反的方式，`__unpad`方法将接收解密的文本，也称为`plain_text`，并将删除`__pad` 方法中所有额外添加的字符。为此，我们首先必须识别最后一个字符，并在`bytes_to_remove` 中存储我们需要修剪`plain_text`末端多少字节，以便解包。

## 加密

有了这两个方法，让我们实现`encrypt`方法

`encrypt`方法接收要加密的`plain_text`。首先我们填充`plain_text`以便能够加密它。在我们生成一个新的随机数`iv`之后，它的大小是一个 AES 块，128 位。我们现在用我们的`key`、模式`CBC`和刚刚生成的`iv`用`AES.new`创建 AES 密码。我们现在调用我们的`cipher`的`encrypt` 函数，把它传递给我们的`plain_text`转换成位。然后，加密的输出放在我们的`iv`之后，并从位转换回可读的字符。

## 解释

既然我们可以加密文本，但我肯定我们希望能够解密它:

为了解密，我们必须回溯在`encrypt` 方法中完成的所有步骤。首先，我们将我们的`encrypted_text`转换成位，并提取`iv`，这将是我们的`encrypted_text`的前 128 位。就像以前一样，我们现在用我们的密钥，在 CBC 模式下，用提取的`iv`创建一个新的 AES 密码。我们现在调用我们的`cipher`的`decrypt`方法，并把它从比特转换成文本。我们用`__unpad`去掉填充，就这样！

`AESCipher`类最终应该如下所示:

# 最后的想法

您现在可以加密和解密您的数据！如果你想玩 AES 或者只是检查你的实现是否正确，试试这个在线的 [AES 密码](https://www.devglan.com/online-tools/aes-encryption-decryption)。如果你对 AES 是如何工作的感兴趣，请随意阅读我关于它的文章。

[](/quick-code/understanding-the-advanced-encryption-standard-7d7884277e7) [## 高级加密标准指南

### AES 演练:它是什么以及如何工作。

medium.com](/quick-code/understanding-the-advanced-encryption-standard-7d7884277e7) 

一如既往，如果需要，请随时联系我。本教程中的所有代码都可以在 GitHub 上免费获得。

[](https://github.com/PaburoTC/AES) [## PaburoTC/AES

### 在 GitHub 上创建帐户，为 PaburoTC/AES 开发做出贡献。

github.com](https://github.com/PaburoTC/AES) 

希望大家觉得这个教程有用，开心加密！

谢谢你。