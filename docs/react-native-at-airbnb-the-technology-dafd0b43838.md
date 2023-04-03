# Airbnb 的本土反应:技术

> 原文：<https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838?source=collection_archive---------0----------------------->

## 技术细节

![](img/4e8230b713c13a127ca0830f6423e078.png)

*这是* [*系列博文*](/airbnb-engineering/react-native-at-airbnb-f95aa460be1c) *中的第二篇，在这篇博文中，我们概述了 React Native 的体验以及 Airbnb 的下一步移动应用。*

在 Android、iOS、web 和跨平台框架的交叉领域中，React Native 本身是一个相对较新且发展较快的平台。两年后，我们可以有把握地说，React Native 在许多方面都是革命性的。这是移动领域的一次范式转变，我们能够从它的许多目标中获益。然而，它的好处并非没有重大的痛点。

# 什么效果好

## 跨平台

React Native 的主要好处是，您编写的代码可以在 Android 和 iOS 上本地运行。使用 React Native 的大多数特性能够实现*95–100%共享代码*和 *0.2%的文件是特定于平台的* (*.android.js/*.ios.js)。

## 统一设计语言系统(DLS)

我们开发了一种跨平台的设计语言，叫做 [DLS](https://airbnb.design/building-a-visual-language/) 。我们有每个组件的 Android、iOS、React Native 和 web 版本。拥有统一的设计语言有助于编写跨平台的特性，因为这意味着设计、组件名称和屏幕在不同的平台上是一致的。然而，我们仍然能够在适用的情况下做出适合平台的决策。例如，我们使用 Android 上的原生[工具栏](https://developer.android.com/reference/android/support/v7/widget/Toolbar)和 iOS 上的 [UINavigationBar](https://developer.apple.com/documentation/uikit/uinavigationbar) ，我们选择隐藏 Android 上的[披露指示器](https://developer.apple.com/ios/human-interface-guidelines/views/tables/)，因为它们不符合 Android 平台设计指南。

我们选择重写组件，而不是包装原生组件，因为为每个平台单独制作适合平台的 API 更可靠，并减少了 Android 和 iOS 工程师的维护开销，他们可能不知道如何正确测试 React Native 中的更改。然而，它确实导致了平台之间的碎片化，其中同一组件的本地版本和反应本地版本会不同步。

## 反应

React 是最受欢迎的 web 框架是有原因的。它简单而强大，可以很好地扩展到大型代码库。我们特别喜欢的一些东西是:

*   **组件:** React 组件通过定义良好的属性和状态来加强关注点的分离。这是 React 可伸缩性的主要贡献者。
*   **简化的生命周期:** Android 的生命周期以及在较小程度上 iOS 的生命周期是出了名的[复杂](https://i.stack.imgur.com/fRxIQ.png)。功能性的 reactive React 组件从根本上解决了这个问题，使得学习 React Native 比学习 Android 或 iOS 简单得多。
*   声明性的:React 的声明性本质有助于我们的 UI 与底层状态保持同步。

## 迭代速度

在 React Native 中开发时，我们能够可靠地使用[热重装](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html)在短短一两秒钟内测试我们在 Android 和 iOS 上的更改。尽管构建性能是我们原生应用的重中之重，但它从未接近我们使用 React Native 实现的迭代速度。在最好的情况下，本机编译时间为 15 秒，但对于完整版本，可能高达 20 分钟。

## 投资基础设施

我们开发了与本地基础设施的广泛集成。所有的核心部分，比如网络、i18n、实验、共享元素转换、设备信息、帐户信息以及其他许多内容都被封装在一个 React Native API 中。这些桥是一些更复杂的部分，因为我们希望将现有的 Android 和 iOS APIs 包装成对 React 一致和规范的东西。虽然使这些桥与新基础设施的快速迭代和开发保持同步是一个不断追赶的游戏，但是基础设施团队的投资使产品工作变得更加容易。

如果没有在基础设施上的大量投资，React Native 将会导致低劣的开发人员和用户体验。因此，我们认为，如果没有大量持续的投资，React Native 不能简单地附加到现有的应用程序上。

## 表演

React Native 最大的问题之一是它的性能。然而，在实践中，这很少成为问题。我们大多数的 React 原生屏幕感觉和我们的原生屏幕一样流畅。性能通常被认为是一个单一的维度。我们经常看到移动工程师看着 JS，认为“比 Java 慢”。然而，在许多情况下，将业务逻辑和[布局](https://github.com/facebook/yoga)移出主线程实际上会提高渲染性能。

当我们确实看到性能问题时，它们通常是由过度渲染引起的，并通过有效使用 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 、 [removeClippedSubviews](https://facebook.github.io/react-native/docs/view.html#removeclippedsubviews) 以及更好地使用 Redux 来缓解。

然而，初始化和首次渲染时间(如下所述)使得 React Native 在启动屏幕、深度链接方面表现不佳，并且增加了在屏幕之间导航时的 TTI 时间。此外，丢帧的屏幕很难调试，因为 [Yoga](https://github.com/facebook/yoga) 在 React 本地组件和本地视图之间转换。

## Redux

我们使用 [Redux](https://redux.js.org/) 进行状态管理，我们发现这很有效，可以防止 UI 与状态不同步，并支持跨屏幕的轻松数据共享。然而，Redux 因其样板文件而臭名昭著，并且具有相对困难的学习曲线。我们为一些常见的模板提供了生成器，但在使用 React Native 时，这仍然是最具挑战性的部分之一，也是混乱的来源。值得注意的是，这些挑战并不是本地特有的。

## 由本地人支持

因为 React Native 中的一切都可以通过本机代码来桥接，所以我们最终能够构建许多我们在开始时不确定是否可行的东西，例如:

1.  *共享元素转换*:我们构建了一个*<Shared element>*组件，它由 Android 和 iOS 上的原生共享元素代码支持。这甚至可以在本机屏幕和 React 本机屏幕之间工作。
2.  *Lottie:* 我们能够通过包装 Android 和 iOS 上的现有库，让 Lottie 在 React Native 中工作。
3.  *原生网络堆栈:* React Native 在两个平台上都使用我们现有的原生网络堆栈和缓存。
4.  *其他核心基础设施:*就像网络一样，我们包装了现有的本地基础设施，如 i18n、experimentation 等。以便在 React Native 中无缝工作。

## 静态分析

我们在网络上使用 eslint 有很长的历史，我们能够利用这一点。然而，我们是 Airbnb 的第一个平台，开创了 T2 更漂亮的 T3。我们发现它能有效减少 PRs 上的虱卵和自行车脱落。我们的网络基础设施团队正在积极调查 Prettier。

我们还使用分析来测量渲染时间和性能，以确定哪些屏幕是调查性能问题的重中之重。

因为 React Native 比我们的 web 基础设施更小更新，所以它被证明是新想法的一个很好的试验台。我们为 React Native 创建的许多工具和想法正在被 web now 采用。

## 动画片

感谢 React Native [动画](https://facebook.github.io/react-native/docs/animated.html)库，我们能够实现无 jank 动画，甚至交互驱动的动画，如滚动视差。

## JS/React 开源

因为 React Native 真正运行 React 和 javascript，所以我们能够利用极其大量的 javascript 项目，如 redux、reselect、jest 等。

## Flexbox

react Native handles layout with[Yoga](https://github.com/facebook/yoga)，一个跨平台 C 库，通过 [flexbox](https://www.w3schools.com/css/css3_flexbox.asp) API 处理布局计算。在早期，我们遇到了 Yoga 的局限性，比如缺乏长宽比，但在随后的更新中已经添加了这些限制。此外，像 [flexbox froggy](https://flexboxfroggy.com/) 这样有趣的教程让入职变得更加愉快。

## 与网络协作

在 React Native 探索的后期，我们开始同时为 web、iOS 和 Android 构建。考虑到 web 也使用 Redux，我们发现大量代码可以在 web 和本地平台之间共享，而无需修改。

# 哪些地方做得不好

## 反应天生的不成熟

React Native 不如 Android 或 iOS 成熟。它更新换代，雄心勃勃，发展速度极快。虽然 React Native 在大多数情况下都工作得很好，但在某些情况下，它的不成熟会让一些在 Native 中无关紧要的事情变得非常困难。不幸的是，这些情况很难预测，可能需要几个小时到几天才能解决。

## 维护 React Native 的分支

由于 React Native 的不成熟，有时我们需要修补 React Native 的源代码。除了对 React Native 作出贡献，我们还必须[维护一个分支](https://github.com/airbnb/react-native/commits/0.46-canary),在其中我们可以快速合并更改并修改我们的版本。两年来，我们不得不在 React Native 上添加大约 50 个提交。这使得 React Native 的升级过程极其痛苦。

## JavaScript 工具

JavaScript 是一种非类型语言。缺乏类型安全不仅难以扩展，而且成为习惯于类型化语言的移动工程师的争论点，否则他们可能会对学习 React Native 感兴趣。我们探索了采用[流程](https://flow.org/)，但是神秘的错误信息导致了令人沮丧的开发体验。我们也探索了 [TypeScript](http://www.typescriptlang.org/) ，但是将它集成到我们现有的基础设施中，例如 [babel](https://babeljs.io/) 和 [metro bundler](https://github.com/facebook/metro) 被证明是有问题的。然而，我们正在继续积极研究网络上的类型脚本。

## 重构

JavaScript 无类型化的一个副作用是重构极其困难并且容易出错。重命名道具，尤其是像 *onClick* 这样有一个普通名字的道具，或者是通过多个组件传递的道具，对于精确重构来说是一场噩梦。更糟糕的是，重构在生产中而不是在编译时中断，并且很难添加适当的静态分析。

## JavaScriptCore 不一致

React Native 的一个微妙和棘手之处在于它是在一个 [JavaScriptCore 环境](https://facebook.github.io/react-native/docs/javascript-environment.html)上执行的。以下是我们遇到的结果:

*   iOS 自带开箱即用的[JavaScript core](https://developer.apple.com/documentation/javascriptcore)。这意味着 iOS 基本上是一致的，对我们来说没有问题。
*   Android 没有自己的 JavaScriptCore，所以 React Native 有自己的包。但是，你默认得到的[是古代的](https://github.com/facebook/react-native/issues/10245)。结果，我们不得不特意捆绑一个[更新的](https://github.com/react-community/jsc-android-buildscripts)。
*   调试时，React Native 会附加到一个 Chrome 开发者工具实例。这很棒，因为它是一个强大的调试器。然而，一旦附加了调试器，所有的 JavaScript 都在 Chrome 的 V8 引擎中运行。这在 99.9%的情况下都没问题。然而，在一个例子中，当 toLocaleString 在 iOS 上工作，但在调试时只在 Android 上工作时，我们被咬了一口。事实证明，Android JSC [不包括它](https://github.com/facebook/react-native/issues/15717)，它会静静地失败，除非你在调试，在这种情况下，它使用的是 V8。如果不知道这样的技术细节，对于产品工程师来说，这可能会导致几天痛苦的调试。

## React 原生开源库

学习一个平台既困难又费时。大多数人只对一两个平台比较了解。React 拥有地图、视频等原生桥梁的原生库。需要对所有三个平台有相同的了解才能成功。我们发现大多数 React 原生开源项目是由只有一两个经验的人编写的。这导致了 Android 或 iOS 上的不一致或意想不到的错误。

在 Android 上，许多 React 原生库也要求您使用 node_modules 的相对路径，而不是发布与社区预期不一致的 maven 工件。

## 并行基础设施和功能工作

我们在 Android 和 iOS 上积累了多年的原生基础设施。然而，在 React Native 中，我们从一张白纸开始，必须编写或创建所有现有基础架构的桥梁。这意味着有时产品工程师需要一些尚不存在的功能。在这一点上，他们要么不得不在他们不熟悉的平台上工作，并且在他们的项目范围之外来构建它，要么被阻止直到它可以被创建。

## 碰撞监控

我们使用 [Bugsnag](https://www.bugsnag.com/) 在 Android 和 iOS 上进行崩溃报告。虽然我们能够在两个平台上正常工作，但它不太可靠，而且比在我们的其他平台上需要更多的工作。因为 React Native 在行业中相对较新且罕见，所以我们必须构建大量的基础设施，例如在内部上传源地图，并且必须与 Bugsnag 合作，以便能够通过 React Native 中发生的事件来过滤崩溃。

由于 React Native 周围的定制基础设施的数量，我们偶尔会遇到严重的问题，其中没有报告崩溃或没有正确上传源代码。

最后，如果问题跨越 React 本机和本机代码，调试 React 本机崩溃通常更具挑战性，因为堆栈跟踪不会在 React 本机和本机代码之间跳转。

## 本地桥

React Native 有一个[桥 API](https://facebook.github.io/react-native/docs/communication-ios.html) 在 Native 和 React Native 之间进行通信。虽然它像预期的那样工作，但是写起来非常麻烦。首先，它需要正确设置所有三个开发环境。我们也经历了许多问题，来自 JavaScript 的类型是意想不到的。例如，整数通常由字符串包装，这个问题直到通过桥才被意识到。更糟糕的是，有时 iOS 会无声无息地失败，而 Android 会崩溃。我们在 2017 年底开始研究从 TypeScript 定义自动生成桥代码，但为时已晚。

## 初始化时间

在 React Native 可以第一次渲染之前，你必须初始化它的运行时。不幸的是，对于我们这种规模的应用来说，这需要几秒钟，即使是在高端设备上。这使得对启动屏幕使用 React Native 几乎是不可能的。我们通过在应用启动时初始化 React Native，最大限度地缩短了它的首次渲染时间。

## 初始渲染时间

与原生屏幕不同，React Native 需要至少一个完整的主线程-> js -> yoga layout 线程->主线程往返，才能有足够的信息来第一次渲染一个屏幕。我们在 iOS 上看到的平均初始 p90 渲染时间为 280 毫秒，在 Android 上为 440 毫秒。在 Android 上，我们使用了通常用于共享元素转换的[延迟界面转换](https://developer.android.com/reference/android/app/Activity.html#postponeEnterTransition()) API 来延迟显示屏幕，直到它被渲染。在 iOS 上，我们遇到了从 React Native 设置 navbar 配置不够快的问题。因此，我们为所有 React 本机屏幕转换添加了 50 毫秒的人工延迟，以防止配置加载后导航条闪烁。

## 应用程序大小

React Native 对 app 大小也有不可忽视的影响。在 Android 上，React Native (Java + JS +本机库，如 Yoga + Javascript Runtime)的总大小为每个 ABI 8mb。如果在一个 APK 中同时包含 x86 和 arm(仅 32 位),它应该接近 12mb。

## 64 位

由于 T4 的这个问题，我们仍然不能在 Android 上发布 64 位 APK。

## 手势

我们避免对涉及复杂手势的屏幕使用 React Native，因为 Android 和 iOS 的触摸子系统差异很大，为整个 React Native 社区提供统一的 API 一直是一个挑战。然而，工作仍在继续进行中，而[react-native-手势处理器](https://github.com/kmagiera/react-native-gesture-handler)刚刚发布了 1.0。

## 长长的名单

React Native 在这方面取得了一些进展，推出了像 [FlatList](https://facebook.github.io/react-native/docs/flatlist.html) 这样的库。然而，它们远没有 Android 上的[recycle view](https://developer.android.com/guide/topics/ui/layout/recyclerview)或 iOS 上的 [UICollectionView](https://developer.apple.com/documentation/uikit/uicollectionview) 成熟和灵活。由于螺纹的原因，许多限制很难克服。适配器数据不能被同步访问，因此当视图在快速滚动时异步呈现时，可以看到视图闪烁。文本也不能同步测量，因此 iOS 无法使用预先计算的单元格高度进行某些优化。

## 升级 React Native

尽管大多数 React 原生升级都是微不足道的，但也有一些升级是痛苦的。特别是，使用 React Native 0.43(2017 年 4 月)到 0.49(2017 年 10 月)几乎是不可能的，因为它使用了 React 16 alpha 和 beta。这是一个很大的问题，因为大多数为 web 使用而设计的 React 库不支持预发布的 React 版本。为这次升级争论适当的依赖关系的过程是 2017 年年中其他 React 原生基础设施工作的主要损害。

## 易接近

2017 年，我们进行了一次重大的[无障碍改造](https://airbnb.design/designing-for-access/)，我们投入了巨大的努力，以确保残疾人可以使用 Airbnb 预订能够满足他们需求的房源。然而，React 本机可访问性 API 中有许多漏洞。为了满足最低可接受的可访问性要求，我们不得不[维护我们自己的 React Native 分支](https://github.com/airbnb/react-native/commits/0.46-canary)，在那里我们可以合并修复。对于这些情况，Android 或 iOS 上的一行修复程序最终花了几天时间来弄清楚如何将它添加到 React Native，挑选它，然后在 React Native core 上提交一个问题，并在未来几周内跟进。

## 麻烦的撞车事故

我们不得不处理一些很难修复的非常奇怪的崩溃。例如，我们目前正在经历[这个关于 *@ReactProp* 注释的崩溃](https://issuetracker.google.com/issues/37045084)，并且已经无法在任何设备上重现它，甚至是那些与在野外崩溃的设备具有相同硬件和软件的设备。

## Android 上跨进程保存的实例

Android 经常清理后台进程，但是给它们一个机会[同步保存它们的状态在一个包](https://developer.android.com/topic/libraries/architecture/saving-states#use_onsaveinstancestate_as_backup_to_handle_system_initiated_process_death)中。然而，在 React Native 上，所有状态只能在 js 线程中访问，所以这不能同步完成。即使不是这种情况，redux 作为一个状态存储也与这种方法不兼容，因为它混合了可序列化和不可序列化的数据，并且可能包含比 savedInstanceState 捆绑包中所能容纳的更多的数据，这将导致生产中的[崩溃](/@mdmasudparvez/android-os-transactiontoolargeexception-on-nougat-solved-3b6e30597345)。

这是一系列博客文章的第二部分，重点介绍了我们使用 React Native 的体验以及 Airbnb 的下一步移动应用。

*   [第一部分:在 Airbnb 上反应本土](/airbnb-engineering/react-native-at-airbnb-f95aa460be1c)
*   [*第二部分:技术*](/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)
*   [第 3 部分:建立跨平台移动团队](/airbnb-engineering/building-a-cross-platform-mobile-team-3e1837b40a88)
*   [第 4 部分:做出反应原生的决定](/airbnb-engineering/sunsetting-react-native-1868ba28e30a)
*   [第五部分:手机的下一步是什么](/airbnb-engineering/whats-next-for-mobile-at-airbnb-5e71618576ab)