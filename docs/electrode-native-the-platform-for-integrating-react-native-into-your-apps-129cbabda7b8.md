# 电极原生:将 React 原生集成到您的应用程序中的平台

> 原文：<https://medium.com/walmartglobaltech/electrode-native-the-platform-for-integrating-react-native-into-your-apps-129cbabda7b8?source=collection_archive---------1----------------------->

![](img/ac9943783311d0f968b3231869c00b18.png)

开发快速、无缺陷、功能全面的移动应用程序，同时利用先进的移动技术，是沃尔玛战略的核心部分。我们希望顾客能够随时随地购物！

利用我们所学到的知识，并在我们开源内部网络平台[电极](http://www.electrode.io)(为 Walmart.com 提供动力的平台)的成功基础上，我们正在利用[电极原生](http://www.electrode.io/site/native.html)增强我们的产品组合，这是一个基于 React Native 的简化移动应用开发平台。这两个独立的平台共享相同的理念，但是[电极原生](http://www.electrode.io/site/native.html)是完全不同的平台。它是从头开始编写的，有自己的代码库，与[电极(web)](http://www.electrode.io/site/web.html) 截然不同。

Electrode Native 提供了 React Native 到现有移动应用程序的简化集成。有了电极原生技术，将不再需要一个既精通移动技术又精通反应原生技术的工程师来将这两种技术结合在一起。对于您现有的移动应用程序，没有繁重的基础设施、代码或开发生命周期更改。电极原生解决了所有这些问题，同时提供了更多的功能！

# 释放 React Native 的潜力

好的，你可能对整个沃尔玛很熟悉，但你可能不知道沃尔玛是由许多不同的品牌组成的，如沃尔玛百货、山姆会员店、Walmart.com ASDA 等等。—每个部门都有自己的 IT 需求、要求和开发团队。在移动端，事情变得更加复杂。每个品牌都有两个移动开发者团队，为每个操作系统开发应用，这些应用在功能上相似，但在执行上不同。这不是出于选择，而是必要的，因为很难找到擅长在 iOS/Android 中编写原生应用的高技能开发人员。对于关联工具，我们大多使用图标或类似的解决方案来包装 web 应用程序。我们认为 React Native 可以真正改变这种模式。

让我们对 React Native 真正感到兴奋的潜在胜利是:

*   重用 —我们知道这很重要，但为什么呢？这样我就不会重写已经写好的东西…看看我之前的博客:*如何用 React 组件实现可重用性*。React Native 不仅让我们能够重用 iOS 和 Android 之间的 UI 组件，还可以重用任何 JavaScript 代码，例如编排一些微服务的隔离模块。React Native 还让我们能够在两个平台之间重用测试套件。除此之外，还有在我们的移动和网络应用之间重用代码的潜力。
*   **空中更新—** OTA 是另一个至关重要的胜利。app store 发布过程可能会很痛苦，一旦完成，你仍然需要等到每个人都更新他们的设备。增量更改、错误修复和自动更新的即时发布是保证客户满意的必要条件。
*   **反应—** 作为额外的奖励，我们使用电极 web 应用程序开发平台与我们的 web 战略保持一致。对于我们来说，使用 JavaScript 和 React 构建我们的原生移动应用程序是有意义的。

# 方法和挑战

当然，将 React Native 集成到沃尔玛的移动应用程序中会有一些挑战。大多数移动应用程序通常是 Objective-C/Swift/Java 移动应用程序，或者包装的 web 应用程序，或者 React Native。React Native 在构建纯粹的 React Native 应用程序时表现出色，但我们的情况更复杂。我们已经有了生产中的移动应用程序，这些应用程序有许多由大量代码支持的功能。重写一切将是时间、雇佣和培训的巨大投资(翻译:金钱)。与增量迁移方法相比，完全重写容易失败。

当考虑我们使用 React Native 的方法时，我们认为将特性逐步构建到生产应用程序中是成功几率最高的解决方案，而不是大爆炸式的重写。

> **如果说我在这些年的技术迁移中学到了什么，那就是增量迁移。**

我们调查的大多数现有解决方案使用 mono-repos，其中移动应用程序和 JavaScript 都保存在同一个存储库中，并且需要对移动应用程序进行更改。单一回购方法使可重用性变得困难，因为我们不能轻松地共享离散的组件或模块。这种方法对我们来说是行不通的，因为我们不想修改当前运行良好的移动应用程序工作流程。我们认为保持不同的代码库相互独立和隔离是正确的做法。集成必须对现有的移动应用程序代码库和基础设施产生最小的影响和复杂性。

随着我们进入开发阶段，我们很快了解到将 React 原生应用集成到原生应用需要很多步骤。当您开始将多个 React 本机应用程序集成到一个本机应用程序中时，这变得更加复杂。要成功集成这两种技术，您需要一个对 React Native 的工作方式有清晰理解的移动开发人员，反之亦然。

即使我们正在构建独立的屏幕和功能，我们仍然需要利用移动应用程序中的代码。我们需要传递身份验证和其他信息，以便从本机应用程序进行本机反应。React Native bridge 不提供强类型，并且不能与本机模型一起工作——这是另一个对我们来说不无缝的接触点。

**总结我们的多重挑战:**

*   native 和 React Native 之间可重复的无缝集成允许我们为 mobile 和 React Native 工程师维护标准工作流。
*   简化的集成，不需要对 native 和 React Native 有深入了解的工程师。
*   简化的工作流程支持 JavaScript 代码的可重用性。
*   强类型本机和动态类型 React 本机之间简化且一致的通信。

# 电极原生是我们的解决方案

**焊条原生芯:容器**

为了在大型组织中扩展 React Native，我们需要应对这些挑战并实现更高效的工作方式，这就是 Electrode Native 的用武之地。电极原生是电极家族的新成员，共享相同的理念，但仍然是完全不同的平台，没有共享代码。它允许移动应用程序和 React Native 之间的无缝集成，方法是根据平台将 React Native 应用程序打包到 AAR (Android)或框架(iOS)中。

通过电极原生，我们生成了一个[容器](https://electrode.gitbooks.io/electrode-native/platform-parts/container.html#adding-a-container-to-your-mobile-application)，可以轻松地添加到现有的应用程序中，并立即开始使用 React 原生组件。集成过程非常顺利和简单，可以由本地或反应本地工程师完成。使用 React Native 添加 JavaScript 特性，不需要对现有的移动应用程序进行基础设施更改或大量的代码基础更改。只需将[容器](https://electrode.gitbooks.io/electrode-native/platform-parts/container.html#adding-a-container-to-your-mobile-application)作为新的原生依赖项添加到移动应用程序中，就像任何其他原生第三方依赖项一样，就这样！这种简化的结果是可能的，因为[容器](https://electrode.gitbooks.io/electrode-native/platform-parts/container.html#adding-a-container-to-your-mobile-application)已经为您完成了生成所有集成代码的繁重工作。

额外的电极原生模块确实有助于简化应用程序开发；从开发生命周期过程到 JavaScript 和 native 之间的交流，再到管理您的发布。

**工程师使用电极原生的好处**

**手机 App 工程师**:不需要安装新的工具，你的工作流程完全一样——甚至不用安装电极原生。从您的角度来看，您不需要处理复杂的集成-有了电极原生，React 原生就像一个版本化的第三方库，被拉入您的移动应用程序中。您的应用程序中的 React 本机依赖项是我们的“容器”，在该容器中是我们所谓的[电极本机 MiniApp](https://electrode.gitbooks.io/electrode-native/overview/what-is-a-miniapp.html) 、，其中包含所有电极本机代码(库和功能代码)。电极原生还能够在原生和电极原生之间实现简化和一致的通信。

React Native engineers :你自己开发基于 React Native 的组件。这些是[电极本地微型应用](https://electrode.gitbooks.io/electrode-native/overview/what-is-a-miniapp.html)，它们位于自己的存储库中，有自己的生命周期。您可以根据需要随时发布这些迷你应用程序的新版本，并通过无线方式发布它们。

**电极固有功能**

**Native to React Native Communication**:我们希望移动开发者能够在利用编译时检查的同时与 JavaScript 进行交流，并采用他们在 Android 或 iOS 上与移动库进行交互时使用的自然构造。为了使这两种技术之间的通信标准化并变得容易，您所需要做的就是编写 Swagger 规范，而 Electrode Native 将负责这两种技术之间的通信。

**版本控制**:多个团队构建多个 React 本地应用程序，然后同时发布多个空中更新，这可能会导致分发不兼容版本的 JavaScript 包和移动应用程序代码。电极原生确保您不会发布可能会使您客户的应用崩溃的捆绑包，我们称之为“[大锅](https://electrode.gitbooks.io/electrode-native/platform-parts/cauldron.html#a-look-inside-the-cauldron)”和“[清单](https://electrode.gitbooks.io/electrode-native/platform-parts/manifest.html)”。

**开发**:为了在类似于生产的环境中进行生产性开发，我们提供了“ [runner](https://electrode.gitbooks.io/electrode-native/platform-parts/runner.html) ”，它可以在模拟器或实际设备(如果已接入)中启动您的 React 本机应用。它的运行方式与生产环境中的运行方式非常相似，即创建一个本地容器，并让' [runner](https://electrode.gitbooks.io/electrode-native/platform-parts/runner.html) '应用程序使用该容器。

**独立存储库:**一个简单的概念，但是我发现它在让组织做什么方面非常强大。我们可以围绕所有权的功能区域来构建我们的团队，例如让“Checkout”团队拥有应用程序的 React Native checkout 区域，该区域位于其自己的存储库中。然后，您可以让项目页面团队拥有自己的回购，允许团队在任何一个厨房都没有太多厨师的情况下进行扩展。独立的存储库也支持可重用性，现在您可以与其他团队共享自包含电极原生迷你应用程序。如果每个应用程序都有一个 mono-repo，那么与另一个团队共享应用程序的片段就会变得非常复杂。

# 我们在哪里

我们的购物车和沃尔玛移动应用程序的结帐部分实际上是在应用程序内的浏览器中提供的响应性 web 应用程序。它们在应用程序中相对缓慢和笨重，我们使用自己的代码作为本地和这种响应性网络体验之间的桥梁。

我们首先转换了响应式 web 购物车屏幕。从功能的角度来看，这相对简单，因为只有一页，但是我们从这次经历中学到了很多。这是我们投入生产的第一个成熟的特性集。[查看 React Native](/walmartlabs/react-native-at-walmartlabs-cdd140589560) 关于我们发现的更深入的文章。

我们迅速转换了您在沃尔玛购物后显示的“感谢”页面。同一个团队现在正在使用 Electrode Native 转换 Walmart.com 的结账流程，他们正在加速进行。另外三个团队已经快速实现了电极原生，并且他们也在 Walmart.com 应用中构建新的功能！此外，我们计划在其他沃尔玛实体的 Walmart.com 购物应用程序之外的应用程序中使用电极原生功能。

到目前为止，使用 Electrode Native 构建的所有功能都是移动应用程序中响应网络的迁移，或者是以前没有的全新屏幕。所有的功能都是相对封装在自己的屏幕中构建的，我们将很快利用更多的电极原生功能，例如用 JavaScript 进行 API 调用，并将其交给常规的原生视图。

# 下一步是什么

我们很高兴并有动力继续支持和改进我们新的电极原生平台——就像我们对电极网所做的那样。我们非常高兴听到来自开源社区的反馈，这只会有助于改进平台。

我们的首要任务之一是支持 UI 组件的可重用性，并研究如何将电极 web 组件原型概念应用于电极原生。

我们希望解决的另一个挑战是改善开发人员的工作流程，让 React 本地开发人员能够在移动主机应用程序(例如，沃尔玛应用程序)中轻松启动他们的 React 本地应用程序(存储在自己的存储库中)，用于开发和调试目的。这一挑战尚未解决，目前仍是一种手动方法，但我们正在努力。我们已经有了一个技术方案，并将很快付诸实施。

# 立即查看电极原生版！

[焊条产地](http://www.electrode.io/site/native.html)

[焊条原生入门](https://electrode.gitbooks.io/electrode-native/getting-started/getting-started.html)

[电极原生文件](http://www.electrode.io/site/docs/introduction.html)