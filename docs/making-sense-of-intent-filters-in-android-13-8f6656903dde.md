# 理解 Android 13 中的意图过滤器

> 原文：<https://medium.com/androiddevelopers/making-sense-of-intent-filters-in-android-13-8f6656903dde?source=collection_archive---------0----------------------->

![](img/30c32f6dddac1010289daca7c1a7860a.png)

在 Android 13 之前，当一个应用程序在其清单中注册一个导出的组件并添加一个`**<intent-filter>**`时，该组件可以由任何显式的意图启动——甚至是那些不匹配意图过滤器的意图。在某些情况下，这可能允许其他应用程序触发仅供内部使用的功能。

这种行为在 Android 13 中得到了更新。现在，当且仅当指定动作的意图与其声明的`**<intent-filter>**`元素匹配时，来自外部应用程序的意图才被交付给导出的组件。

# **违反直觉**

在现有的 Android 版本中，当意图**与组件声明的`**<intent-filter>**`元素**不匹配时，有两种方式将意图传递给组件(如`**<activity>**`):

1.  [显式意图](https://developer.android.com/guide/components/intents-filters#ExampleExplicit):只要发送者有权限，带有组件名称集的意图将总是被交付给组件。
2.  [意图选择器](https://developer.android.com/reference/android/content/Intent#setSelector(android.content.Intent)):将匹配意图设置为主意图的选择器时，主意图总是被传递。

开发者期望意图过滤器会影响所有意图，而不仅仅是一个子集。事实上，我们已经看到了很多关于意图过滤器的混乱。

# **一次回顾**

对于以下每个问题，您将看到以下内容:

*   创建一个意向对象，该对象被传递给`**startActivity()**`或`**sendBroadcast()**`。
*   一个`**<intent-filter>**`元素。

你的工作是回答这个问题:**意向会和意向过滤器匹配吗？**

首先，意图:

和意图过滤器:

不要！

如果一个意图不包含任何类别，Android 将把它视为`**CATEGORY_DEFAULT**`传递给`**startActivity**()`和`**startActivityForResult**()`的所有隐含意图。请注意，当且仅当意图用于启动活动时，才定义此行为。意图过滤器必须包括`**CATEGORY_DEFAULT**`，以便接收隐含的活动意图([文档](https://developer.android.com/guide/topics/manifest/category-element))。**请注意，这仅适用于开始活动时。**它们不适用于启动服务或发送广播。

为了正确匹配此示例，意图过滤器应实现如下:

再来一个怎么样？

意图:

过滤器:

又不行！

意图过滤器必须指定一个`<data>`元素来接受带有数据的意图([文档](https://developer.android.com/guide/components/intents-filters#DataTest))。与之匹配的应该是这样的:

# **一项风险得到改善**

在过去的一年中，我们发现了一个特殊的陷阱，我们认为我们可以帮助解决，让意图过滤器以更直观的方式工作。

让我们来看一个例子:

意图:

过滤器:

在现有的 Android 版本中，是的——意图确实与过滤器相匹配！这是因为显式意图不需要匹配声明的意图过滤器。当一个应用程序在其清单中声明一个导出的组件并添加一个`<intent-filter>`时，该组件可以由任何意图启动——甚至是那些不匹配意图过滤器的意图！这可能会导致许多应用程序出现漏洞。

这是我们在野外发现的一个例子:

易受攻击的应用代码:

受害者清单中声明的组件:

动作`**com.example.PRIVATE_INTERNAL_ACTION**`不应该在应用程序之外被访问，因为处理它的接收者(`**InternalReceiver**`)没有被导出。然而，由于`**ExternalReceiver**`不检查和保护传入的动作，恶意参与者可以通过以下方式触发内部功能:

对于以 Android 13+为目标的应用程序，这一点现在已经改变了。下一节将描述这些变化。

# **有什么变化？**

从面向 Android 13+的应用程序(意向接收方)开始，当且仅当意向与其声明的`**<intent-filter>**`元素匹配时，源自外部应用程序的所有意向才会被传递到导出的组件。

**一个大的警告:**如果一个意图没有指定一个动作，只要过滤器包含至少一个动作，它就通过了意图匹配测试。这意味着，如果传入的意图没有动作(当意图。`**getAction**()` 回报`**null**`)！

不匹配的意图被阻止。不实施意图匹配的情况包括以下几种:

*   交付给未声明任何意图过滤器的组件的意图
*   源自同一应用程序的意图
*   源自系统和根的意图。

虽然出于安全原因，这一变化很大——如果你依赖这一行为通过显式意图使你的应用程序与另一个应用程序交互，你可能会看到你的应用程序中的行为变化，即使你没有更新到目标 Android 13。

有了这些变化，假设受害者应用程序更新到目标 Android 13，我们前面示例中的恶意参与者不再能够触发我们在 Android 13 上的受害者应用程序的内部功能。但是，仍然**强烈建议**更新所有导出的组件，以检查并仅接受在旧版本 Android 上运行时允许保护您的应用程序的操作。我们更新后的示例如下:

# **我该怎么办？**

首先，我们应该注意到，只有当**意向接收应用程序针对 Android 13+** 时，才会启用强制执行。它不会影响内部传递给同一应用程序的意图。

# 意向发送方

如果您正在向另一个应用程序的**发送显式意图(带有显式组件名称集的意图)，请确保意图与组件的`<**intent-filter**>`元素相匹配。匹配逻辑与[解析隐含意图完全相同。](https://developer.android.com/guide/components/intents-filters#ExampleFilters)**

当试图开始一个带有不匹配的明确意图的活动时，您将立即得到一个带有消息的`**ActivityNotFoundException**`:

> 找不到显式活动类{组件名称}；您是否在 AndroidManifest.xml 中声明了此活动，或者您的意图是否与其声明的<intent-filter>不匹配？</intent-filter>

对于广播接收机和服务，除了标签“`PackageManager`”中的警告日志消息外，没有明显的故障信号:

> 意图与组件的意图过滤器不匹配:
> 
> 访问被阻止:

# 意向接收方

确保在`**AndroidManifest.xml**.`中声明您的组件期望并接受的所有可能的意图过滤器

除此之外，如果一个组件只需要显式的意图，如果不需要过滤，可以考虑移除所有的`**<intent-filter>**`元素。没有意图过滤器的组件将接受任何明确的意图。

# 测试

从开发者预览版 1 开始，这个变化已经在 Android 13 版本中启用了。

您可以使用开发者选项中的[兼容性框架](https://developer.android.com/guide/app-compatibility/test-debug) UI，或者通过在终端窗口中输入以下 ADB 命令来切换此更改:

请记住，这适用于应用程序*接收*意图；您不能禁用您的应用程序*发送给其他应用程序*的意图更改。

# 前进

因为这种变化甚至会在开发者瞄准 13 之前影响他们，所以如果你使用意图与外部应用交互，调查这种变化并**尽快协调更新是很重要的。有关更多信息，请查看我们描述意图和意图过滤器的[文档。](https://developer.android.com/guide/components/intents-filters)**