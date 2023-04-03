# 用 Buck 构建混合语言 iOS 项目

> 原文：<https://medium.com/airbnb-engineering/building-mixed-language-ios-project-with-buck-8a903b0e3e56?source=collection_archive---------2----------------------->

在 Airbnb，我们认识到开发者经验是优秀工程的关键。我们的团队——Mobile Developer Infra——的目标是优化我们移动应用的构建时间。

六月，我们获得了[美元](https://buckbuild.com/)成功开发我们的 iOS 应用。这对我们来说是一个巨大的里程碑:在我们开始工作之前，Buck 不支持混合语言的 iOS 项目，我们的 iOS 代码库由 Swift 和 Objective-C 组成，随着这一变化，我们看到**CI 构建速度提高了 50%**并且**应用程序大小减少了 30%**。

达到这个里程碑是一个复杂的过程。在本帖中，我们想分享一些技术细节，关于我们面临的挑战，以及我们如何在我们的 iOS 代码库中使用 Buck。希望这对其他对类似事业感兴趣的人有用。

# iOS 应用程序是如何构建的

当我们研究 Xcode 到底是如何构建一个 iOS 项目的时候，我们发现了 [**这个牛逼的帖子**](https://www.bignerdranch.com/blog/manual-swift-understanding-the-swift-objective-c-build-pipeline/) ，里面详细解释了这个过程。

简而言之，构建过程包括以下步骤:

1.  将桥接头文件(作者维护)和 Swift 源文件传递给`swift`工具，生成两种文件:
    a) `.o`，用于生成最终可执行二进制的机器码文件。
    b) `*-Swift.h`，包含 Swift 代码中定义的所有类和接口。这些可以在 Objective-C 文件中显式导入，以便使用 Swift 代码中定义的功能。
2.  将所有的 Objective-C 源文件和`*-Swift.h`文件传递给`clang`工具，为每个 Objective-C 文件生成`.o`文件。
3.  将所有`.o`文件传递给`ld`命令，该命令将链接所有机器码文件并生成最终的可执行文件。

# 降压转换器和 Xcode 的区别

Buck 使用大致相同的过程来构建 iOS 项目。然而，有一个关键的区别使得支持像我们这样的混合语言项目更具挑战性。

Xcode 独立构建每个模块，产生动态链接的框架。对于一个特定的模块`M`，可执行二进制文件和相关的资源/资产在最终的 app 文件夹中的`App.app/Framework/M.framework`下结束。

Buck 将这些模块视为静态库，将它们链接在一起，生成一个可执行的二进制文件。这种方法可以有效地减少二进制文件的大小，因为:
a)如果多个模块正在使用相同的资源/资产，则不需要将相同的文件复制到每个`*.framework`文件夹中。b)它可以去除更多未使用的符号，因为所有库都是静态链接在一起的。

这在纯 Objective-C 或纯 Swift 项目中非常有效。不幸的是，这种优化给混合语言项目带来了问题。

# ***标志导入底层模块*不工作**

`-import-underlying-module` build 标志导致 Objective-C 文件在同一模块内隐式导入 Swift。不幸的是，这面旗子不适用于巴克。

当 Xcode 生成框架时，它会生成`module.modulemap`和`.hmap`头文件来指示头文件的位置。`swift`工具稍后使用这些文件导入 Objective-C 头文件。然而，由于 Buck 不生成独立的框架，所以它不生成这些文件。因此，`-import-underlying-module`标志在`swift`工具中不起作用。

这意味着我们必须显式地将桥接头传递给`swift`工具。然而，这样做会导致更多的问题。

## 无法使用桥接标头

考虑[这个](https://github.com/airbnb/BuckSample/tree/master/src/ImportObjC)例子:`A.h` 包含了`#import “B.h”`这条线，但是`B.h`放在了`folderB/`下面。在`.hmap`的帮助下，这与 Xcode 完美配合。但是在 Buck 中，这不起作用，因为它不能定位`B.h`。

在[这个 PR](https://github.com/facebook/buck/pull/1164) 中，我们更新了 Buck，使其能够生成供`swift`工具使用的头文件，允许工具定位头文件并导入它们。

## 在*-Swift.h 中找不到桥接标头

当`swift`工具生成`*-Swift.h`文件时，它显式导入 Objective-C 定义的桥接头。这打破了巴克建立。

根据[苹果的代码](https://github.com/apple/swift/blob/e7a0ba84339e318853f17f6a2a6b72af6bbe2917/lib/PrintAsObjC/PrintAsObjC.cpp#L2569)，当使用`-import-underlying-module`标志时，生成的`*-Swift.h`文件导入项目头。例如:

然而，当显式提供桥接头文件时(就像我们需要 Buck 做的那样)，生成的`*-Swift.h`文件最终直接导入桥接头文件:

如您所见，导入的路径是一个相对路径。当另一个文件导入这个`*-Swift.h`文件时，它将无法定位桥接头。

在[提交](https://github.com/airbnb/buck/commit/8a1908d3d84b18ef7b53b14903087fe7045a7277)中，我们更新了 Buck 来传递`-iquote buckRootPath`作为编译器参数。这明确地告诉`swift`工具在`buckRootPath`寻找桥接头文件。

## `@import`不起作用

将头文件导入 Objective-C 有两种方式，`#import`和`@import`。`@import`在 Buck 中不起作用，因为 Buck 不产生`module.modulemap`。

这实际上要求我们将`@import M`替换为`#import <M/M.h>`和/或`#import <M/M-Swift.h>`。这对于我们自己的源代码来说已经足够简单了。然而，对于生成的代码来说，这有点棘手。例如，`*-Swift.h`文件总是使用`@import`。

为了解决这个问题，我们使用了一个公认的黑客解决方案，引入[这个脚本](https://github.com/airbnb/BuckSample/blob/master/scripts/transform_buck_swift_header.py#L31)来动态执行替换。在[这个提交](https://github.com/airbnb/buck/commit/93453a5730445c0dd799872b6e8bba49233430d7)中，我们向 Buck 的`apple_library`构建规则添加了一个新的`objc_header_transform_script`参数，它允许我们调用所有`*-Swift.h`文件上的替换脚本。

这个变化打倒了我们最后一个使用 Buck 的大盖帽。

# 巴克样本

为了帮助说明我们所做的工作，我们创建了这个[示例](https://github.com/airbnb/BuckSample)项目，您可以随意克隆它并亲自测试它！

**构建混合语言库**
如前所述，在构建混合语言库时，我们需要将桥接头文件传入`apple_library`构建规则。

**构建协同 pod** 我们将每个 pod 视为一个单独的库，对每个 pod 使用不同的构建规则。

在大多数情况下，pod 包括其源代码，因此我们可以简单地使用`apple_library`来构建它。

当一个 pod 只提供一个编译过的二进制文件时(例如`libSample.a`，我们使用`[prebuilt_cxx_library](https://buckbuild.com/rule/prebuilt_cxx_library.html)`构建规则。

当一个 pod 提供一个框架文件时，我们使用`[prebuilt_apple_framework](https://buckbuild.com/rule/prebuilt_apple_framework.html)`构建规则。此构建规则的更多示例可在[这里](https://github.com/facebook/buck/tree/7ccef04e22ea0289c17c9289a455da6879a9243d/test/com/facebook/buck/apple/testdata/prebuilt_apple_framework_builds)找到。

## 下一步是什么？

我们仍然面临一些挑战:

1.  启用`buck project`生成 Xcode 项目文件。我们计划的工作流程包括在本地开发中使用 Xcode，在 CI 中使用 Buck。
2.  升级 Buck 来构建模块库，这样`-import-underlying-module`就可以工作，我们的黑客就可以被移除了。
3.  为 iOS 优化降压缓存。我们在 Buck 缓存机制中发现了一些改进的空间，并将继续对其进行投资。
4.  进一步分析从 Xcode 切换到 Buck 能获得多少收益。

如果您有任何问题/反馈，请随时联系我们。如果你想帮助我们应对这些挑战，请加入我们！