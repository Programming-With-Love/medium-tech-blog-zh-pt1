# 了解合成浏览器事件

> 原文：<https://medium.com/square-corner-blog/understanding-composition-browser-events-f402a8ed5643?source=collection_archive---------4----------------------->

什么是 IME，我为什么要关心？

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

我们的 Square 卖家需要对他们的整个业务有一个统一的视图，这就是 Dashboard 的用武之地。Dashboard 是一个大型前端 Ember 应用程序，使卖家能够通过分析、报告和各种服务有效地运营他们的业务。我们不断为 Dashboard 添加新功能，以帮助我们的卖家利用 Square 平台做更多事情。

最近，我的团队一直在开发一个特殊的功能，用于在 Dashboard 中搜索各种产品。当用户在搜索框中键入内容时，一个简短的搜索建议列表会显示在输入内容的下方，如果他们按下 enter 键，一个新的标签会打开，显示所有结果。看起来很简单，对吧？

不幸的是，当我开始用英语之外的语言测试这个搜索框时，我注意到了一些非常不可取的行为:当我仔细地构思我的搜索查询时，突然一个新标签会不知从哪里打开！我决定进行调查，最终我了解到了一些关于浏览器和其他语言的文本组成的非常有趣的细节。但是首先，让我们回顾一下什么是 IME。

An input method editor, or IME, is an operating system-level program that converts characters from one language or character set to another language or character set. For example, let’s say that I wanted to type the character 桜 (Japanese for “cherry blossom”). I don’t have that character on my keyboard, but if I enable an IME on my OS, I can set my input source as Japanese and type the Romanization of the phrase: “sakura”. Let’s see what happens!

![](img/8848bc7c08c167e2e45fe7e4f7b9a556.png)

当我开始键入时，文本下方会出现一条下划线，表示 IME 处于活动状态。当我的操作系统试图智能地决定什么是最好的表示时，英文字符会自动转换成各种日文字符集。(像日语这样的许多语言有数百个同音词，因此对于一个发音，可能有许多可行的转换。)在这个过程中的任何时候，我都可以按键盘上的空格键或上/下箭头键来打开一个对话框，让我选择哪一组字符与我想表达的意思相匹配。

From this point, hitting enter will lightly confirm my selection, letting me use the left and right arrow keys to move to another section of my sentence to convert next. For my simple example, I don’t have any other sections to convert, so I can hit enter a second time to commit my decision. Now, the character 桜 is placed in the input, and the IME disappears. 桜 acts just like any typical text, so it can be deleted, copied/pasted, and so forth. Character conversion via an IME is one of the primary ways that users from various countries are able to type in their native languages with ease.

好的，太好了！现在我们知道 IME 是如何工作的了。那么是什么问题呢？

请注意，为了与 IME 交互，需要进行大量的键盘输入——我们可以使用箭头键、空格键、回车键等等。起初，我天真地认为，由于 IME 是一个操作系统程序，任何输入或编辑都将存在于浏览器之外，因此浏览器将不会注意到我正在使用 IME 的事实。但事实并非如此！IME 中的每一次按键都会被操作系统**和浏览器**响应！因此，当用户按下回车键来确认他们的 IME 选择时，我们的代码会立即处理一个 keydown 事件，打开一个新的选项卡，即使用户可能还没有完成他们的搜索查询。不好！如果代码处于这种状态，我们会让许多 Square 卖家用他们的母语使用我们的搜索框变得非常麻烦。

*(重要提示:这个 bug 不仅限于回车键！如果您有一个监听任何与 IME 控制方式一致的事件的输入，那么您的代码中可能会有类似的 bug。)*

那么如何才能修复呢？我花了 [研究](https://www.stum.de/2016/06/24/handling-ime-events-in-javascript/)的[位](https://developer.mozilla.org/en-US/docs/Web/Events/compositionstart) [，但我最终了解到了两个我以前从未遇到过的浏览器事件:`compositionStart` 和`compositionEnd`。当用户开始用 IME 输入时，现代浏览器触发`compositionStart`事件，当文本最终被确认时，`compositionEnd`将被触发(有一个例外—见下文)。这正是我们所需要的！现在，我们可以在应用程序中设置一些状态，关于用户当前是否正在通过 IME 编写一些文本，如果是，我们将不使用任何自己的键盘事件逻辑。代码如下所示:](https://readable-email.org/list/webkit-dev/topic/handling-ime-composition-events)

万岁！这在我们在 Square 支持的浏览器中运行良好，只有一个例外。当用户想要完成他们的合成时出现问题；在这种状态下，`isComposing`是真实的。首先，我们来看看快乐案例中的事件链。

*   用户按回车键结束合成。
*   浏览器启动`keyDown`，在`keyDown()`中运行我们的代码。`isComposing`是真的，所以我们提前退出。
*   浏览器启动`compositionEnd`，在`handleCompositionEnd()`中运行我们的代码。`isComposing`被设置为假。
*   用户再次点击 enter。
*   浏览器再次触发`keyDown`，但是由于`isComposing`现在为假，我们运行我们的自定义代码。

这是用户应该期望的；只有在完成撰写之后，后续的 enter presses 才应该在新的标签中打开他们的搜索查询。然而，在我的测试中，Safari 似乎以相反的顺序触发了`keyDown`和`compositionEnd`事件。逻辑路径如下:

*   用户按回车键结束合成。
*   Safari 启动`compositionEnd`，在`handleCompositionEnd()`中运行我们的代码。`isComposing`被设置为假。
*   Safari 启动`keyDown`，在`keyDown()`中运行我们的代码。`isComposing`是假的，所以我们运行我们的自定义代码。

换句话说，在新标签页中打开查询时，完成合成的回车键会被重复计算一次，这是一种糟糕的体验。为了解决这个问题，我将`handleCompositionEnd()`中的行包装在一个 Ember 队列中，这样它将总是在`keyDown`事件之后运行:

最终，我们的输入成功地处理了来自多个来源的文本输入！这是一个非常酷的 bug，我花了很长时间才找到它，但是一旦我理解了它，就有了一个非常好的解决方案。一些个人心得:

*   **考虑与你大相径庭的用户很关键。**这适用于国际化(不同的输入源、翻译时的内容移动大小、从右向左的语言)和可访问性(盲人用户的屏幕阅读器、仅键盘访问)。如果你只测试你使用软件的方式，你可能会错过你的用户群的重要部分！
*   如果你最终写了一堆非常复杂的逻辑，感觉很粗糙，最好后退一步，重新评估你的选择。当我开始做这个的时候，我不知道合成事件，并且尝试了一些非常荒谬的事情来检测用户是否正在通过 IME 打字。这种痛苦迫使我做更多的研究，最终我找到了一个干净的解决方案。
*   MDN 文档中有一些隐藏的宝石。😉此外，[这个网站](https://caniuse.com/#feat=ime)对于找出跨浏览器的奇怪之处是无价的。

感谢阅读！如果你对此感兴趣，看看 Square 这里的一些[职位空缺——我们一直在寻找有才华的工程师加入我们的团队！](https://squareup.com/careers/jobs?role=Engineering)