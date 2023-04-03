# 使用 Go-Template 以简单的方式建立一个简单的网站

> 原文：<https://medium.easyread.co/building-simple-website-using-go-template-in-easy-way-36e271ec9c2b?source=collection_archive---------1----------------------->

> 我被隔离已经有一段时间了，一个人。有一天，我感到非常无聊，我没有动力做任何事情。玩我的 FIFA 也没什么帮助。我对自己说，“让我们做一些简单但有用的事情”。然后我做了一个显示[冠状病毒信息](http://corana-indonesia.herokuapp.com/)的网站。

![](img/2400040b5b8c5e5fa44dbd200bc3de6e.png)

Photo by [Glenn Carstens-Peters](https://unsplash.com/@glenncarstenspeters?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我用 Go-Template 开发了这个网站。基本上，因为我每天都在使用这种编程语言。我从印尼 [**伦理黑客**](https://kawalcorona.com/api/) 那里得到*冠状病毒信息*数据。

好，我们开始吧。

# 创建获取 API 的函数

第一步，我们需要创建一个从 API 获取数据的函数。这是一个简单的任务，尤其是当你使用 Go 作为你的主要编程语言的时候。

在获取数据之后，我们需要做的是将它们映射到一个结构，这样我们就可以在以后使用这些数据。

Map API response to Struct

这一部分还有几个步骤，比如，我不想在我收到的每个请求中都使用 API。取而代之的是，我用 cron 打 API 每 10 分钟更新一次数据。

# 创建模板

在这一步，我们至少需要知道如何使用 HTML、CSS 和 Javascript(如果你需要的话)。下面这个模板基本上就是一个 HTML 脚本，结合 CSS，看你的网站设计了。

Go Template — Example

通过使用模板，显然我们想要显示内容，这样用户就可以收到我们想要传递的东西。因此，我们需要将模板与从中获取数据的数据结构绑定在一起。为了从一个结构中获取数据，我们可以使用`**{{ .FieldName }}**`动作，在解析时用给定结构的值`**FieldName**`替换它。你可以在 [**Gopher Academy 博客**](https://blog.gopheracademy.com/advent-2017/using-go-templates/) 了解更多关于模板的内容。

# 连接结构和模板

有 3 个函数通常用于解析模板，

*   *新建* —分配一个新模板。
*   *解析* —你使用的模板，这个函数。
*   *Parse file*——与 *Parse* 基本相同，但写入不同的文件。
*   *必须—* 在解析过程中验证模板是否有效。
*   *执行—* 执行模板，将结构注入模板，并将结果写入给定的编写器

在我们的例子中，我们已经有了使用`**AttributeNationData**` struct 存储的冠状病毒信息数据，并且我们已经编写了模板。那么我们需要做的就是把它们连接起来。

这是它现在的样子

在上面的脚本中，我们将`**AllData**` struct 注入模板。`**AllData**` struct 内部有一片`**AttributeNationData**` struct。请返回到*创建模板*部分，再次看看模板，以及它是如何处理数据结构的。

更多详情可以访问我的 GitHub—[https://github.com/robertotambunan/corona-indonesia](https://github.com/robertotambunan/corona-indonesia)。不要期望代码是整洁的，因为这是一个突然的想法😆。如果您觉得有什么需要添加或修改以使其变得更好，请随时投稿。你也可以在这里看到直播网站—[http://corana-indonesia.herokuapp.com/](http://corana-indonesia.herokuapp.com/)

关于 Go 模板的进一步阅读，推荐大家使用 Go 模板 访问 [**。**](https://blog.gopheracademy.com/advent-2017/using-go-templates/)