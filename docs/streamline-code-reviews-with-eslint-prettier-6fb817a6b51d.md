# 使用 ESLint+appearlier 简化代码审查

> 原文：<https://medium.com/javascript-scene/streamline-code-reviews-with-eslint-prettier-6fb817a6b51d?source=collection_archive---------2----------------------->

## 猎枪视频集

![](img/991d0b9dbb03bab579fb88c41f2f5453.png)

Photo: [Brandon Bailey](https://www.flickr.com/photos/luxurydesigner/) — Midnight Cowboy (CC-BY-2.0)

在最新一集的《埃里克·埃利奥特的猎枪》中，这是一个为[EricElliottJS.com](https://ericelliottjs.com)成员制作的视频系列，我们通过[完成了设置 ESLint 和 appetter](https://ericelliottjs.com/shotgun-postamp-episode-one-linting)的过程，以自动化 JavaScript 项目的语法检查和代码风格管理过程。

## 为什么自动化 Lint 和代码风格很重要？

**ESLint** 自动扫描您的 JavaScript 文件，查找常见的语法和样式错误。

**更漂亮**扫描您的文件的样式问题，并自动重新格式化您的代码，以确保遵循一致的规则，如缩进、间距、分号、单引号和双引号等。

我们在团队中使用它们是因为:

*   他们让每个人都在同一页上，遵循同样的规则。
*   **它们节省了代码审查的时间**，因为我们可以安全地忽略所有的风格问题，并专注于真正重要的事情，比如我们代码的结构和语义。
*   **他们捕捉错误**。漂亮点，没那么漂亮，但是 ESLint 实际上捕捉了很多语法错误和简单形式的类型错误，比如未定义的变量。
*   **设置这些东西是一次性成本**，但节省时间的好处会随着时间的推移而增加。

## 配置更漂亮的以使用 ESLint

Prettier 可以作为 ESLint 的一个插件运行，它允许你用一个命令来 Lint 和格式化你的代码。在我看来，任何简化开发过程的方法都是成功的。beauty+ESLint 是开发者天堂里的一对。

![](img/8e84b32f8c9805ce04944159c9301f19.png)

It’s dangerous to go alone! Take this.

如果你曾经试图同时运行 beautiful 和 ESLint，你可能会与冲突的规则作斗争。放心吧！你不是一个人。插入[eslint-config-appellister](https://github.com/prettier/eslint-config-prettier)，所有 ESLint 的冲突样式规则都会自动为你禁用。

如果你还没有使用 [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react) ，它可以提醒你添加 PropTypes 到你的组件中，并且[eslint-plugin-React-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)可以帮助你发现用户在使用 React hooks API 时遇到的常见问题。首先安装所有这些组件，如`devDependencies`:

```
npm install --save-dev eslint eslint-config-prettier eslint-plugin-prettier eslint-plugin-react eslint-plugin-react-hooks prettier
```

接下来，您需要一些配置文件。首先，让我们忽略一堆我们不想用`.eslintignore`处理的东西:

```
node_modules
.next
```

和`.prettierignore`:

```
package-lock.json
.next
node_modules/
```

你需要一把`.eslintrc`。这是我的:

```
{
  "env": {
    "browser": true,
    "commonjs": true,
    "es6": true,
    "node": true
  },
  "plugins": [
    "react",
    "react-hooks"
  ],
  "extends": ["eslint:recommended", "plugin:react/recommended", "plugin:prettier/recommended"],
  "parserOptions": {
    "sourceType": "module",
    "ecmaVersion": 2018
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  },
  "rules": {
    "linebreak-style": ["error", "unix"],
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

还有一个`.prettierrc`。这是我的:

```
{
  "singleQuote": true
}
```

现在你只需要给`package.json`添加一个`"lint"`脚本:

```
"lint": "eslint --fix . && echo 'Lint complete.'"
```

完成后我喜欢附和。否则，它只是在没有错误时保持沉默。

我还把林挺加入到我的观察脚本中，就像这样:

```
"watch": "watch 'clear && npm run -s test | tap-nirvana && npm run -s lint' src",
```

如果你从未使用过`watch`，你需要在使用之前安装它:

```
npm install --save-dev watch tap-nirvana
```

如果你是一个 Windows 用户，我推荐使用 Windows 的 Linux 子系统。我不能保证没有它所有的脚本都能正常工作。

## 立即尝试 Zeit

> 免责声明:这里不需要免责声明。没人给我任何东西来说我将要说的话。

在视频中，我谈到了 Zeit 现在有多酷。成员应该[观看视频](https://ericelliottjs.com/shotgun-postamp-episode-one-linting/)来看看 GitHub 钩子的连续部署。

Zeit Now 是一个很棒的托管服务，可以轻松地与 GitHub 集成，使用无服务器技术为您提供端到端的连续部署。不确定这意味着什么或如何编写“无服务器”应用程序？没问题。只需使用 [Next.js](https://nextjs.org/) 并让 Next 和 Now 为您处理所有细节。

这就像拥有世界上最好的 DevOps 团队——而不需要雇佣全职开发人员来简化您的持续交付流程。Zeit 在托管和开发时间上为我们节省了资金。

由于 Next 的自动捆绑分裂、服务器端渲染和超快的无服务器响应时间，我们的应用程序比以往任何时候都服务得更快，它们甚至与 [Cloudflare CDN](https://www.cloudflare.com/) 集成，以将世界各地的延迟降至接近零。

## 关键视频要点

*   **甚至当我在做原型时，我也使用 TDD。**当您第一次开始使用 TDD 时，创建一个初始构建可能要多花 15% — 30%的时间。有了一两年的经验，首先编写测试实际上可以节省您的时间，因为您花费更少的时间来更改代码、刷新页面以及遍历工作流来测试您的 UI 更改。
*   **自动化的 lint 和代码格式化通过捕捉错误并让开发人员在同一页面上，让您的团队将代码审查集中在更有意义和更有成效的主题上，从而使开发人员更有成效**。
*   **试试** [**Zeit 现**](https://zeit.co/now) **。**
*   **连接一个监视脚本**在你编码时运行，自动 lint 并在每次文件保存时运行你的单元测试。

[![](img/649c1c875d8140aa42e9e3d9ffedf8e5.png)](https://ericelliottjs.com/premium-content/lesson-pure-functions)

[Start your free lesson on EricElliottJS.com](https://ericelliottjs.com/premium-content/lesson-pure-functions)

***埃里克·艾略特*** *是本书的作者，* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](http://pjabook.com) *。作为*[*EricElliottJS.com*](https://ericelliottjs.com)*和*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，他教授开发者必备的软件开发技能。他为加密项目组建开发团队并为其提供建议，并为 Adobe Systems、* ***、Zumba Fitness、*** ***《华尔街日报》、*******ESPN、*******BBC、*** *以及包括* **在内的顶级录音师提供软件体验*****

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**