# 使用 Codemods 的涡轮增压 JavaScript 重构

> 原文：<https://medium.com/airbnb-engineering/turbocharged-javascript-refactoring-with-codemods-b0cae8b326b9?source=collection_archive---------0----------------------->

![](img/75eca60d52502bbc7cb9de7d2958c9df.png)

乔·伦乔尼

在我的花园里种植和收获新作物很有趣，但如果我不定期除草，我最终会醒来发现一团糟。虽然每种杂草本身并不构成问题，但它们会合力堵塞整个系统。在一个没有杂草的花园里工作是一种富有成效的乐趣。代码库也是这样。

我也不喜欢除草，所以我忘了除草，结果惹上了麻烦。值得庆幸的是，在编码方面，我们有像 [ESLint](http://eslint.org/) 和[SCSS-林特](https://github.com/brigade/scss-lint)这样的好工具来确保我们在前进的过程中剔除杂草。然而，如果我们发现大块的遗留代码值得关注，我们可能会被手动调整一百万个空格和无数个悬空逗号的想法淹没。

在过去的 8 年里，数百万行 JavaScript 代码已经在 Airbnb 的源代码控制中被签入；与此同时，前端 Web 开发已经发生了巨大的变化。特性、框架甚至 JavaScript 本身都是移动的目标——尽管从一开始就遵循[一个好的风格指南](https://github.com/airbnb/javascript)将有助于最小化这种痛苦，但很容易以不再遵循当前“最佳实践”的代码库而告终。每一个小的不一致就像一棵小杂草，等着被拔掉，为一些有益的事情腾出空间，为一个更有生产力的团队铺平道路。看看我们花园的形状:

Before codemods

我痴迷于使团队更快，并且知道来自 linters 的一致的代码和信息收紧了反馈循环并减少了通信开销。我们最近开始了一个清理项目，准备大量的旧 JavaScript 来遵循我们的风格指南，并在更多的地方启用我们的 linters。手工完成所有的工作是非常乏味和耗时的，所以我们寻找工具来自动完成一些工作。虽然` *eslint* — *fix* 是一个很好的起点，但是[目前它能修复的](https://github.com/eslint/eslint/issues/5329)是有限的。他们最近已经开始接受自动修正任何规则的拉请求，并且正在努力为 JavaScript 实现一个具体的语法树，但是这还需要一些时间。谢天谢地，我们找到了脸书的 [jscodeshift](https://github.com/facebook/jscodeshift) ，这是一个 codemod 的工具包(codemod 是帮助大规模、部分自动化的代码库重构的工具)。如果一个代码库是一个花园，jscodeshift 就是一个机器人园丁。

该工具将 JavaScript 解析成[抽象语法树(AST)](https://en.wikipedia.org/wiki/Abstract_syntax_tree) ，应用转换，写出修改后的 JavaScript，同时匹配本地编码风格。转换本身是用 JavaScript 编写的，这使得我们的团队很容易使用这个工具。寻找或发明我们需要的转换加速了平凡的重构，这有助于我们的团队专注于更有意义的工作。

运行了几个 codemods 之后，我们的花园看起来稍微好了一点:

After codemods

# 策略

由于大多数 codemods 处理成千上万的文件只需要不到一分钟的时间，我发现当我在主线程上等待某些事情(例如代码审查)的时候，codemods 是一个很好的辅助任务。这有助于最大限度地提高我的工作效率，同时在更大或更重要的项目上取得进展。

我在进行大规模重构时遇到的主要挑战通常围绕着 4 C:沟通、正确性、代码审查和(合并)冲突。我使用了以下一些策略来帮助最小化这些挑战。

并不是所有的 codemods 在每种情况下都能产生我想要的精确结果，所以回顾和调整这些变化是很重要的。我发现在运行 codemod 后，以下命令非常有用:

```
git diff
git add --patch
git checkout --patch
```

小提交和拉请求是最好的，codemods 也不例外。我通常一次处理一个 codemod，以便于检查和解决合并冲突。我还经常提交 codemod 结果本身，如果需要的话，再提交一个手动清理。这使得在重定分支的基础时解决合并冲突变得更加容易，因为我通常可以

```
git checkout --ours path/to/conflict
```

并对该文件再次运行 codemod，而不干扰我的手动更改。

有时，codemod 会产生非常大的差异。对于这些情况，我发现对基于路径或文件名的代码片段使用单独的提交或拉请求是有帮助的。例如，一次提交修复*。js 文件和另一个提交修复*。jsx 文件。这使得审查更容易，并顺利合并冲突的解决。得益于对 Unix 哲学的坚持，在不同的片上运行 codemods 就像调整对“ *find* ”的调用一样简单:

```
find app/assets/javascripts -name *.jsx -not -path */vendor/* | \
  xargs jscodeshift -t ~/path/to/transform.js
```

为了避免踩到别人的脚趾，我在周五的早些时候推动 codemod 提交进行评审，然后在周一早些时候在大多数人重新开始工作之前进行重置和合并，这样做效果很好。这让人们有机会在周末之前完成他们正在做的工作，而不会被你的 codemod 妨碍。

# 对我们很有用的 Codemods

虽然这个工具还很年轻，但是已经有很多有用的 codemods 可以使用了。这里有一些我们迄今为止已经成功的例子。

## 轻量级代码模块

这些 codemods 非常有用，应用起来相对容易，给我们带来了立竿见影的效果。

[**js-code mod/arrow-function**](https://github.com/cpojer/js-codemod#arrow-function)**:**保守地将函数转换为箭头函数

之前:

```
[1, 2, 3].map(function(x) {
  return x * x;
}.bind(this));
```

之后:

```
[1, 2, 3].map(x => x * x);
```

[**js-code mod/no-vars**](https://github.com/cpojer/js-codemod#no-vars)**:**保守地将` *var* 转换为` *const* 或` *let*

之前:

```
var belong = 'anywhere';
```

之后:

```
const belong = 'anywhere';
```

[**js-code mod/object-速记**](https://github.com/cpojer/js-codemod#object-shorthand) **:** 将对象文字转换为使用 ES6 速记的属性和方法

之前:

```
const things = {
  belong: belong,
  anywhere: function() {},
};
```

之后:

```
const things = {
  belong,
  anywhere() {},
};
```

[**js-code mod/un chain-variables**](https://github.com/cpojer/js-codemod#unchain-variables)**:**un chain 链式变量声明

之前:

```
const belong = 'anywhere', welcome = 'home';
```

之后:

```
const belong = 'anywhere';
const welcome = 'home';
```

[**js-code mod/un quote-properties**](https://github.com/cpojer/js-codemod#unquote-properties)**:**从对象属性中删除引号

之前:

```
const things = {
  'belong': 'anywhere',
};
```

之后:

```
const things = {
  belong: 'anywhere',
};
```

## 重量级代码模块

这些 codemods 要么产生了更大的差异，这对于合并和避免冲突来说更具挑战性，要么它们需要更多的后续调整来确保代码看起来仍然不错。

[**react-code mod/class**](https://github.com/reactjs/react-codemod#class)**:**将` *React.createClass* `调用到 ES6 类中

这个 codemod 避免了 mixins 参与时的转换，并且它很好地完成了其他必要的转换，比如` *propTypes* `、默认属性和初始状态是如何定义的，以及在构造函数中绑定回调。

之前:

```
const BelongAnywhere = React.createClass({
  // ... 
});
```

之后:

```
class BelongAnywhere extends React.Component {
  // ...
}
```

[**react-code mod/sort-comp**](https://github.com/reactjs/react-codemod#sort-comp)**:**重新排序 React 组件方法以匹配 [ESLint react/sort-comp 规则](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

因为这会移动大量的大块代码，git 不会自动解决大多数合并冲突。我发现在运行这个转换之前最好进行充分的沟通，并且在最不可能出错的时候运行它(例如，在一个周末)。当我在这个基础上遇到冲突时，

```
git checkout --ours path/to/conflict
```

再次运行 codemod 是最好的方法。

之前:

```
class BelongAnywhere extends React.Component {
  render() {
    return <div>Belong Anywhere</div>;
  } componentWillMount() {
    console.log('Welcome home');
  }
}
```

之后:

```
class BelongAnywhere extends React.Component {
  componentWillMount() {
    console.log('Welcome home');
  } render() {
    return <div>Belong Anywhere</div>;
  }
}
```

[**js-code mod/template-literals**](https://github.com/cpojer/js-codemod#template-literals)**:**将字符串串联转换为模板文字

由于我们有许多字符串连接，并且这个 codemod 尽可能多地将其转换为模板文字，所以我最终发现了许多可读性较差的情况。我把这个 codemod 列在“重量级”部分，只是因为它涉及了太多的文件，需要大量的手动选择和调整才能获得最佳结果。

之前:

```
const belong = 'anywhere '+ welcomeHome;
```

之后:

```
const belong = `anywhere ${welcomeHome}`;
```

# 资源

无论您是想编写自己的 codemods，还是只是想了解什么是可能的，这里都有一些有用的资源:

*   Christoph Pojer 著复杂系统的渐进发展。在 JSConf EU 2015 上谈论脸书的 codemod(参见[有效的 JavaScript codemod](/@cpojer/effective-javascript-codemods-5a6686bb46fb))。
*   如何编写一个 codemod :指导你编写一个 codemod 来将字符串连接转换成模板文字的教程。
*   [AST Explorer](https://astexplorer.net/) :探索各种解析器生成的 AST 的 web 工具。这是一个很好的地方，您可以在这里体验一下，看看对于您想要转换的一些代码，AST 是什么样子的。
*   [NFL ♥ Codemods:迁移一个整体](/nfl-engineers/nfl-codemods-migrating-a-monolith-1e3363571707):NFL 如何使用 Codemods 的案例研究。
*   [React-codemod](https://github.com/reactjs/react-codemod):React 特定的 code mod 集合。
*   [js-codemod](https://github.com/cpojer/js-codemod) :通用 JavaScript codemods 的集合。

# 影响

使用可用的 codemods 和一些我们编写并反馈回来的代码，我们很快对旧代码做了很大的改进。我通过 codemod 不费吹灰之力修改了 40，000 行代码，这使得许多旧代码更好地符合我们的 ES6 风格指南。我们的菜园状况更好了，我们已经准备好迎接更快乐、更高产的收成。

运行已经可用的 codemods 只是触及了表面——当你拿起键盘开始编写自己的代码时，真正的力量就被释放了。Codemods 非常适合从样式重构到支持突破性 API 更改的各种修改，所以请尽情发挥您的想象力。这些技术非常值得投资，可能会为您和使用您项目的人节省大量时间和精力。

![](img/3913f6470a7657e02386189e67b4eb30.png)

## 在 [airbnb.io](http://airbnb.io) 查看我们所有的开源项目，并在 Twitter 上关注我们:[@ Airbnb eng](https://twitter.com/AirbnbEng)+[@ Airbnb data](https://twitter.com/AirbnbData)