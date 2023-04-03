# React 本机应用的基本要求

> 原文：<https://medium.com/quick-code/basic-requirements-for-react-native-app-8820067e1c50?source=collection_archive---------5----------------------->

## (杂货清单应用程序)

![](img/40a8c27033d0113a0218cfc3489c6ab9.png)

Image Copyright — Pixabay.com — free images

这是第三篇文章，关于我公司的发展历程。

我们将在首次发布时构建一个简单的购物清单应用程序。可以阅读 [**第一篇**](/quick-code/if-you-have-recipe-based-website-and-want-to-have-an-application-too-5da1a17737d8) 和 [**第二篇**](/quick-code/how-i-plan-to-get-from-app-idea-into-working-prototype-food-tech-92f1fdcc432a) ，以获取更多信息。

*   我们将选择一个 React 原生 UI 套件
*   我们将把我们的应用程序部署到 [**Expo.io**](https://expo.io/)
*   我们将使用我们的 npm 模块，它包含 json 测试数据(数据库文件)
*   我们将为搜索功能使用自动完成元素
*   我们将使用 [**ESLint**](https://eslint.org/) 和[**AirBnB styles addon**](https://www.npmjs.com/package/eslint-config-airbnb)
*   我们将使用 [**代码质量**](https://www.codacy.com/) 进行代码评审&
*   我们将使用 [**哨兵发布**](https://docs.sentry.io/learn/releases/) 来发布更好的代码
*   我们将使用 [**git-flow**](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) ，这样我们就不会在开发者之间产生交集
*   我们将使用 PR 来将您的代码推入 master，而我将是一名审阅者
*   我们将使用 [**bin-flow 包**](https://github.com/facebook/flow)
*   为了让 react 组件为跨项目做好准备，我们将使用 [**位**](https://bitsrc.io/)
*   我们将使用 [**Jest +酶进行测试**](https://facebook.github.io/jest/)
*   我们将使用 [**Travis CI**](http://travis-ci.org) 来设置测试覆盖
*   我们将连接到我们的 API 服务器。我们应该准备与远程方法相关的文档，这将生成我们的 API 端点。为了调用 API，我们将使用 [**Axios**](https://github.com/axios/axios) 。
*   在下一阶段，我们将从 REST API 切换到 [**graphQL**](https://graphql.org/) ，因此我们应该将我们的 API 请求与组件逻辑分开存储
*   我们将使用 [**GitBook**](https://www.gitbook.com/) 来保存更新的文档
*   我们将使用 [**脸书登录**](https://www.npmjs.com/package/react-native-facebook-login) 进行注册流程。 [**教程**](https://developers.facebook.com/docs/react-native/login)
*   我们应该保持良好的组件结构。
*   我们应该创建一个发布计划。对此，我们将遵循亚马逊的方法
*   我们将创建一个应用程序流程图，这将为我们的开发团队节省大量时间
*   我们将使用开源包，因为我讨厌重新发明轮子

感谢你到达终点。多鼓掌来表达你的爱！

如果你想了解我如何管理我的项目，请点击下面的链接

[](/quick-code/how-i-manage-my-projects-c17c78c796c4) [## 我如何管理我的项目

### 2013 年是我第一次使用“任务跟踪器”。在那之前，我的团队领导使用螳螂(臭虫追踪器)。客户寄给我…

medium.com](/quick-code/how-i-manage-my-projects-c17c78c796c4)