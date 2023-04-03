# 什么是 FATML，为什么要关注它

> 原文：<https://medium.com/compendium/https-medium-com-mab-55055-what-is-fatml-and-why-should-you-care-dfb36e51f2f4?source=collection_archive---------1----------------------->

![](img/b1ed97f17d8e3f3ef2430992073940c8.png)

Sky over Atlanta

在我写这篇文章的时候，我正坐在去乔治亚州亚特兰大的 ACM FAT* 2019 会议的飞机上。这是该会议第二次举行，旨在汇集对社会技术系统的公平性、问责制和透明度感兴趣的研究人员和从业人员。机器学习的这个领域被称为 [FATML](https://www.fatml.org/) 。因此，在接下来的内容中，我将向您简要介绍 FATML 原则，并讨论它对可持续未来的重要性。

我们正走向一个数字化决策的社会，也就是说，在幕后有一个算法为我们做出选择。事实上，我们已经在那里。如果你需要在网上搜索一些东西，你就*谷歌*它，对吗？一个结果列表神奇地呈现在你面前供你选择。昨天你通过推荐在网飞看的电影怎么样？或者你在 Instagram 上看到的下一个帖子？你想过这些选择是如何为你产生的吗？你应该盲目地相信他们吗？我们在过去几年中已经看到了一些案例，证明了这些算法是如何以意想不到的(有时是有意的)方式影响你的行为的。它们可以让你感觉好或不好，改变你对世界的看法，改变你的投票观点，改变你的消费习惯，让你买你不想买的东西。

# 人类决策的案例

所以这听起来不太妙，对吧？如果我们不相信算法，而是相信人类的思维和决策呢？好吧，在《法官调查》[【2】](#1bbf)中，一群上世纪 70 年代的美国研究人员试图回答这个问题。他们创建了一个假设案件的设置，并独立询问了 47 名弗吉尼亚州地方法院的法官，他们将如何处理每一个案件。

这是其中之一:

> 一名 18 岁的女性被告因持有大麻被捕，与她的男朋友和其他七名熟人一起被捕。有证据表明发现了大量吸食和未吸食的大麻，但在她身上没有直接发现大麻。她以前没有犯罪记录，是一个来自中产阶级家庭的好学生，对自己的行为既不叛逆也不道歉。

令人难以置信的是句子的多样性。47 名法官中， **29 名**表示**无罪**18 名宣布她**有罪**。选择了有罪判决的人当中， **8** 推荐**缓刑**， **4** 想要**罚金**， **3** 说**缓刑加罚金**，而 **3** 同意**监禁**。所以，如果这是同一个数字算法的几次运行，我敢说你会找到一个足够疯狂的人把它投入生产！但是研究人员更进一步，在给法官的 41 个假设案例中，有 7 个出现了两次——被告的名字被改变了。大多数法官在第二次审理同一案件时，都无法做出相同的判决。这表明，人类在公正或不偏不倚方面不太可靠，甚至在相同的证据下做出相同的选择。

# 数字决策的案例

让算法做决定的一个好处是，在给定相同证据的情况下，更容易得到相同的决定。这样，我们至少可以在决策过程中引入可预测性。但是在这条道路上，还有其他一些危险:

*   你的算法不公平公正。你可能认为你知道什么是公平，但实际上，这里有关于公平的 21 个定义。在高层次上，您可以认为算法的分数对于不同的人口统计组(即，按年龄、性别、种族、宗教等分组)应该是相同的。
*   没有明确的补救途径或缺乏责任。当一个决定是通过算法做出的，质疑这个决定并给出结果的解释应该是很容易的。
*   缺乏[透明度](https://www.aclu.org/blog/privacy-technology/pitfalls-artificial-intelligence-decisionmaking-highlighted-idaho-aclu-case)，当我们盲目相信一个算法做出决定。如果算法的创造者抛弃了三分之二的历史记录来产生一个不反映现实的公式，你怎么知道呢？

关于我们应该从这些算法中要求什么，以及为了信任这些算法，我们在设计这些算法时可以遵循的[准则](https://cdt.org/issue/privacy-data/digital-decisions)，一个[共识](http://www.fatml.org/resources/principles-for-accountable-algorithms)正在形成。我在这里讨论几个:

*   **责任**解决系统对个人或社会不利影响的补救途径。它还要求为负责及时补救此类问题的人员指定一个内部角色。
*   问责制是一项原则，即在法律上或政治上对伤害负有责任的人必须提供某种补偿或理由。请注意，为了负责任，你必须有一定程度的控制，也就是说，你能够造成伤害或者能够阻止伤害。
*   **可解释性**意味着可以用非技术术语向最终用户和其他利益相关者解释决策。
*   [**公平性**](https://fairmlbook.org/introduction.html) 是指在考虑不同人口统计数据时，决策不会对最终用户造成歧视性或不公正的影响。

如果我们要用算法来衡量人类的决策，我们需要理解我们在做什么。公平是一个[难题](http://joshualoftus.com/post/algorithmic-fairness-is-as-hard-as-causation/)，其中一个原因是，对于如何从你对公平的道德要求到要使用的正确衡量标准，还没有明确的最佳实践，或者说一切似乎都取决于环境。我们不能狭隘地认为[公平可以用数学方法解决](/s/story/the-seductive-diversion-of-solving-bias-in-artificial-intelligence-890df5e5ef53)。在这里，来自伦理学、法学、社会经济学、政治学、数学和计算机科学的学科必须联合起来。仅仅是这些分支之间的通信就已经是一个非常困难的问题了。但是如果我们做对了，我们将能够建立一个没有歧视或产生不公平结果的系统。这就是为什么我关心 FATML，为什么你也需要关心它。

因此，我来到 ACM FAT* 2019，以便更好地了解 FATML 面临的挑战。与我同行的是约瑟芬和丹尼斯，他们正在丹麦哥本哈根 IT 大学(ITU)攻读硕士学位，他们是算法公平(𝛼@ITU).)新成立的研究小组的成员他们将与我现在的雇主 Computas 合作，撰写关于机器学习公平性的硕士论文，我是他们的行业顾问。他们参加这次会议是由 Computas 赞助的，我们希望从这个领域中得到启发，并希望在这个令人兴奋的领域中带回新的更好的研究想法。

[Computas](https://www.computas.com/) 已经为挪威的公共和私营部门提供了关键任务解决方案。我们正在向丹麦扩张，我们的子公司 [Computas Denmark](https://computas.com/dk/) 是与 ITU 的 Josephine 和 Denise 合作的主要驱动力。通过这次合作，我们旨在加强我们的承诺，在公平、问责和透明的原则下提供机器学习解决方案，并建立一个更美好和可持续的未来。

# 进一步阅读

如果你想进入兔子洞，这里有一个精选的链接列表:

1.  [https://fairmlbook.org/introduction.html](https://fairmlbook.org/introduction.html)这是一个比我在这里试图实现的更好的介绍，解释了为什么算法决策的公平性很重要。
2.  https://www . Amazon . com/Hello-World-Being-Human-Algorithms/DP/039363499 xHannah Fry 写的这本书有很多例子说明当我们让算法自由运行时会发生什么。这也是这篇文章的灵感之一。你绝对应该读一读！
3.  https://www . Forbes . com/sites/gregorymcneal/2014/06/28/Facebook-manipulated-user-news-feeds-to-create-emotional-contagion/# 650 e 0 CFC 39 DC 简而言之，脸书有能力让你感觉好或坏，只需调整你的新闻订阅中显示的内容。
4.  [https://www . oreilly . com/ideas/managing-risk-in-machine-learning](https://www.oreilly.com/ideas/managing-risk-in-machine-learning)一篇讨论如何管理机器学习风险的伟大文章。
5.  [https://EC . Europa . eu/digital-single-market/en/news/draft-ethics-guidelines-trust-ai](https://ec.europa.eu/digital-single-market/en/news/draft-ethics-guidelines-trustworthy-ai)这份草案展示了欧洲如何试图规范 AI 的使用。
6.  [https://www.h2o.ai/blog/what-is-your-ai-thinking-part-1/](https://www.h2o.ai/blog/what-is-your-ai-thinking-part-1/)可解释机器学习的高级介绍。
7.  https://hacker noon . com/explaible-AI-wot-deliver-here-is-why-6738 f 54216 be 这是一篇关于可解释人工智能未来挑战的很好的评论文章。批评主要集中在解释的简单性上，但是这个领域正在解决这个问题。解释给出的价值是无价的，因为它允许你在你的算法中识别偏见或种族主义行为。
8.  [http://joshualoftus . com/post/algorithmic-fairness-is-as-hard-as-cause/](http://joshualoftus.com/post/algorithmic-fairness-is-as-hard-as-causation/)一篇很棒的博文，解释了为什么删除受保护的属性(如种族、性别等)不会帮助你解决公平问题。
9.  [https://www . DARPA . mil/program/explable-artificial-intelligence](https://www.darpa.mil/program/explainable-artificial-intelligence)DARPA 关于可解释 AI 的计划。

# 感谢

我要感谢约瑟芬、丹尼斯和英格丽德对这篇博文草稿的有益评论。

# 放弃

Computas 是作者现在的雇主。文中表达的观点、想法和意见仅属于作者，不一定属于作者的雇主、组织、委员会或其他团体或个人。

# 参考

[1] Robert Epstein 和 Ronald E. Robertson，“搜索引擎操纵效应(SEME)及其对选举结果的可能影响”，*《美国国家科学院院刊》，2015 年第 112 卷第 33 期，第 e 4512–21 页*，【http://www.pnas.org/content/112/33/E4512】T2。

[2]威廉姆斯·奥斯汀和托马斯·A·威廉姆斯三世，“法官对模拟法律案件反应的调查:量刑差异研究笔记”，*《刑法与犯罪学杂志》，1977 年第 68 卷第 2 期，第 306–310 页*。