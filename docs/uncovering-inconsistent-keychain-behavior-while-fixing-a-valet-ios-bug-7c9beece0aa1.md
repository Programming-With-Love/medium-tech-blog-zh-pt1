# 修复代客 iOS 错误时发现不一致的钥匙串行为

> 原文：<https://medium.com/square-corner-blog/uncovering-inconsistent-keychain-behavior-while-fixing-a-valet-ios-bug-7c9beece0aa1?source=collection_archive---------4----------------------->

几个月前，一名开发人员从他们的 iOS 应用程序向 Square 发送了以下错误堆栈跟踪:

Valet error stack trace

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

这导致了一个[代客](https://github.com/square/Valet)的补丁，这是 Square 广受欢迎的开源库，用于管理 iOS 和 macOS 的钥匙链。在这个过程中，我们还发现了 iOS 和 macOS 上钥匙链行为的微妙但有趣的不一致性。

本帖将涵盖以下内容:

*   错误的详细情况；
*   iOS 钥匙串和代客服务概述；
*   代客调试和打补丁；和
*   我们如何偶然发现 iOS 和 macOS 中不同的钥匙串行为，以及造成这种行为的因素。

# 错误详细信息

```
NSInvalidArgumentException’, reason: ‘-[__NSCFData isEqualToString:]: unrecognized selector sent to instance 0x6080000b97b0
```

上面的错误消息表明， **isEqualToString** (一个 **NSString** 方法)正在一个 __ **NSCFData** 实例(一个 **NSData** 的私有子类，它没有实现 **isEqualToString** )上被调用。

进一步查看堆栈跟踪，不属于苹果 [CoreFoundation](https://developer.apple.com/documentation/corefoundation) 框架的第一个调用是:

```
-[VALValet migrateObjectsMatchingQuery:removeOnCompletion:]
```

VALValet 是 Valet 中的一个类，**migrateObjectsMatchingQuery**方法在这里调用 **isEqualToString** 一次[:](https://github.com/square/Valet/blob/2.2.3/Valet/VALValet.m#L426)

Error location in [VALValet migrateObjectsMatchingQuery:removeOnCompletion:]

就在代码调用 **isEqualToString** 和崩溃发生之前，从 keychain 条目中检索的 **kSecAttrAccount** 值被存储为 **NSString** :

```
NSString *const key = keychainEntry[(__bridge id)kSecAttrAccount];
```

然而，这个异常表明**键**实际上是一个 **NSData** 对象。

为什么 Valet 会假设 **kSecAttrAccount** 值是一个字符串？

在回答这个问题之前，先回答以下先决问题会有所帮助:

*   iOS 钥匙扣是什么？
*   什么是贴身男仆？
*   Valet 的**migrateObjectsMatchingQuery**方法是做什么的？

# iOS 钥匙扣是什么？

如果您已经熟悉 iOS 钥匙串，请随意跳过这一部分。

在应用程序启动之间保存数据的一种方式是通过 [UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults) ，这是一个带有简单 API 的键值存储:

```
let myUsername = "foo"**// Store value in UserDefaults** UserDefaults.standard.set("bar", forKey: myKey)**// Retrieve data** UserDefaults.standard.string(forKey: myKey)
```

iOS [**钥匙链**](https://developer.apple.com/library/content/documentation/Security/Conceptual/keychainServConcepts/iPhoneTasks/iPhoneTasks.html#//apple_ref/doc/uid/TP30000897-CH208-SW1) 是另一个持久选项，允许应用程序在设备上的加密 SQLite 数据库文件中存储敏感数据，如密码。可以通过使用预定义的属性字段(例如， **kSecAttrAccount** 、 **kSecClass** 、 **kSecAttrService** )来检索和存储钥匙串条目。

## iOS 钥匙串和用户默认设置有什么区别？

iOS 钥匙串是安全的存储(并且有一个难以使用的 API)，而 UserDefaults 则完全不安全(但是有一个易于使用的 API)。

UserDefaults 将数据存储在应用程序沙箱内的. plist(属性列表)文件的纯文本中。像 [iBrowse](https://www.ibrowseapp.com/) 和 [iExplorer](https://macroplant.com/iexplorer) 这样的实用程序可以很容易地检查设备上的文件，使得 UserDefaults 只适合存储用户偏好和设置等非敏感数据。

以下说明了在 iOS 钥匙串中创建和获取用户名/密码条目所需的最少代码量:

Storing and fetching iOS keychain entries

不仅钥匙串的使用冗长，而且每个与钥匙串交互的函数(例如， **SecItem*** )都会返回一组需要检查和处理的错误代码(参见 [SecBase.h](https://opensource.apple.com/source/Security/Security-57740.60.18/base/SecBase.h) 中的 **OSStatus** )。

# 什么是贴身男仆？

处理 keychain 很复杂，容易出错，并且通常包含大量样板代码。不幸的是，对于任何需要存储帐户信息或敏感数据的应用程序来说，这也是不可避免的。

为了使钥匙串管理更容易，Square 开发了 Valet 作为 iOS 和 macOS 的开源库，将钥匙串的复杂性隐藏在简单的键-值接口下。

通过代客在钥匙串中存储和检索用户名/密码类似于用户默认设置:

```
let myUsername = "my-username"; let myPassword = "my-password"let valet = VALValet(
    identifier: Bundle.main.bundleIdentifier!, 
    accessibility: .whenUnlocked
)**// Store username/password** valet?.setString(myPassword, forKey: myUsername)**// Retrieve password by username** valet?.string(forKey: myUsername)
```

现在我们有了更多的上下文，让我们回到我们对开发人员收到的错误的调查。

# Valet 的 migrateObjectsMatchingQuery 方法是做什么的？

如前所述，异常:

> NSInvalidArgumentException[_ _ NSCFData is equaltostring:]:无法识别的选择器

发生在 VALValet 的**migrateObjectsMatchingQuery**方法中。这个方法是做什么的？

**migrateObjectsMatchingQuery**方法的典型用法如下:

```
let valet = VALValet(
    identifier: Bundle.main.bundleIdentifier!, 
    accessibility: .whenUnlocked
)let query: [String: Any] = [
    kSecAttrService as String: Bundle.main.bundleIdentifier as Any,
    kSecClass as String: kSecClassGenericPassword as Any
]**valet?.migrateObjects(
    matchingQuery: query, 
    removeOnCompletion: true
)**
```

**migrateObjectsMatchingQuery:removeOnCompletion:**允许应用程序将现有钥匙串条目的管理转移到代客。如果应用程序在使用 Valet 之前使用钥匙串来保存数据，此方法会将现有钥匙串条目重新构造为一种格式，允许通过 Valet 的 simpler API 更新或获取这些条目。

```
let query: [String: Any] = [
    **kSecAttrService** as String: Bundle.main.bundleIdentifier as Any,
    **kSecClass** as String: kSecClassGenericPassword as Any
]
```

**查询**参数用于选择要迁移到代客的钥匙串条目，并使用标准钥匙串属性字段名称作为过滤标准。

迁移允许应用程序过渡到代客，而无需强制用户重新输入帐户密码或储存在钥匙串中的其他信息。

# 仔细查看错误发生的位置

重新访问**migrateObjectsMatchingQuery**中抛出异常的代码块:

migrateObjectsMatchingQuery: sanity checking Keychain Entries

在迁移之前，这部分代码负责对作为迁移候选对象的现有钥匙串条目进行完整性检查。例如，要符合条件，现有条目必须具有 **kSecAttrAccount** 和 **kSecValueData** 值。

逐行解释:

```
for (NSDictionary *const keychainEntry in **queryResultWithData**)
```

**queryResultWithData** 是一个数组，包含使用提供给**migrateObjectsMatchingQuery**方法的**查询**搜索钥匙串后返回的条目的数据。

```
**NSString** *const **key** = keychainEntry[(__bridge id)kSecAttrAccount];
```

每个条目的 **kSecAttrAccount** 值存储在 **key** 变量中。**注意这里假设值是一个字符串。**编译器允许这样做，因为 **NSDictionary** 值是 **id** 指针，这允许隐式转换。

```
if ([**key isEqualToString:VALCanAccessKeychainCanaryKey]**) { 
    // We don't care about this key. Move along.
    continue;
}
```

在对每个条目进行完整性检查之前，将**键**与**valcanaccesskeychainkanarykey**进行比较，如果发现匹配，则跳过所有检查。

**valcanaccesskeychainkanarykey**是一个字符串常量，在执行探索性(金丝雀)钥匙串条目插入时，由代客存储在条目的 **kSecAttrAccount** 字段中。这些插入验证了在初始化代客实例时指定了正确的钥匙串可访问性。

如果查询结果包含金丝雀条目，我们希望跳过对该条目的任何健全性检查。金丝雀条目是一种特殊情况，不会导致任何迁移健全性检查失败。

当**键**(条目的 **kSecAttrAccount** 字段中的值)为 **NSString** 时，一切正常。然而，这个异常证明了**键**可能是一个 **NSData** 对象。

问题是 Valet 假设一个条目的 **kSecAttrAccount** 值将是一个 **NSString** 对象:

```
**NSString** *const **key** = keychainEntry[(__bridge id)kSecAttrAccount];
```

# 为什么 Valet 假设 kSecAttrAccount 值是 NSString？

与 SecAttrAccount 属性相关联的[钥匙串值的 Apple 文档提供了以下描述:](https://developer.apple.com/documentation/security/ksecattraccount)

> 一个键，其值是一个**字符串**，表示该项目的【钥匙串条目的】帐户名。

由此看来，假设 **kSecAttrAccount** 值总是字符串似乎是合理的。

此外，从这个代客使用的例子:

```
let myUsername = "my-username"; let myPassword = "my-password"let valet = VALValet(
    identifier: Bundle.main.bundleIdentifier!, 
    accessibility: .whenUnlocked
)**// Store username/password** valet?.setString(myPassword, forKey: myUsername)**// Retrieve password by username** valet?.string(forKey: myUsername)
```

Valet 只接受密钥的字符串参数(在上面的示例中为 **myUsername** )，然后这些参数作为 **kSecAttrAccount** 值存储在钥匙串中。

由 Valet 创建的任何条目都遵守 Apple 对 **kSecAttrAccount** 值的类型限制。然而，对于现有的钥匙串条目，代客作出有缺陷的假设:

*   所有开发人员都遵守这些钥匙串类型限制；或者
*   框架或操作系统对钥匙串数据实施类型限制。

# 重现错误

然而，开发人员收到的异常表明，有可能将 NSData 对象存储在钥匙串条目的 **kSecAttrAccount** 字段中，这与 Apple 的文档相矛盾。

下面的代码片段证明了这一点，并且在尝试迁移 keychain 条目时抛出了相同的异常。

Reproducing the “unrecognized selector” exception

# 修复:添加类型检查

由于不能再假设 **kSecAttrAccount** 总是一个字符串，我们需要在字符串比较之前添加一个类型检查，以避免将 **isEqualToString** 消息发送给非 **NSString** 对象。

```
if (**[key isKindOfClass:[NSString class]] &&** [key isEqualToString:VALCanAccessKeychainCanaryKey]) { // We don’t care about this key. Move along.
    continue;
**}**
```

因为代客总是使用 **kSecAttrAccount** 字段中的 **NSString** 值来创建条目，所以代客金丝雀条目的键将总是一个 **NSString** 。因此，添加这种类型检查是一个有效的需求。

# 一个无意的发现:macOS 和 iOS 钥匙链的不一致行为

接下来，我们添加了下面的测试来验证我们的更改(代客是在 Objective C 中编写的):

```
NSString *identifier = @"my_identifier";
NSData ***dataBlob** = [@"foo" dataUsingEncoding:NSUTF8StringEncoding];

NSDictionary ***keychainData** = @{ 
    (__bridge id)kSecAttrService : identifier, 
    (__bridge id)kSecClass : (__bridge id)kSecClassGenericPassword, 
 **(__bridge id)kSecAttrAccount : dataBlob,**    (__bridge id)kSecValueData : dataBlob
};
```

测试故意存储**数据块**，它是 **keychainData** 的 **kSecAttrAccount** 字段中的 **NSData** 对象，它被插入到钥匙链中。正如所料，该修复防止了异常被抛出。

有趣的是，最后一个断言通过了 iOS 目标，但是**在 macOS** **环境**中失败(代客可以管理 iOS 和 macOS 钥匙链):

```
NSError *error = 
    [self.valet migrateObjectsMatchingQuery:query removeOnCompletion:NO];

**XCTAssertNil(error); // Passes for iOS, fails for macOS**
```

在 macOS 目标上运行测试导致了一个**VALMigrationErrorKeyInQueryResultInvalid**错误，表明钥匙串条目的 **kSecAttrAccount** 值为 **nil** 。回想一下，任何作为迁移候选的键都必须在 **kSecAttrAccount** 字段中有一个值。

如果类型不正确，在 macOS 上插入包含 **kSecAttrAccount** 数据对象的条目会导致 **kSecAttrAccount** 值无法持久化。另一方面，无论类型是否正确，iOS 都会持久保存 **kSecAttrAccount** 值。

在两个操作系统中插入 **keychainData** 后检查钥匙串条目，确认 **kSecAttrAccount** 数据值成功存储在 iOS 上，但未存储在 macOS 上:

```
**macOS:**(lldb) po keychainEntry
{ **/** MISSING ACCT VALUE !!! **/**    cdat = "2017-08-12 11:37:39 +0000";
    class = genp; // kSecClass
    labl = "Keychain_With_Account_Name_As_NSData";
    mdat = "2017-08-12 11:37:39 +0000";
    svce = "my_identifier"; // kSecAttrService
    "v_Data" = <666f6f>; // kSecValueData
    "v_PersistentRef" = <...>;
}**iOS:** (lldb) po keychainEntry
{
    **/** PERSISTED kSecAttrAccount VALUE !!! **/
**    **acct = <666f6f>;**
    agrp = "<*>.com.squareup.Valet-iOS-Test-Host-App";
    cdat = "2017-08-12 11:35:45 +0000";
    mdat = "2017-08-12 11:35:45 +0000";
    musr = <>;
    pdmn = ak;
    svce = "my_identifier"; // kSecAttrService
    sync = 0;
    tomb = 0;
    "v_Data" = <666f6f>; // kSecValueData
    "v_PersistentRef" = <67656e70 00000000 00000325>;
}
```

# 为什么 iOS 会持久化 kSecAttrAccount 值，而 macOS 不会？

苹果公司的一名开发人员在一个询问这个问题的帖子上发布了[这个回复](https://forums.developer.apple.com/thread/46429):

> **sec item API 有两个实现:**
> 
> **iOS 实现**，也用于 OS X 上的 iCloud 钥匙串
> 
> **OS X 实现**，这是一个兼容垫片，可以过渡到传统的钥匙链
> 
> 注意两者在达尔文都有。在安全项目中搜索 **SecItemUpdate_ios** 和 **SecItemUpdate_osx** ，看看这在幕后是如何工作的。
> 
> **iOS 实现基于 SQLite。如果你熟悉 SQLite，你会知道它的核心是无类型的，因此 iOS 实现必须进行自己的类型转换。这就是为什么该实现在类型方面有些宽容。然而……**
> 
> 重要说明:我强烈建议您使用头文件中指定的类型。其他类型也能工作，但那是实现的意外，而不是设计的特性。此外，由于有两个实现，这些事故并不总是排成一行。
> 
> 我意识到这条规则被苹果样本代码的不同部分打破了。

简而言之，这两个因素导致了这种行为差异:

*   iOS 和 macOS 的钥匙链不使用相同的数据存储。
*   两种操作系统的钥匙串实现是不同的。

## iOS 和 macOS 使用不同的钥匙串数据存储

SQLite 是支持 iOS keychain 的数据存储，它使用**类型关联**而不是严格的类型，这意味着 SQLite 中的列数据类型更多的是一种建议，而不是一种要求。

来自 [SQLite 文档](http://sqlite.org/datatype3.html):

> 任何列仍然可以存储任何类型的数据。只是有些列，如果可以选择的话，会更喜欢使用一个存储类而不是另一个……**具有文本相似性的列使用存储类 NULL、TEXT 或 BLOB** 来存储所有数据。

这解释了在 iOS 中观察到的行为，它允许将一个 **NSData** 对象存储在 **kSecAttrAccount** 列中，即使推荐的数据类型是字符串。

## iOS 和 macOS 上不同的钥匙串实现

iOS 和 macOS 都使用 **SecItemUpdate** 函数来更新钥匙串条目。它的实现可以在 [SecItem.cpp](https://opensource.apple.com/source/Security/Security-57740.60.18/OSX/libsecurity_keychain/lib/SecItem.cpp.auto.html) 中找到，它是开源的，是苹果安全框架的一部分:

从 **can_target_(ios|osx)** 布尔值和**secitem update _(IOs | OS x)**函数可以明显看出，每个操作系统的代码路径都是不同的。

对于感兴趣的读者， **SecItemUpdate_ios** 通过一个预处理宏别名到 [SecItem.c](https://opensource.apple.com/source/Security/Security-57740.60.18/OSX/sec/Security/SecItem.c) 中的 **SecItemUpdate** 函数。 **SecItemUpdate_osx** 在 [SecItem.cpp](https://opensource.apple.com/source/Security/Security-57740.60.18/OSX/libsecurity_keychain/lib/SecItem.cpp.auto.html) 中定义。整个安全框架可以在这里[下载。](https://opensource.apple.com/tarballs/Security/)

## 钥匙串发现摘要

总结本次调查中我们对钥匙链的了解:

*   如果**数据**对象存储在钥匙串条目的 **kSecAttrAccount** 字段中，iOS 和 macOS 都不会返回错误。
*   iOS 持久保存 **kSecAttrAccount** 值，而不考虑类型，而 macOS 不这样做。
*   很明显，macOS keychains 保存在类型安全数据库中，或者在 macOS codepath 中添加了 iOS 上不存在的类型检查。

# 修复测试

回到失败的代客测试，为了说明不同平台之间不同的钥匙串行为，以下:

```
**XCTAssertNil(error); // Passes for iOS, fails for macOS**
```

需要替换为:

```
# if **TARGET_OS_IPHONE**
    XCTAssertNil(error);# elif **TARGET_OS_MAC**/**
  iOS allows kSecAttrAccount NSData entries, 
  while OSX sets the value to nil for any non-string entry.
  */ **XCTAssertEqual(
        error.code, 
        VALMigrationErrorKeyInQueryResultInvalid
    );**# else
    [NSException raise:@"UnsupportedOperatingSystem" format:@"Only OSX and iOS are supported"];# endif
```

有了这个改变，测试现在通过了，可以在 Github 的[这里](https://github.com/square/Valet/pull/114)找到最终修复的链接。

# 向开发人员传达调查结果

在完成我们的调查后，我们告诉开发人员，崩溃是由于一个 **NSData** 对象存储在一个钥匙串条目的 **kSecAttrAccount** 属性中。

他们回应说，他们的应用程序一直存储着 **kSecAttrAccount** 字符串值，但使用的是第三方库，该库也存储着包含钥匙链中 **kSecAttrAccount** 值的凭证。极有可能此资料库正在插入有问题的钥匙串条目。然而，该库对应用程序的功能至关重要，不能被删除。

通常使用应用程序的主包标识符作为 **kSecAttrService** 属性值，它充当钥匙串中的名称空间。开发人员的代码和库很可能在同一个名称空间下存储条目。

开发人员应用程序中用于将钥匙串条目迁移到代客的代码:

```
let valet = VALValet(
    identifier: Bundle.main.bundleIdentifier!, 
    accessibility: .whenUnlocked
)**let query: [String: Any] = [
    kSecAttrService as String!: Bundle.main.bundleIdentifier as Any,   
    kSecClass as String!: kSecClassGenericPassword as Any
]**valet?.migrateObjects(
    matchingQuery: query, 
    removeOnCompletion: true
)
```

上面的查询返回应用程序捆绑包标识符下存储的所有通用密码，在本例中，它包括一个由第三方库存储的带有 **kSecAttrService** 数据对象的条目。

将开发人员的应用程序更新到包含修复程序的 Valet 版本为他们解决了问题。

我要感谢代客的创造者**埃里克·穆勒**和**丹·费德曼**，他们帮助调试和修复了这个问题。