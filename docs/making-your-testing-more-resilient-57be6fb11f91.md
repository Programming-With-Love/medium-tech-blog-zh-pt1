# 让您的测试更有弹性

> 原文：<https://medium.com/walmartglobaltech/making-your-testing-more-resilient-57be6fb11f91?source=collection_archive---------6----------------------->

自动化测试对于我们作为一个软件组织如何快速自信地运作至关重要。在这个敏捷开发和持续部署的时代，拥有接近 100%的端到端功能测试可以决定软件产品的成败。

![](img/1911d4da12863e20484cec7fbbe0f541.png)

Credit: [pixabay.com](https://pixabay.com/photos/horse-racing-gallop-jockey-horses-4018609/)

浏览器测试是一个相当新的领域，主要由基于 Selenium 的 web 驱动程序来驱动特定于浏览器的 API。直到赛普拉斯最近才出现。关键的区别在于，Cypress 测试直接在浏览器中运行，而不是在 web 驱动中运行，这使得它更快更可靠。在很短的时间内，它就在质量工程师和开发人员社区中流行起来。

我职业生涯的大部分时间都是一名开发人员，从事全栈工作，习惯于单元测试。编写功能测试对我来说是新的，因为它主要是质量工程师的职责。但是自己编写一个功能性的端到端测试让我晚上睡得更好😴。当我知道我有一堆功能测试来*证明*我的工作时，我更有信心点击*合并*按钮。

用 cypress 编写功能测试既快速又简单。它的文档和直观的 API 使得新手升级没有麻烦。在我过去六个月的 it 之旅中，我遇到了许多顿悟的时刻，我愿意与工程师同事们分享。

1.**在柏使用** [***柏-插件-重试***](https://github.com/Bkucera/cypress-plugin-retries)

使用自动化软件运行功能测试不同于用户与它交互的方式。一个真正的用户*知道*屏幕上正在发生什么，并且能够适应它；与在浏览器上运行一组指令的脚本不同。应用程序 UI 的可变性可能是由于不同的 API 响应时间、网络速度、浏览器和服务器环境等造成的。重试失败的测试非常有助于抵消这种不可预测性😅

2.**服务器端使用*cy . request*进行渲染内容验证**

测试服务器端呈现的代码并不简单。当一个测试在浏览器中请求一个特定的 URL 时，脚本会立即执行并修改服务器呈现的代码。使用 cy.request 我们可以通过发出 HTTP 请求来请求 HTML 文档。一旦返回 HTML，我们就可以创建一个 HTML 文档，像我们期望的对象和查询元素。

3.**运行测试时自动生成模拟**

为您的数据提供模拟对于使测试可预测和更快是至关重要的。我们看到，在添加模拟后，CI 中的片状化和测试周期显著减少。然而，我讨厌添加模拟的部分是为无数用例手动添加模拟文件。使用这个简单的实用程序有助于生成 mock(如果不存在的话),或者用以前生成的 mock 进行响应。🙌

4.使用实时服务/模拟服务有选择地运行测试。

使用 mock 运行测试有其优势，但有时与 live API 同步并确保应用程序按预期运行同样重要。我们可以使用下面的包来正则表达式匹配测试名称，并运行没有模拟的测试。

5.**柏树司令部的无名英雄——cy . wrap，。如(' @…')**

*cy.wrap* 有时很方便，它帮助包装任何不是 cypress 命令直接结果的对象，并继续在其上执行命令。

。*as(…)*DOM 元素的别名有点独特。当您为在使用别名调用时已经从 DOM 中删除的 DOM 元素命名时，Cypress 会自动重新查询 DOM 以再次找到这些元素。

> 功能测试是伟大的，但是在某些情况下，比如服务人员，让它工作起来是很重要的。在这种情况下，我们总是可以依靠单元测试功能。服务工作者环境不同于浏览器或 Node.js 环境。服务-工人-模拟 npm 包产生了模拟服务工人环境。通过添加正确的事件侦听器和适当的缓存检查，很容易进行测试。

# 参考

[https://github.com/Bkucera/cypress-plugin-retries](https://github.com/Bkucera/cypress-plugin-retries)

[https://github . com/zackargyle/service-workers/tree/master/packages/service-worker-mock](https://github.com/zackargyle/service-workers/tree/master/packages/service-worker-mock)