# 在 NPM 分享排版和调色板

> 原文：<https://medium.com/compendium/sharing-typography-and-color-palette-as-sass-mixins-through-npm-4e7925cd6600?source=collection_archive---------1----------------------->

*作者埃里克·塔朗——高级知识工程师*

随着公司 web 应用程序数量的增长，维护应用程序间一致的版式和调色板所需的工作也在增加。这个问题的一个好的解决方案是创建一个共享的设计包来处理最基本的设计配置。通过将它从 GUI 组件中分离出来，该库就前端框架而言变得不独立，因为它没有耦合到例如 React 或 Angular。在本文中，我们将制作一个简单的 NPM 包，包含一个调色板和一个排版配置，作为 SASS mixin 提供给消费者。如果你想了解更多关于共享可重用代码的知识，你可以看看 Magnus Stuhr 关于[构建可重用 Angular NPM 包](https://grensesnittet.computas.com/building-reusable-angular-npm-packages/)的文章。

小贴士:

阅读时看看[范例库](https://github.com/eTallang/shared-design-demo)可能是个好主意，因为这样更容易理解。

# 设置结构

由于共享设计包的用途很窄，存储库中的文件数量非常少。本质上，唯一需要的是一个./src 文件夹来包含所有的样式，以及一个[gupp](https://gulpjs.com/)-脚本来在我们想要[发布 NPM 包](https://docs.npmjs.com/getting-started/publishing-npm-packages)时将它们合并在一起。我们将回到为什么合并文件，以及本文后面的另一种方法。

# 定义颜色

在我们的例子中，我们将在调色板中创建四种颜色和两种灰色。首先在。/src 文件夹包含我们所有的颜色和色调:

```
// Colors
$calm-blue: #2196F3;
$teal: #009688;
$amber: #FFC107;
$red: #D32F2F;// Greys
$dark-grey: #282828;
$light-grey: #dedede;
```

这些都被定义为 SASS 变量，这样就可以从依赖于我们共享设计包的项目中引用它们。

# 定义排版

前面描述的调色板由 SASS 变量组成，无论如何，只要消费者愿意，就可以使用这些变量。另一方面，字体设计并不是这样工作的。

因为我们的排版配置将是全局的，所以将它实现为 SASS mixin 是一个很好的实践，这样我们的包的消费者可以决定他/她是否或者何时想要使用它。这也是防止污染全局样式表的一个很好的防护措施，因为我们的主题文件的每次导入都会将排版配置添加到应用程序中。出于本文篇幅的考虑，我只展示一部分排版配置。要查看完整的配置，请参见[示例-存储库](https://github.com/eTallang/shared-design-demo)。

```
@import './palette.scss';@mixin demo-typography() {
  * {
    font-family: 'Montserrat', sans-serif;
    font-style: normal;
    font-weight: normal;
  } h1, h2, h3, h4, h5, p, b, strong, i, em, mark, small, del, ins, sub, sup {
    color: $dark-grey;
  } h1 {
    font-weight: bold;
    font-size: 3rem;
    line-height: 4rem;
  } p, b, strong, i, em, mark, del, ins {
    font-size: 1.2rem;
    line-height: 2rem;
  } .small-typography {
    h1 {
      font-size: 3.1rem;
      line-height: 2.6rem;
    } h2 {
      font-size: 2rem;
      line-height: 2.7rem;
    } p, b, strong, i, em, mark, del, ins {
      font-size: 1rem;
      line-height: 2rem;
    }
  }
}
```

正如你可能注意到的，我们还没有使用媒体查询来为我们的移动排版定义断点。这自然是你在自己的项目中可以做到的事情。在我们的例子中，我们已经将移动排版实现为一个 CSS 类，因此可以使用 [@angular/flex-layout](https://github.com/angular/flex-layout) 轻松地将其切换为一个根级 CSS 类。这是防止在应用程序中重复相同媒体查询断点的好方法:

```
<div ngClass.xs=”small-typography”> // Root element of our application
```

ngClass.xs-attribute 是围绕 Angular ngClass 属性的 flex-layout 包装器，如果屏幕的宽度在[超小(xs)范围](https://github.com/angular/flex-layout/wiki/Responsive-API)内，它会将“小字体”CSS-class 添加到 div-element 中。

# 融合在一起

到目前为止，我们的项目在。/src 文件夹，即。/src/palette.scss 和。让我们的库对消费者来说更加用户友好的一个很好的方法是让它更容易使用软件包的不同部分，而不必直接引用不同的文件:

```
@import '~@my-package/shared-design-demo/typography';
@import '~@my-package/shared-design-demo/palette';
```

随着我们设计库的增长，这种方法不能很好地扩展，因为包中的每个新文件都需要一个新的导入行给消费者。通过我们的包暴露我们的内部文件结构也不是一个好主意。这阻止了我们重命名或重构我们的样式，因为这将在我们的消费者应用程序中引入一个突破性的变化。因此，我们将创建一个用作公共访问点的文件，其中包含我们“私有”文件中的所有样式。

对此有两种方法。我们可以创建一个“index”SASS 文件，它简单地导入。/src 文件夹。这是最简单的方法，因为它允许我们直接发布我们的包，不需要任何进一步的构建步骤。缺点是，如果在。/src-folder。另一种方法是使用 Gulp 创建一个简单的构建脚本，将。/src-folder，然后发布我们的包。我们将选择后者，只是为了让这篇文章更有趣一些。该脚本放在 gulpfile.js 中，要求我们在项目中添加 gulp 和几个 gulp 插件:

```
npm install gulp gulp-concat gulp-scss-combine -D
```

脚本如下:

```
'use strict';var gulp = require('gulp');
var sass = require('gulp-scss-combine');
var concat = require('gulp-concat');gulp.task('default', mergeSass);function mergeSass(done) {
  return gulp.src(['src/palette.scss', 'src/*.scss']) // Reads all files in src
    .pipe(concat('theme.scss')) // Merges all of the files into theme.scss
    .pipe(sass()) // Serves to remove @import statements from the output
    .pipe(gulp.dest('./')); // Writes the file to root
}
```

它所做的就是从我们的。/src 文件夹，从我们的调色板开始。这只是为了确保调色板变量被放在我们文件的顶部，因为它们被用于例如排版。该脚本还确保删除@import 语句，例如排版顶部调色板的@import。这是必要的，因为我们库的输出文件夹将不包含 palette.scss 文件，因此库会抛出一个错误。

# 准备发布

我们的共享设计包所需的几乎所有部分现在都已就绪。剩下的工作就是创建一个 NPM 脚本来使用我们新创建的 Gulp 脚本，并确保我们的 theme.scss 文件包含在我们的 NPM 包中。为了处理这个问题，我们的 package.json 是这样的:

```
{
  "name": "@my-package/shared-design-demo",
  ...
  "scripts": {
    "merge": "gulp"
  },
  "files": [
    "theme.scss"
  ],
  "devDependencies": {
    "gulp": "^3.9.1",
    "gulp-concat": "^2.6.1",
    "gulp-scss-combine": "^1.0.0"
  }
}
```

通过“npm run merge”运行我们的脚本，现在应该会在我们的根文件夹中生成一个名为 theme.scss 的文件。现在剩下的就是运行“npm publish — access=public”来将我们的包发布到 npm！

# 最后的想法

通过使用一个共享的 NPM 包来定义 web 应用程序中的通用设计元素，我们可以在改变应用程序中应该相同的设计时节省大量时间。值得一提的是，我们的排版配置为我们的排版外观的每个方面提供了一种自以为是的方法。如果更具可配置性的方法是首选，可以改变排版混合以接受输入参数。例如，这些可以包括字体系列或您希望可配置的排版的其他方面。

# 奥姆·埃里克·塔朗

Erik 拥有计算机科学硕士学位，在该行业有三年的工作经验，主要专业领域是前端开发。过去的一年致力于深入到角，和杂项补充图书馆，如角材料。