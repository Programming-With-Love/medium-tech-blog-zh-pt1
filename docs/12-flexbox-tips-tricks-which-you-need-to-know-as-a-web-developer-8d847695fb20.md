# 作为一名 web 开发人员，你需要知道的 12 个 CSS Flexbox 技巧和诀窍

> 原文：<https://medium.com/quick-code/12-flexbox-tips-tricks-which-you-need-to-know-as-a-web-developer-8d847695fb20?source=collection_archive---------0----------------------->

![](img/948e5a7b4da33859c9cf07dc16be52c8.png)

[Duomly — programming online courses](https://www.duomly.com)

本文最初发表于:[https://www.blog.duomly.com/flexbox-cheatsheet/](https://www.blog.duomly.com/flexbox-cheatsheet/)

可能前端行业的几乎每个人都听说过 flexbox，以及当我们需要设计网格时它能给我们带来的好处。在这篇文章中，我想解释什么是 flexbox，以及我们如何使用它来节省我们的时间。

Flexbox 是一个 CSS 布局模块，它允许我们以一种快速友好的方式设置行为良好且响应迅速的网格。让我们看看 flexbox 的一些最重要的元素，我们可以在日常工作中使用它们。

# 1.如何将内容靠左对齐

很多时候，我们希望在页面的一侧设置我们的内容，这有点类似于左边的浮动，但不是 100%相同。我们可以通过指定容器的属性 flex-direction: row 将元素向左对齐。

# 2.如何将内容右对齐

和前面的例子一样，我们将内容向一侧对齐。这一次，我们将内容与容器的右侧对齐。

我们可以通过指定容器的属性 flex-direction: row-reverse 将元素向右对齐。

# 3.如何将内容水平居中对齐

我经常使用的，也是在没有 flex 的情况下设置起来更具挑战性的，是将内容放在容器的中心。

如果我们想让元素在水平位置居中，我们需要在容器中添加属性 justify-content: center。

# 4.如何证明-内容

如果我们想要调整我们的元素，我们需要添加一个类似的属性，就像前面的例子一样，但是我们使用的是方法 space-between，而不是 center，所以我们的属性应该看起来像 justify-content: space-between

# 5.如何设置每行一个元素

如果我们想在垂直列中设置元素，我们也可以这样做。

要设置一个列，我们需要用 flex-direction: column 指定我们的容器。

# 6.什么是灵活增长

Flex-grow 是一个属性，指定我们的元素应该占用多少空间。

默认情况下，flex-grow 设置为 1，这意味着我们的元素占用属于他的所有空间(例如，如果我们有 4 个元素，一个元素占用 25%的空间)。但是我们可以用 flex-grow: xx(其中 xx 是数字)来指定目标元素，例如，如果我们只将一个元素设置为 flex-grow: 2，那么这个元素占用的空间是其余元素的两倍。

# 7.如何在容器顶部设置元素(align-items:flex-start)

如果我们想在容器顶部设置所有子元素，我们可以通过向容器添加 align-items:flex-start 来轻松实现。

# 8.如何在容器底部设置元素(align-items:flex-end)

与前面的例子相反，我们在容器的底部指定项目，我们需要添加一个类似的属性，但是我们用 end 代替 start。

我们的属性应该是 align-items:flex-end。

# 9.如何垂直和水平对齐居中元素(对齐项目:居中)

更有用的是，在没有网格的情况下，通常是通过绝对位置来完成的，现在我们可以做得非常简单。为了使元素垂直和水平居中，我们需要在容器中指定两个属性。首先是 align-items: center，第二个是 justify-content: center。

# 10.如何用物品实现整个集装箱的高度(拉伸)

有时我们希望物品的高度是容器高度的 100%。我们可以通过几种方式来实现，但现在我想向您展示一个带有 flexbox 的解决方案。为此，您只需将容器的属性设置为 align-items: stretch。瞧啊。

# 11.如何设置元素的宽度

如果我们想要设置一些与元素数量相关的元素宽度呢？

我们可以使用 flex 来做到这一点，而且速度非常快！为此，我们可以将所有项目设置为 flex: 1，并将我们希望比 rest 大 2 倍的元素设置为 flex: 2。

# 12.如何设置网格

使用 flexbox，我们可以在几分钟内建立一个漂亮的网格。在这个例子中，我们设置了一个标准的博客网格，包括菜单、博客文章、侧边栏和页脚。

为此，我们从在容器中设置 flex-flow: row wrap 开始。接下来，我们需要将所有的元素设置为 flex: 1 100%,将所有的元素设置为 100%宽度。现在我们可以进入网格的主体，通过设置 flex:30；和侧边栏长度(按 flex ): 10；这意味着我们的边栏长度应该是边栏长度的 3 倍。仅此而已！我们已经创建了简单的网格！

# 结论

我希望现在您已经了解了 flexbox 的基本知识，并且您应该能够在日常工作中使用它们。Flexbox 的工作效率更高，您可以更深入地了解它，但是我想告诉您一些我在日常工作中使用的方法，以及哪些方法可以提高我的工作效率并节省我的时间。

感谢阅读！

![](img/2bebe9fe48fb99c5d1c4456e97533030.png)

[Duomly — programming online courses](https://www.duomly.com)