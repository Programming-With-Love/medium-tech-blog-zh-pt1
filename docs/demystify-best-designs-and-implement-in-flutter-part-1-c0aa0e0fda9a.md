# 揭开 Flutter 中最佳设计和实现的神秘面纱——第 1 部分

> 原文：<https://medium.com/globant/demystify-best-designs-and-implement-in-flutter-part-1-c0aa0e0fda9a?source=collection_archive---------3----------------------->

我一直喜欢设计和摄影。我喜欢漂亮的设计，即使我不能做出完美的设计，但我仍然喜欢在最好的网站上创造那些漂亮的设计，比如 [dribble](http://dribbble.com/) 和 [uplabs](https://www.uplabs.com) 等。我不想不真实地发布一些代码或其他东西，这篇文章或系列只是为了展示颤振之美和设计整合，以及我对如何使这些设计成为可能的一些想法。

这个系列将会有大量的代码，所以我建议启动一台笔记本电脑。

在第一部分中，我们试图构建的是:

![](img/24b15b1ddd785c863a15ec0d8c166d2b.png)

credits: [https://dribbble.com/shots/6160500-Card-Paginator-Component](https://dribbble.com/shots/6160500-Card-Paginator-Component)

Cuberto 是我最喜欢的设计师之一，我喜欢他们创造的东西。这是我们将要创建的设计的原始链接:

[](https://dribbble.com/shots/6160500-Card-Paginator-Component) [## 卡片分页器组件

### 刚刚为我们正在开发的应用程序使用了我们的一个组件——寻找 UI 应用程序设计？了解更多信息…

dribbble.com](https://dribbble.com/shots/6160500-Card-Paginator-Component) 

我不能发布视频，还有一个原因是我不想发布，因为这不是我的设计，而是最好的设计，所以请留意他们的设计并做出贡献。

让我们通过深入研究该设计，首先检查我们将需要哪些组件。

首先，我们将需要大约卡和个人资料照片折叠效果银应用程序栏。

第二个是 feeds 卡，现在该卡位于 about 卡的上方，当我们拖动时，两张卡都随着折叠效果向上移动。我们不能添加两个卷轴在另一个之上，所以我们将需要一个内部的堆栈视图，我们将把条子和饲料卡。

第三个是当进给卡到达大约卡顶点时协调折叠效果的逻辑，反之亦然。

![](img/6306e0888ee9156b84d6620064602e37.png)

Special Thanks to [draw.io](https://www.draw.io/)

让我们创建虚拟小部件，现在，我们不会克隆同一个视图，但会创建一个虚拟视图。

![](img/b978eda05848807876fd4610e119d865.png)

Our output feed list generation: [link](https://github.com/parthdave93/FlutterUIDesigns/blob/master/lib/card_paginator_component/card_pagination_screen.dart)

我们已经创建了一个带有列的列表。在该列中，有一个图像和一个文本小部件。目前，它是一个静态的虚拟列表。

![](img/06a689258db529dd146f24f7bab99de4.png)

about list generation: [link](https://github.com/parthdave93/FlutterUIDesigns/blob/master/lib/card_paginator_component/card_pagination_screen.dart)

我们已经创建了一个带有行的列表。在这一行中，有三个带有图标的容器，它们将复制原始设计。

![](img/e4736a6f2238634839a6f2e48afa64fe.png)

about the widget: [link](https://github.com/parthdave93/FlutterUIDesigns/blob/master/lib/card_paginator_component/card_pagination_screen.dart)

因此，对于 about widget，我们将所有列表放在一列中。请记住，我们将为 about card 使用 slivers，因此我们在这里不需要 SingleChildScrollView。而我们将需要饲料卡滚动部件。

拖拉部分还有另一个问题。再看一下那个视频，第一个用户拖动提要页面，直到工具栏折叠，或者我们应该说顶部用户图像折叠，然后提要页面可以无限滚动。

所以我们需要做的是。有什么猜测吗？

咄！！！！！如果你猜不到，就认为我们不能滚动内部项目，直到我们最大限度地展开外部项目。这意味着我们需要监听折叠的外部项目，然后为滚动设置状态。

在 flutter 中有多种滚动物理，我们将使用其中的两种，即永不滚动物理和弹跳滚动物理。

为什么从不滚动？嗯，我们不希望我们的用户在提要小部件没有完全展开的时候滚动它。因此，在用户将 feed 小部件展开到最大程度之前，我们会将永不滚动物理设置为滚动小部件。

因此，我们将需要一个滚动控制器和滚动物理在我们的饲料部件。

![](img/f24965341aefe9aa729e7b6984f5ae77.png)

feed widget: [link](https://github.com/parthdave93/FlutterUIDesigns/blob/master/lib/card_paginator_component/card_pagination_screen.dart)

好的，我们可能还会面临另一个问题。嗯，提要卡可以滚动和拖动，但如果用户在那个时候选择了提要卡，我们需要隐藏提要卡，我们可以设置提要卡的动画来隐藏向下，但现在，我们将在用户滚动提要卡到顶部的同时推动提要卡。

为此，我们将需要一个滚动控制器，但除此之外，我们将使用 NotificationListener 类。这个类帮助开发人员在小部件树项目发生变化时获得通知。就像这里，我们用长条滚动“关于”卡片，由于这个原因，滚动的细节正在改变。

![](img/ad453b3b5edee94f4701796794017774.png)

在尝试一些新的东西时，我们可能会面临一些问题，比如这里，我试图在长条上放置一张卡片，但内容溢出了，因为我想到的是定位部件的顶点，而不是底部，所以不要忘记这一点。此外，另一个东西 SliverToBoxAdapter，我还没有在任何例子中看到过，因为他们使用 SliverExpaned 或类似的东西，由于内容太多，将无法容纳我们的列，所以我尝试了太多的东西，并落在这个类上。最后但并非最不重要的是，我们将需要通知，银已被折叠，目前我还没有看到任何银类本身。所以我们需要使用的是 LayoutBuilder，它将给出我们的高度，我们从它开始构建 FlexibleSpaceBar，但在此之前，我们检查它可以折叠到的最小高度，并更新变量。目前，我们要硬编码的东西，但我们可以把它们分开在另一个文件中。

因此，我们已经完成了 UI 部分，现在剩下的是逻辑部分。

将解释所有的逻辑，但不放任何代码，你可以从文章末尾添加的链接签出存储库。

1.  计算所有初始点，就像从顶部放置提要小部件的所有分辨率一样。
2.  当我们拖动时，我们需要移动 feed，所以我们要将定位的小部件顶点与手势检测器结合起来，手势检测器将为我们提供拖动点，然后通过计算，我们将更新顶点并调用 setState 方法，因此用户会感觉他正在拖动 feed 小部件。
3.  一旦用户将提要卡拖动到 about 卡上，我们需要将长条向上推一点，为此我们将使用 about widget scroll controller。我们将用移动过的像素调用跳转方法。
4.  当我们拖动 about card 时，我们需要隐藏 feed card，方法是将它动画化到下方，虽然在这段代码中我们没有使用动画，我们只是使用 PositionedWidget 的顶部变量将它们向下推，但是用户会感觉到我们使用了动画，为此我们需要使用 NotificationListener 类来获取滚动点，该类使用 inheritance/InheritedWidget，切片的滚动会传播到这个小部件。通过确定我们要把 feed card 往下推一点。
5.  最后但并非最不重要的是，我们将听取控制器，滚动到达或没有结束。想想看，当我们将提要卡拖动到顶部时，一旦 slivers 将要折叠，我们将使用 NotificationListener 来更新提要卡的滚动物理，现在用户不能再将小部件拖动到顶部，但如果用户不想滚动并希望再次折叠小部件，该怎么办？在那个时候，我们需要有 NeverScrollPhysics 时，饲料小部件还没有滚动同样的关于小部件。

瞧啊。！我们做了视频中显示动画。浏览此链接:

[](https://github.com/parthdave93/FlutterUIDesigns/tree/master/lib/card_paginator_component) [## parthdave93/FlutterUIDesigns

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/parthdave93/FlutterUIDesigns/tree/master/lib/card_paginator_component) 

我知道这篇文章太长了，但是有很多东西要讲，所以我不能用一篇小文章来记录。我也在为这个系列准备视频 tuts，敬请期待。

希望你喜欢。

你知道你可以按拍手吗👏按钮 50 次？你走得越高，我就越有动力为你们写更多的东西！

你好，我是帕斯·戴夫。noob 开发者和 noob 摄影师。你可以在 [Linkedin](https://in.linkedin.com/in/parth-dave-907b8177) 上找到我，或者在 [GitHub](https://github.com/parthdave93) 上跟踪我，或者在 [Twitter](https://twitter.com/the_parth_dave) 上关注我？

祝你有一个愉快的飞行日！