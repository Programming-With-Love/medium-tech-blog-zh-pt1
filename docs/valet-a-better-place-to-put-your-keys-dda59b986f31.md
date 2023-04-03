# 贴身男仆:放钥匙的好地方

> 原文：<https://medium.com/square-corner-blog/valet-a-better-place-to-put-your-keys-dda59b986f31?source=collection_archive---------4----------------------->

## 安全地存储您的秘密，无需阅读苹果的 SecItem.h

*作者* [*丹费德曼*](https://medium.com/u/5755ab427632?source=post_page-----dda59b986f31--------------------------------) *和* [*埃里克穆勒。*](http://@TheProfessor22)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

# 钥匙链很恐怖

如果您曾经不得不仔细研究 SecItem.h 或与 Keychain 交互，您会很清楚这是一个可怕的 API。你传入一个字典，然后返回一个错误，让你知道至少有一个参数是错误的。想知道是哪个？没有骰子。一旦您最终验证了这些字典参数，仍然没有设置语义。箭筒中仅有的箭头是 add 和 update，但是如果 key 已经存在于 Keychain 中，那么添加 key/value 对将会失败。更糟糕的是，在 iOS 和 Mac 上，您应该传递给 SecItem*的键/值对之间存在微妙且未记录的差异。

痛苦不止于此。Keychain 在多线程环境中是一个松散的大炮，因为没有任何东西可以保证 Keychain 操作的原子性。即使您做的一切都是正确的，您也可能会被 SecItemUpdate 调用咬到，该调用错误地告诉您它已经成功更新了您传入的元数据——而事实上，它只更新了它应该更新的元素的子集。

# …但是你需要它

如果你把你的秘密储存在钥匙串以外的储存机制中，[你做错了](https://developer.apple.com/library/ios/documentation/Security/Conceptual/cryptoservices/KeyManagementAPIs/KeyManagementAPIs.html#//apple_ref/doc/uid/TP40011172-CH11-CHDDJIHA)。没有其他安全的地方来存储您的数据。有 NSUserDefaults、plists 等。但是这些地方储存任何需要安全等级的东西都差得可笑——问问任何使用过 iExplorer 的人就知道了。如果你认为你很好，因为你已经推出了自己的加密机制，你就违反了加密的基本规则:不要写自己的密码。

# 所以让我们来保管你的钥匙

[Valet](https://github.com/square/Valet) 有一个受 NSMapTable 启发的 API，在引擎盖下使用了 Keychain。你习惯的每个 Cocoa API 都有 set/get 语义，Valet 也是。代客确保原子读取和写入钥匙圈；您永远不会失败，因为您是在多线程上与 Valet 交互。如果您以前没有接触过 Keychain，我们保证您永远也不需要读取 SecItem.h，并且您会很快将这些令牌从您的 plists 迁移到 Valet 实例中。

如果你已经花时间学习了钥匙链的来龙去脉，我们很抱歉。但是好的一面是，Valet 的 API 已经在你的词典里了。哦，我们有一个简单的方法可以将您的旧钥匙链项目导入到代客。一旦迁移到 Valet，您就再也不需要为 errSec*处理编写另一个开关了。只要-canAccessKeychain 返回 YES，代客呼叫就永远不会失败。

更好的是，你永远不必担心踩坏或删除你不想要的秘密——每个贴身男仆都生活在一个沙盒里。如果两个 Valets 可以读或写同一个空间，他们将通过 isEqual:和==检查。每个代客都基于四个核心属性进行沙盒化:初始化器、标识符、可访问性和类。这个沙盒意味着从一个只有在手机解锁时才可访问的代客迁移到另一个在第一次解锁后可访问的代客保证有效。(您不会被糟糕的 SecItemUpdate 调用咬到。)当您在您的贴身男仆之间迁移这些项目时，这些项目实际上是从一个贴身男仆复制到另一个贴身男仆，而不是就地更新它们(我们估计这有大约 0.5%的无声故障率)。

最重要的是，代客可以轻松利用钥匙串的全部功能。想要在您编写的多个应用程序之间共享秘密吗？只需将您的共享访问组标识符传递到共享访问组初始化器中。想要通过 iCloud 将您的秘密同步到其他设备吗？我们有一个贴身男仆。想要将您的秘密存储在 Secure Enclave 上，要求触控 ID 或设备的密码来检索每个秘密吗？我们也有贴身男仆。

你还在等什么？交出钥匙，继续做你真正想做的工作。

*本帖是 Square 的“* [*周 iOS*](https://corner.squareup.com/2015/06/a-week-of-ios.html) *”系列的一部分。*

[](/@dfed) [## 丹·费德曼

### 请关注丹·费德曼在 Medium 上的最新活动。57 个人在关注丹·费德曼，看看他们的故事…

medium.com](/@dfed) [](http://twitter.com/theprofessor22) [## 埃里克·穆勒(@TheProfessor22) |推特

### 埃里克·穆勒的最新推文(@TheProfessor22)。iOS @Square(从注册到 skunkworks 再到开发者)，prev。…

twitter.com](http://twitter.com/theprofessor22)