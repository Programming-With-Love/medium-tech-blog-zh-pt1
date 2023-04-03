# 散弹枪第 2 集:编写不会崩溃的测试

> 原文：<https://medium.com/javascript-scene/shotgun-episode-2-writing-tests-that-don-t-break-6fbce334c0c8?source=collection_archive---------5----------------------->

![](img/991d0b9dbb03bab579fb88c41f2f5453.png)

Photo: [Brandon Bailey](https://www.flickr.com/photos/luxurydesigner/) — Midnight Cowboy (CC-BY-2.0)

Shotgun 是一个新节目，当我为真正的应用程序和库解决真正的编程挑战时，你可以和我一起乘坐 shotgun。这个节目只对 EricElliottJS.com 的成员开放，但是我在这里记录了这次冒险。

在[第 1 集](/javascript-scene/shotgun-javascript-video-experience-c8b6a7771d49)中，我们启动了一个全新的 React 项目——一个使用 React 的在线课程展示引擎。我们用磁带覆盖了单元测试，用 Watch 覆盖了 auto-lint &测试，用 Enzyme 覆盖了小故障。

## 仍然对酶充满希望

在第一集中，我们切换到 Cheerio 进行 DOM 测试。大概是第 5 次被[酵素](https://github.com/jshomes/course-player/issues/2)[bug](https://github.com/airbnb/enzyme/issues/41)出轨了。我真的对 Enzyme 感到兴奋，但要做好遇到错误和提交错误报告的准备。请随时关注这些链接，调查错误，并提交适当的问题& PRs。如果我有时间，我很乐意做这件事。

在这个早期阶段，对于大多数测试来说，酵素可能会比麦片更慢。但是请记住，在撰写本文时，Enzyme 才刚刚成立 4 个月，许多聪明人正在解决这些问题，并对 API 进行改进。在 6 个月内，我希望这将是一个非常不同的故事。

## 编写持久的 UI 测试

我不知道你怎么想，但是我在构建应用程序的时候会不断地修改我的 UI 标记。当你测试 UI 组件的时候，很容易因为一个小小的改变而破坏一系列的单元测试。

为了防止这种情况，我避免编写对 DOM 了解过多的测试。例如，在 Course Player 项目中，我们有一个 *`card-player`* div，如下所示:

如你所见，它有一个*`导航条`*、一个选择菜单、一些卡片内容和一个继续按钮。我考虑过将卡片导航下移至卡片内容下方。如果我在 *`nav-bar`* 元素测试中寻找它，向下移动它会破坏测试，我将不得不进行更多的重构。

当我知道有些东西可能会改变形状时，我不会试图通过测试完整的组件输出来复制所有子测试的工作。相反，对于容器元素，我只是测试该元素是否可以被选择，以及它是否可以呈现子元素。让我们看一个例子:

看一下 *`output`* 变量。它选择 *`.nav-bar`* 元素，获取 children 集合并检查其【T4 `. length`属性。 *`actual`* 测试只是确保【T8 `. length`大于零，这告诉你它是否呈现任何子元素。

你可能会说，导航条应该检查导航选项是否显示，但是导航元素会有自己的测试来确保传递给它的子元素会被呈现，所以这个测试是多余的，只会使测试套件比它需要的更脆弱。

现在，如果我决定将导航列表从'. nav-bar '组件中移除，当然，也许我可以重新考虑将其命名为 nav-any one，因为在这一点上，它更像是一个标题栏，而不是导航栏，但如果我们能到达那里，我们将跨越这座桥梁。

## JSX 中的布尔属性

HTML 有一些布尔属性。在课程播放器中，如果还没有满足卡片要求，我们使用 *`disabled`* 属性来禁用继续按钮，同时向学生指示应该在尝试继续之前完成卡片。

编写 HTML 时，您可能想简单地添加 disabled 属性，如下所示:

在制作这一集的时候，我试着这样做:

它神奇地成功了。

注意 *`isCompleted`* 三元表达式。有一分钟，我担心空的 disabled 值会导致 *`disabled`* 属性尝试使用 falsy 值进行渲染，这不是有效的 HTML，但我很高兴地被提醒，当您将 falsy 值传递给布尔属性时，React 会做正确的事情:这是一种方便的快捷方式，使处理布尔属性变得轻而易举。

我想我们都希望事情有时会比实际情况更困难。谢天谢地，React 开发人员很懒，他们希望事情第一次就“正常工作”。

如果你是会员，现在就可以自由地[进入第二集](https://ericelliottjs.com/premium-content/shotgun-episode-2/)。

如果你还不是会员，看看预告视频，加入我们的。Shotgun 与[终身访问通行证](https://ericelliottjs.com/product/lifetime-access-pass/)捆绑在一起，它让您可以访问我们的所有视频:网络广播系列和课程内容，涵盖 TDD、原型 OO、函数式编程、React、通用应用程序开发、ES6+等主题。

# [散弹枪第 3 集:在 React 的子组件上循环](/javascript-scene/looping-over-child-components-in-react-shotgun-episode-3-396de16289cc)

> [*向埃里克·艾略特*](http://ericelliottjs.com/product/lifetime-access-pass/) *学习 JavaScript 成为终身访问会员:
> 网络广播
> 视频体验
> 书籍&更多…*

***埃里克·埃利奥特*** *著有《编写 JavaScript 应用程序*[](http://pjabook.com)**(O ' Reilly)，以及《学习通用 JavaScript App 开发用节点&**。他为 Adobe Systems******Zumba Fitness*******【华尔街日报*******【ESPN*******BBC****等顶级录音师贡献了软件经验*******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**