# 使用电极将 React 服务器端渲染性能提高了 70%

> 原文：<https://medium.com/walmartglobaltech/using-electrode-to-improve-react-server-side-render-performance-by-up-to-70-e43f9494eb8b?source=collection_archive---------1----------------------->

![](img/c7696a7c7039c8d8480d75ef2dc41731.png)

我们构建了[电极](/walmartlabs/introducing-electrode-an-open-source-release-from-walmartlabs-14b836135319#.7j2n6ec6x)，react/node.js 应用平台为 walmart.com 提供了几个目标，包括易用性、跨应用的组件可重用性，以及最重要的性能。

我们在几乎所有的应用程序中使用服务器端渲染有两个原因:

1.  为客户提高性能
2.  更适合搜索引擎优化

然而，在我们的测试中，我们发现 React 的`renderToString()`需要相当长的时间来执行——由于`renderToString()`是同步的，服务器在运行时会被阻塞。每个服务器端渲染执行`renderToString()`来构建应用服务器将发送给浏览器的 HTML。

为了解决这个问题，我们创建了两个电极模块:[折叠渲染(ATF)](http://www.electrode.io/docs/above_fold_rendering.html) 和[服务器端渲染剖析和缓存(SSR 缓存)](http://www.electrode.io/docs/server_side_render_cache.html)。让我们看看这些是如何工作的，使用 Walmart.com 主页应用程序作为我们的用例。这是**真正的**首页应用，不是被嘲讽的 app。它包括 10 个旋转木马(很多交易！)，而且它已经使用了我们的性能增强 [redux-router-engine](http://www.electrode.io/docs/redux_router_engine.html) 。出于测试的目的，它运行在 Macbook Pro 上，而不是商用服务器硬件上(这在`renderToString()`性能上有很大差异)。

# 基线

作为基线测试，我们首先配置应用程序，没有 ATF 模块，没有 SSR 缓存模块，有`renderWithIds:true`，我们同步运行它 30 次。这种配置下的平均`renderToString()`通话时间为 **153.80 毫秒**。

# 电极默认值— renderWithIds:False

不过，默认情况下，电极带有`renderWithIds:false`，这给我们一个`renderToString()`时间 **124.80 毫秒**。不错——电极的默认配置已经提高了 19%的渲染时间！

# 服务器端渲染配置文件和缓存

接下来，我们添加了页眉、页脚和转盘的 [SSR 缓存](http://www.electrode.io/docs/server_side_render_cache.html)，这为我们提供了 96.80 ms 的平均`renderToString()`。非常好！这比 Electrode 的默认值提高了 23%。

# 在折叠渲染之上

假设我们没有实现缓存，而是实现了仅用于渲染的 [ATF 渲染](http://www.electrode.io/docs/above_fold_rendering.html)。这样，我们的`renderToString()`时间为 **65.73 ms** 。哇，降低了 48%！也就是说，很多旋转木马的价格都低于正常水平。

# SSR 缓存+ ATF 渲染

最后，让我们一起使用 [SSR 缓存](http://www.electrode.io/docs/server_side_render_cache.html)和 [ATF 渲染](http://www.electrode.io/docs/above_fold_rendering.html)。这样一来，我们的`renderToString()`时间一路下降到了**36.56 ms**——与默认的 Electrode 配置相比，提高了 71%，与原始的未优化测试相比，提高了 76%。还要记住，这是同步代码，这意味着它会阻止 Node.js 应用程序执行任何其他操作，直到完成为止。我们还没有将`DOMContentLoaded()`引入到讨论中，但是通过 [ATF 渲染](http://www.electrode.io/docs/above_fold_rendering.html)，我们看到了近 30%的改进，因为我们只是将内容呈现在文件夹的上方，然后立即在客户端启动 React 渲染。

# 结论

这就是了，电极棒极了，你应该使用它。将您的反应服务器端性能提高高达 70%。我们正在使用这个@ WalmartLabs，通过这两个模块(+ redux 路由器引擎)，我们的生产应用程序的性能得到了显著提升。如您所见，主页的本地性能提高了 70%。

但请记住，这些测试是在快速硬件上运行的。在商品服务器 CPU 上，`renderToString()`可能需要两倍的时间。这意味着 70%的改进并不意味着 90 ms，而是节省了 180 ms。

# **特别感谢:**

感谢[阿鲁内什·乔希](https://medium.com/u/f034b4cfef9f?source=post_page-----e43f9494eb8b--------------------------------)、[德米特里·耶辛](https://medium.com/u/f658c22b63be?source=post_page-----e43f9494eb8b--------------------------------)和主页团队，他们已经实现了电极模块，并正在生产中的主页上使用它们。

感谢[石](https://medium.com/u/e16a73e715e3?source=post_page-----e43f9494eb8b--------------------------------)，感谢他帮助我们收集数据，确保我们得到正确的数字！

# **更多信息:**

查看 [Joel Chen](https://medium.com/u/9f67cc98eb00?source=post_page-----e43f9494eb8b--------------------------------) 关于 [ReactJS SSR 剖析和缓存](/walmartlabs/reactjs-ssr-profiling-and-caching-5d8e9e49240c#.q8jblqb6z)的帖子或 [Arpan Nanavati](https://medium.com/u/d8fa8407b711?source=post_page-----e43f9494eb8b--------------------------------) 关于[构建企业级 React.js 的帖子](/walmartlabs/building-react-js-at-enterprise-scale-17c17a36fd1f#.9j3wmrza6)。

[查看我关于 Electrode 发布的帖子，这是支持 Walmart.com 的面向客户的平台](/walmartlabs/introducing-electrode-an-open-source-release-from-walmartlabs-14b836135319#.7j2n6ec6x)。

焊条网址: [www.electrode.io](http://www.electrode.io)