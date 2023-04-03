# 密码已过时—如何保护您的应用和用户

> 原文：<https://medium.com/javascript-scene/passwords-are-obsolete-how-to-secure-your-app-and-protect-your-users-1cd6c7b7c3bc?source=collection_archive---------0----------------------->

![](img/4c75fdd8b77f51febd27783b1904bd95.png)

> [这部分我在](/javascript-scene/improving-user-authentication-and-security-ddb60b1ef69b)之前已经说过了，所以如果你看了上一篇文章，就跳到下面的“如何实现无密码认证”。我再次为那些懒得点击链接的人张贴介绍。

管理用户认证和授权是一项非常严肃的责任，如果做错了，比未经授权访问您的应用程序要付出更大的代价。它还会危及用户隐私或导致财务损失或用户身份被盗。除非你是一家拥有庞大安全团队的大公司，否则你不会希望你的应用承担这种责任。

如今，大多数应用都是通过用户名和密码认证构建的，一旦用户登录，用户会话就可以做任何想做的事情，而无需重新验证用户的意图。

这种安全模式被打破的原因有很多:

**密码已经过时。**如果你对此有任何疑问，请前往[haveibenpwned](https://haveibeenpwned.com/)并输入你的电子邮件地址。敏感数据在许多高调的数据泄露事件中被盗，影响到 Dropbox、Adobe、Disqus、Kickstarter、LinkedIn、Tumblr 等公司。如果数据库里有密码，它被盗只是时间问题。

散列密码不会拯救你或你的用户。一旦密码数据库被盗，黑客就会将巨大的分布式计算能力瞄准这些密码数据库。他们使用并行 GPU 或拥有成千上万个节点的巨型[僵尸网络](https://www.bankinfosecurity.com/massive-botnet-attack-used-more-than-400000-iot-devices-a-12841)来尝试每秒[数千亿次密码组合](https://www.zdnet.com/article/25-gpus-devour-password-hashes-at-up-to-348-billion-per-second/)以期恢复明文用户名/密码对。

如果攻击者能够发现一个与你的数据库中存储的密码散列相同的密码，他们将采用该组合，并在诸如银行账户网站之类的东西上进行尝试。在许多情况下，甚至一个加盐的、散列的密码数据库也会每分钟左右泄漏另一个有效的用户名/密码对。这意味着每年大约有 50 万个密码被泄露，而且这个数字每隔几年就会翻一番。2013 年我写过这个话题。现在，坏人破解密码的速度比以前快了 10 倍以上。

用户会话被劫持。 [用户会话通常在认证后被劫持](https://www.owasp.org/index.php/Session_hijacking_attack)，使得攻击者能够利用该用户的应用资源。为了防止这种情况，您需要在每次请求时对用户进行重新认证，并且在用户名和密码方面，这将造成一种尴尬的用户体验。

## 升级身份验证

分散式应用程序最酷的特性之一是分散式安全模型。使用以太坊区块链生态系统，每个用户得到一个公钥和私钥对。您可以用用户的私钥签署每个请求，并用用户的公钥验证请求。每个请求都经过唯一的身份验证，这将劫持的可能性降低到几乎为零。

劫机者需要代表用户签名的能力，但如果无法获得用户的私钥，他们就无法做到这一点，因为私钥受到硬件级安全性的保护。使用**硬件安全模块(HSMs)** ，我们可以保护私钥不被暴露在互联网上。我们不通过网络发送私钥，而是发送需要在 HSM 中用私钥签名的消息。用户授权签名，签名的请求得到验证和处理。如果签名无效，请求将被拒绝。

此外，这些密钥对可以加密和解密用户数据，以便只有拥有数据的用户才能读取数据。如果一个应用程序开发者选择让用户加密他们的数据，那么在没有用户许可的情况下，即使是应用程序也无法解密数据。有了这个安全模型，我们可以让用户控制他们的私人信息。

# 神奇链接的无密码认证

一种新兴的绕过密码的方法是使用魔法链接。神奇链接是一个临时 URL，在使用后或特定时间间隔后过期。神奇的链接可以发送到你的电子邮件地址、应用程序或安全设备。单击该链接授权您登录。

纯密码安全已经过时，而且非常不安全。神奇的链接消除了密码丢失或被盗的烦恼，保护了应用程序用户。

但是你不要试图滚动你自己的基于公钥/私钥的神奇链接，否则你将从煎锅中进入火中。如果你认为保护密码安全很难，那就不要试图管理私钥，如果私钥被盗，可能会被授予访问装有贵重货币、收藏品、会员资格等的以太坊钱包的权限。

许多应用程序和钱包将这一责任推给了最终用户。这就像把银行金库的钥匙放在某人的手机里。如果手机丢失、被盗或升级怎么办？

在我看来，最好的方法是将密钥管理委托给专门从事密钥管理的人。其中一项服务已于今日推出。它叫做[魔法](https://magic.link/)。它是由网络安全公司 Magic Labs 制造的，该公司汇集了来自 Docker、苹果、谷歌、亚马逊、Yelp、优步、埃森哲和道明银行等公司的专家。

他们的安全模型将用户的私钥存储在 HSMs 中。HSM 有点像私钥的硬件锁。按键受硬件保护，永远不离开硬件。钥匙从来不会暴露在互联网上。相反，需要由这些密钥签名的消息被传送到专用硬件并在其上签名。

想象一个银行保险箱。盒子里面是一个密钥，可以用来授权签名和交易。当你从银行租用保险箱时，保险箱里的东西是属于你的。银行只是为你保管它。使用魔法有点像给每个用户一个专用的保险箱来存放他们的钥匙。魔法不能接触到钥匙，你也不能。密钥始终由用户控制，但托管在云中，因此用户不必担心丢失。

换句话说，Magic 是一个非托管的、硬件安全的密钥管理系统。它具有一流的安全性和 SOC 2 合规性。但是最好的部分是你的用户不需要知道这些意味着什么。他们所需要知道的就是输入他们的电子邮件，然后点击一个按钮。

## 为您的应用增添魔力

[Magic 有一个很棒的入门指南](https://docs.magic.link/)和文档来帮助你快速设置和理解基础知识，但是我们将进行更深入的探索，剖析我们为与[EricElliottJS.com](https://ericelliottjs.com/)集成而开发的实际`useMagicLink` React 钩子。如果你阅读了关于 Fortmatic 的前一篇文章，这将看起来非常类似于我们之前开发的`useFortmatic`钩子，但是它有一些细微的变化。

在深入研究源代码之前，您需要理解一些基本概念:

*   [React 基础知识](https://reactjs.org/docs/hello-world.html)
*   [JavaScript 箭头函数和基本语法](/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)
*   [定制功能](/javascript-scene/curry-and-function-composition-2c208d774983)
*   [承诺](/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261)
*   [反应钩](https://reactjs.org/docs/hooks-intro.html)
*   [使用效果](https://reactjs.org/docs/hooks-effect.html)、[使用状态](https://reactjs.org/docs/hooks-state.html)和 [useRef](https://reactjs.org/docs/hooks-reference.html#useref)
*   [本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
*   [高阶组件(hoc)](https://reactjs.org/docs/higher-order-components.html)

这个文件需要一些助手。第一个是一个`usePromise`钩子，它持有对`magicReady`承诺的持久引用。我保证，你希望你的魔法在你试图使用之前就准备好了，否则你的咒语会适得其反。

以及`useState`的 localStorage 插件替换:

最后，一些杂项工具:

钩子完成后，我们的下一步是将钩子集成到我们现有的应用程序中。我们使用 Redux，并尽可能地将逻辑、I/O 和状态过程从我们的表示组件中分离出来。这个[高阶组件(HOC)](https://reactjs.org/docs/higher-order-components.html) 让我们可以在一个地方将神奇的链接逻辑组合到应用程序的每个页面中。

下面是我们用来将横切关注点组合到每个需要它们的页面中的特设:

因为这些 hoc 非常容易创建和维护，所以您可以创建自定义的 hoc，省略特定页面不需要的功能，或者添加每个页面都不需要的功能。现在，我们可以在我们网站的任何地方登录或退出！

## 结论

以下是我目前对用户认证安全性的建议:

*   仅使用密码的安全性已经过时，而且非常不安全。不要用。
*   **公钥加密为我们提供了公钥/私钥对**，我们可以用它们来显著增强用户的安全性。
*   **对应用开发者和终端用户来说，密钥管理都很难。**
*   应用开发者应该将密钥管理委托给安全专家。
*   **硬件安全模块(HSM)可以在云中安全地存储用户密钥，**而无需强迫用户知道这意味着什么。
*   **神奇链接消除了密码丢失或被盗的烦恼**，保护了 app 用户。
*   **Magic 为您的用户提供一流的非托管委托密钥管理**。它们为应用和用户更安全的未来铺平了道路。它们为用户身份验证的安全性和用户体验设立了一个新的标杆，也是我目前推荐给新应用程序开发人员的唯一身份验证解决方案。
*   **魔法开启以太坊。**借助 Magic 基于以太坊的公钥/私钥对，我们可以使用它们与以太坊和 EVM 兼容的协议和令牌进行交易，释放出许多在区块链技术发明之前不可能实现的功能。

> **注意:**你不必构建以太坊 dapp 来受益于 Magic 的无密码认证和改进的用户安全性。你可以用魔法改进任何 app。

## 后续步骤

Magic 正在进行产品搜索。过来发表评论，提出问题，并与制作者交流。

在[EricElliottJS.com](https://ericelliottjs.com/account)上试驾认证流程。我们的现有用户拥有 GitHub 认证帐户，您需要在第一次登录时进行链接，但是一旦您链接了 GitHub 帐户，您就不需要再次进行链接。注销并重新登录以查看仅限 Magic 的流程。

GitHub 流要求 shields 在 Brave 中关闭，因为认证流是如何委派的。新的神奇认证流程就像一个带着盾牌的魔咒。Magic 的简化流程有很多隐藏的好处。

当您查看 EricElliottJS.com 时，请浏览[优质内容](https://ericelliottjs.com/premium-content)，并探索我们的一些在线 JavaScript 课程。

变点魔法。

***埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com)*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】********

*他和世界上最美丽的女人享受着与世隔绝的生活方式。*