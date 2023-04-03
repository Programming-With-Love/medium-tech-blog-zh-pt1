# Jest v18 到 v23 迁移注意事项

> 原文：<https://medium.com/walmartglobaltech/jest-v18-to-v23-migration-notes-b726cf6aafcf?source=collection_archive---------5----------------------->

![](img/0ef4eb5b632def4d0fe96fa0749bc84c.png)

Jest Image from [https://jestjs.io/](https://jestjs.io/)

Jest 是由脸书创建的一个令人愉快的 Javascript 测试框架。它被许多公司广泛使用，包括我们——沃尔玛网上杂货店。

我们很久以前就开始用 Jest 了(18 版)，但是直到最近才有机会升级。由于 v18 是一个过时的版本，我们因此缺乏许多主要的新功能，我们计划开始这次升级:从 v18 到 v23。

我的任务是从 v18 升级到最新的 v23。因为我找不到任何关于如何进行这种迁移的好文章，所以我决定写这篇文章。我将详细介绍在升级过程中遇到的 5 个主要问题。

# 为什么这么做？

Jest v23 让测试变得令人愉快，以下是一些亮点:

*   交互式快照模式:允许我们遍历每个失败套件中的每个失败快照，以查看失败的快照，并选择单独更新/跳过每个快照。
*   快照属性匹配器:允许我们向快照匹配器传递指定数据结构的属性，而不是特定的值。
*   Jest each:允许我们定义一个测试用例表，然后用指定的列值对每一行进行测试。
*   调试挂起测试:当 Jest 不存在时，允许我们调试并找到打开的句柄。
*   其他几个亮点:自定义异步匹配器、非对称匹配器和新匹配器等。

要查看完整的博客，请点击查看[。完整的变更列表，请参见](https://jestjs.io/blog/2018/05/29/jest-23-blazing-fast-delightful-testing)[变更日志](https://github.com/facebook/jest/blob/master/CHANGELOG.md)。

# 如何对作品进行分段？

由于沃尔玛在线杂货应用程序有一个很大的代码库，而我们拥有的 Jest 版本已经过时，我们需要提前计划如何分割迁移工作。

*   首先，我们需要更新全局状态中的依赖项和配置，以确保 Jest v23 可以成功地开始其测试工作。
*   然后我们可以通过模块/文件夹分开更新测试。
*   一切解决后，一起测试它们。

# 依赖关系和配置更新

![](img/d8e174124155e3aca678c3d367e01b19.png)

image from [https://www.lgc.org/wordpress/wp-content/uploads/2016/11/code-changes.jpg](https://www.lgc.org/wordpress/wp-content/uploads/2016/11/code-changes.jpg)

*   **package.json**

首先我们需要更新 [Jest v23](https://jestjs.io/docs/en/23.x/getting-started.html) 所需的版本依赖。

package.json:

```
...
"jest": "23.6.0",
"jest-localstorage-mock": "2.4.0",
...
```

*注意:* `[*jest-localstorage-mock*](https://www.npmjs.com/package/jest-localstorage-mock)` *模块用于运行依赖于 localStorage/sessionStorage 的 web 测试。我们的杂货应用程序有多个地方需要使用本地存储和会话存储 API 来模拟功能。*

确保运行`npm install` 来安装新模块。

*   **jest.config.js**

从 Jest v19 开始，`testPatDirs` 被重命名为`roots` 以避免在配置 Jest 时产生混淆。

```
{
  ...
  "roots": ["test/"]
}
```

# 常见错误和修复

![](img/d066ef1282f2d15ec3fe658186e75d21.png)

image from [Issues — uswarwatch.org](https://www.google.com/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwjXhamr-PXhAhXrwIsBHcDACeAQMwixASg4MDg&url=http%3A%2F%2Fwww.uswarwatch.org%2Fcategory%2Fissues%2F&psig=AOvVaw1DFAzOeDI7UiDfzyLcYuwJ&ust=1556649211421770&ictx=3&uact=3)

*   **快照故障**

Jest v19 增加了一个快照版本，并将 JSX 的右括号放在新的一行。这些突破性的变化将导致大量快照测试用例失败。为了解决这个问题，我们需要更新我们的快照工件。您可以使用一个标志来运行 Jest，该标志将告诉它重新生成快照:

```
jest --updateSnapshot
```

比较更改时，您应该会看到类似于以下内容的更改:

```
+ // Jest Snapshot v1, [https://goo.gl/fbAQLP](https://goo.gl/fbAQLP)
+-       framePadding="0" />
+       framePadding="0"
+     />
```

*   **JSDOM 重新配置**

从 JSDOM v11 开始，`window`上的`top`属性在规范中被标记为`[Unforgeable]`，这意味着它是一个不可配置的自有属性，因此不能被 JSDOM 中运行的正常代码覆盖或隐藏，即使使用了`Object.defineProperty`。

由于上述原因，您可能会看到如下错误消息:

```
TypeError: Cannot redefine property: pathname at Function.defineProperty (<anonymous>)...
> 744 |     Object.defineProperty(window.location, 'pathname', {
...
```

我们遵循的解决方案来自[这个线程](https://github.com/facebook/jest/issues/890)，它建议删除全局状态，并分配一个新的来解冻窗口位置对象。我们在`jest/setup.js`文件中设置了这个:

```
const oldLocation = global.window.location;
delete global.window.location;
global.window.location = {...oldLocation};
```

类似地，我们将相同的规则应用于窗口`sessionStorage` 对象:

```
const oldSessionStorage = global.window.sessionStorage;
delete global.window.sessionStorage;
global.window.sessionStorage = {...oldSessionStorage};
```

如果你想了解更多关于 JSDOM 的知识，你可以点击[这里](https://github.com/jsdom/jsdom#reconfiguring-the-jsdom-with-reconfiguresettings)。

*   **addSnapshotSerializer**

Jest 19+中提供了快照序列化程序，我们使用 Jest 的默认序列化程序: [enzyme-to-json](https://github.com/adriantoine/enzyme-to-json) 。这用于调整和格式化快照的输出，使其更具可读性。

# 退关货物

![](img/3653829a6732cc17e630d03bd927496e.png)

image from contenthub-static.grammarly.com

非常感谢我的经理桑杰·诺罗尼亚、我的导师大卫·奥查德、杰弗瑞·麦克里菲和阿梅亚·科什蒂，感谢他们一直以来的帮助和支持。也感谢 Dat Vong 帮助我评论这篇文章。