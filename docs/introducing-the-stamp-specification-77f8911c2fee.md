# 介绍邮票规格

> 原文：<https://medium.com/javascript-scene/introducing-the-stamp-specification-77f8911c2fee?source=collection_archive---------0----------------------->

## 移过去，` class`:
可组合的工厂函数在这里

2010 年，JavaScript 世界看起来大不相同。开发人员开始意识到 JavaScript 的力量和潜力，正如我们所做的那样，构建 JavaScript 应用程序的公司数量达到了临界质量。突然间，JavaScript 不仅仅是网页浏览器脚本语言。对于真正的应用程序开发来说，JavaScript 是一个严肃的、受人尊敬的工具。

大约在那个时候，我开始做大量的面试来雇佣开发人员。就在那时，我注意到了一个巨大的问题，这个问题一直在增长:

很多人知道一点 JavaScript，但是几乎没有人真正理解 JavaScript 的本质，以及是什么使它特别和独特地适合作为 web 平台的语言。

一般软件开发市场存在技能缺口，但 JavaScript 的缺口更大。大峡谷。真正理解 JavaScript 的人数与使用 JavaScript 的人数相比简直是个笑话。道格拉斯·克洛克福特称 JavaScript 是“世界上最容易被误解的编程语言”我同意。

> " JavaScript 是世界上最容易被误解的编程语言."
> ~道格拉斯·克洛克福特

我认为主要原因是因为 JavaScript 与大多数程序员熟悉的命令式和面向类的语言有本质的不同。其核心是两种截然不同的范式。

## JavaScript 的两大支柱

1.  **原型遗传**
2.  **功能编程**

我在 2013 年的 Fluent 大会上做了一个关于原型继承的演讲:“经典继承过时了:如何在原型 OO 中思考”。在你继续这篇文章之前，你应该看看它。

不服气？Mattias P Johansson 今天发布了一个有趣的视频，生动地展示了杀人机器狗的大猩猩香蕉问题:

经典继承创建了与限制性分类法的关系，如果继续使用和发展，所有这些最终都不适合新的用例。但事实证明，我们通常为 **has-a** 、 **uses-a** 或 **can-do** 关系使用继承。

作曲更像是吉他效果器踏板。想要一个可以做延迟，微妙失真和机器人声音的东西？没问题！把它们都插上电源:

```
const effect = compose(delay, distortion, robovoice); // Rock on!
```

什么时候需要使用类继承？对我来说，答案很简单:“永远不会。”

## **作文是:**

*   **更简单**
*   **更强大**
*   **更加灵活**
*   **更具扩展性**
*   **更适合敏捷开发**

# 介绍邮票

印章是**可组合的工厂功能**。我制作邮票是为了实验奥莱利的书《T2》，“编写 JavaScript 应用程序”。一切都始于这个有趣的挑战:

> 假装课程使用 JavaScript 是有好处的(我强烈反对使用它)。如果我们为原型 OO 创建了 sugar，它支持 JavaScript 必须提供的所有最好的特性，会是什么样子？*

据我所知，这种探索的结果是最流行的基于原型的 JavaScript 继承库: [Stampit](https://github.com/stampit-org/stampit) 。

这个想法似乎引起了人们的共鸣，没过多久，衍生图书馆就开始出现了。值得注意的是，Tim Routowicz 为可组合的反应组分编写了优秀的[反应模板](https://github.com/troutowicz)库。

React stamps 只有一个问题:因为它们是专门为创建 React 组件而设计的，所以对 API 进行了更改，以便更好地服务于该目的。反应邮票和冲压邮票不能组合在一起！哎呀。

大约在同一时期，Stampit 社区开始猜测在 JavaScript 规范本身中引入语法来支持 stamps 的想法。如果发生了这种情况，这些邮票将如何与 React 邮票或 Stampit 邮票互操作？

今天，我很自豪地宣布，我们一直在致力于一个规范，它将统一所有兼容的 stamp 实现:[Stamp Specification:Composables](https://github.com/stampit-org/stamp-specification#stamp-specification-composables)。我们目前正在进行三个实施:

*   [**为实现者提供带有测试套件的参考实现**](https://github.com/stampit-org/stamp-utils)
*   [**Stampit 3.0**](https://github.com/stampit-org/stampit) ，带有 Stampit 2.0 API 的兼容层
*   [**反应-冲压**](https://github.com/stampit-org/react-stamp)

我们将在接下来的几周内致力于这些实现。目标是在 2015 年 10 月 27 日澳大利亚悉尼 **的官方发布会和培训大师班前完成。**

这是我对你的正式邀请。来听听官方发布的公告，并使用新的 Stampit 3.0 和 React stamps 获得一整天的 JavaScript 两大支柱实践培训。

额外收获:你也会加深对函数式编程的理解。

> [**报名参加 2015 年 web directions
> 大师班“JavaScript 的两大支柱”。**](http://www.webdirections.org/wd15/#workshops)
> 
> 悉尼见！

***Eric Elliott****著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(O'Reilly)，以及* [*【学习 JavaScript 通用 App 开发用节点，ES6，&*](https://leanpub.com/learn-javascript-react-nodejs-es6/)*。他为 Adobe Systems******Zumba Fitness*******华尔街日报*******ESPN*******BBC****等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**