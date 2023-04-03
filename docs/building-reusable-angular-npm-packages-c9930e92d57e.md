# 构建可重复使用的角度 NPM 包

> 原文：<https://medium.com/compendium/building-reusable-angular-npm-packages-c9930e92d57e?source=collection_archive---------0----------------------->

首席工程师 Magnus Stuhr

我的[前一篇博客文章](https://grensesnittet.computas.com/implementing-your-own-code-sharing-platform/)认为，编程最重要的原则之一是避免代码重复，而是重用现有的代码来实现相同的功能——它旨在提供关于任何人如何实现自己的代码共享策略的见解。这篇博文将通过展示一个具体的例子来进一步阐述这一点，这个例子将告诉你如何快速地开发和发布你自己的 Angular NPM 包，这些包可以在你的不同应用中重用。

当发布我们自己的 NPM 包时，我们需要做的第一件事是确保我们有一个 NPM 注册中心来推送我们的包。如果您想将您的包推送到公共存储库，您不需要设置任何身份验证。但是，如果您有某种类型的私有 NPM 注册中心，您需要遵循该存储库的身份验证设置说明，因为在本文中我们不会详细讨论这一点。无论哪种方式，你都应该得到一个*。项目根文件夹中的 npmrc* 文件，包含应用程序应该从中检索包的注册表列表:

**。npmrc**

```
@<your_registry_name>:registry=<your_registry_url>
always-auth=true
```

# 构建和发布 NPM 包

本节包括如何构建和发布您自己的 NPM 软件包的分步指南。

首先，当开发 NPM 包时，我们通常希望包含对等依赖和开发依赖，而不是常规的直接依赖。对等依赖是包含我们自制的 NPM 包所需的包，而开发依赖是用于开发我们的 NPM 包的依赖。我们应该包含库的常规依赖，这些库只在我们的 NPM 包中需要，并且不需要在包范围之外被引用。完整文档见 [package.json 依赖关系](https://docs.npmjs.com/files/package.json#dependencies)。

# 1 所有组件和服务都应该有自己的“index.ts”文件

当编码 NPM 包时，确保正确地导出你的代码，以便从外部正确地使用它。我们所有的组件和服务都应该有自己的 index.ts 文件来导出功能。

**<组件/服务> /index.ts**

```
export * from '<your_component/service_name.component/service/module>';
```

示例:

```
export * from './dialog-provider.module';
export * from './dialog-provider.service';
```

# 2 你的应用程序模块应该有一个“index.ts”文件

最后，我们需要创建一个包含所有模块功能的入口点:

**索引. ts**

```
export * from '<your_component/service_paths>'; (no file extension declaration after path)
```

示例:

```
export * from './dialog-provider';
```

# 3 为正确的建筑增加吞咽集成

当构建我们的包时，我们需要将 HTML 和 CSS 内联到 Angular 组件装饰器中。

# 3.1 安装 Gulp 插件

```
npm install gulp gulp-inline-ng2-template --save-dev
```

# 3.2 添加新的吞咽任务

如果您的项目结构不同，请确保包含您自己的相对路径，而不是下面的路径。

```
const inlineNg2Template = require('gulp-inline-ng2-template');
gulp.task('inline-build-templates', () => {
    return gulp.src(['./src/app/**/*.ts', '!./src/app/**/**.spec.ts', '!./src/app/app.*'])
        .pipe(inlineNg2Template({
            target: 'es5',
            useRelativePaths: true,
         }))
         .pipe(gulp.dest('./build'));
});
```

# 4 在 package.json 的“脚本”对象中添加构建步骤

```
"scripts": {
...
	"inline-build-templates": "gulp inline-build-templates",
	"ngc-build": "ngc -p ./tsconfig.lib.json
...
}
```

# 5 安装类型

我们需要安装必要的类型，以便软件包构建时没有错误。

```
npm install --save-dev @types/core-js
```

# 5.1 确保您的根文件夹中有一个“tsconfig.lib.json”文件

**tsconfig.lib.json**

```
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "moduleResolution": "node",
    "sourceMap": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "removeComments": false,
    "noImplicitAny": true,
    "declaration": true,
    "outDir": "./lib",
    "stripInternal": true,
    "lib": [
      "es6",
      "dom"
    ]
  },
  "include": [
    "./build/*"
  ],
  "files": [
    "./node_modules/@types/core-js/index.d.ts"
  ],
  "angularCompilerOptions": {
    "skipTemplateCodegen": true
  }
}
```

# 5.2 为我们的包添加类型脚本类型和入口点

**package.json**

```
...
"main": "./lib/index.js",
"typings": "./lib/index.d.ts"
...
```

# 6 将“lib”文件夹添加到 package.json 中的“files”数组中

“lib”文件夹是我们构建的包的原始输出。

```
"files": [
...
   "lib"
...
 ]
```

# 7 添加 rimraf 集成

添加 rimraf-integration，以便清除每个构建的现有文件。

# 7.1 安装 rimraf

我们希望确保新版本中没有旧文件。

```
npm install rimraf --save-dev
```

# 7.2 将 rimraf build 命令添加到 package.json 文件中的“scripts”对象

```
"scripts": {
    ...
   "cleardown": "rimraf build lib",
   "package": "npm run cleardown && npm run inline-build-templates && npm run ngc-build"
    ...
}
```

# 8 构建您的包

```
npm run package
```

# 9 将您的包推送到 NPM 注册中心

对于您的最终版本(即 1.0.0 版):

```
npm publish
```

对于测试版(即 1 . 0 . 0-1 版):

```
npm publish --tag beta
```

恭喜你，你的包现在应该已经被推到 NPM 注册！

# 马格努斯·斯托尔

*首席工程师 Computas 数据科学社区负责人，meetup.com 语义技术小组组织者。最重要的是热爱编码。*