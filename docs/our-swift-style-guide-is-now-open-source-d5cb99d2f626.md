# 我们的 Swift 风格指南现已开源

> 原文：<https://medium.com/airbnb-engineering/our-swift-style-guide-is-now-open-source-d5cb99d2f626?source=collection_archive---------1----------------------->

我们在 Airbnb 使用 Swift 已经四年了。语言变了，我们的做法也变了。今天，我们分享如何编写 Swift。

![](img/aa257f8771316988917eb7a3d112cb1f.png)

Some of Airbnb’s native engineers talking in our Native Product Development tech talk last August

# Airbnb 的 Swift 简史

在 2014 年 WWDC 上，苹果用一种新语言让我们大吃一惊:Swift。在 Airbnb，我们很快就抢先一步，在 2014 年 8 月写下了我们的第一行 Swift——甚至在 Swift 达到 1.0 之前[。](https://developer.apple.com/swift/blog/?id=14)

当年晚些时候，苹果发布了 Apple Watch 和 WatchKit。鉴于社区对传闻已久的 Apple Watch 的兴奋，2015 年 4 月，我们打了一个赌，开始完全在 Swift 1.1 中编写第一个版本的 [Airbnb 的 Apple Watch 应用程序](/airbnb-engineering/how-it-ticks-building-the-airbnb-apple-watch-app-663288dc52e8)。

在 2015 年 WWDC 上，苹果正式发布了 Swift 2.0。因此，当需要构建我们的[苹果电视应用](/airbnb-engineering/mastering-the-tvos-focus-engine-f8a13b371083)时，我们尝试了一下，并使用 Swift 2.0 编写了它。

在这两次成功的试点之后，2016 年 1 月，我们决定在 Swift 2.0 中编写我们所有的新功能。一切都进行得很顺利，直到[大迅捷更名](https://github.com/apple/swift-evolution/blob/master/proposals/0005-objective-c-name-translation.md)落地。为了升级到 Swift 3，我们指派了一个两人小组在 5 周的时间内迁移我们的代码库[。谢天谢地，升级到 Swift 4 简单多了；自 2017 年 10 月以来，我们的整个代码库都在 Swift 4 中。](/airbnb-engineering/getting-to-swift-3-at-airbnb-79a257d2b656)

# 为什么是风格指南？

Swift 是一门年轻的语言。当我们在 2014 年开始使用它时，我们没有标准化的 Swift 风格指南。我们让 15 名工程师在我们的代码库中自由活动，他们每个人都以自己的风格写作。很快就清楚了，如果我们不同意一个标准化的风格，我们要么在我们的 PRs 中花太多时间讨论风格，要么我们的代码库就会像一幅[杰森·布拉克的画](https://github.com/airbnb/javascript/issues/102)。

当我们在开发苹果电视应用程序时，我们开始了一个非正式的风格指南。在【2016 年 1 月这与其他特别的努力相结合，成为官方的 [Airbnb Swift 风格指南](https://github.com/airbnb/swift)，在那里我们开始合作定义在 Airbnb 写 Swift 的首选方式。

苹果公司和 Swift 社区为如何编写 Swift 提供了宝贵的指导。尽管他们绝对影响了我们在 Airbnb 编写 Swift 的方式，但我们仍然认为保持我们自己的风格指南是有价值的，因为这是一种重复我们认为正确的方式，同时与社区保持一致。这就是为什么我们将苹果的建议加入到我们的[指导原则](https://github.com/airbnb/swift#guiding-tenets)中。

我们不想手动识别和纠正违反样式指南的情况，所以我们采用了 Swift 社区中最流行的 linter 和 formatter，分别是 [SwiftLint](https://github.com/realm/SwiftLint) 和 [SwiftFormat](https://github.com/nicklockwood/SwiftFormat) 。在我们的[风格指南](https://github.com/airbnb/swift)中，您会找到我们的 [SwiftLint](https://github.com/airbnb/swift/blob/master/resources/swiftlint.yml) 和 [SwiftFormat 配置](https://github.com/airbnb/swift/blob/master/resources/airbnb.swiftformat)。如果你想使用和我们一样的规则，只要抓住它们并开始在你的项目中使用它！

# 为什么我们要开源它？

自从我们第一次开始编写 Swift 以来，几年过去了，社区已经对一些模式进行了标准化。Swift 的语言和社区对我们很有帮助，所以我们想分享一些我们在 Airbnb 编写 Swift 的模式。

我们明白，不是每个人都会同意我们在 Airbnb 的做事方式。我们相信尊重他人的不同意见，我们很乐意听到您的反馈，我们很乐意分享我们的想法。如果你不同意或者认为我们遗漏了什么，我们欢迎你的贡献！

# 拥抱 Swift

在 Airbnb，我们认为 Swift 是 iOS 开发的未来，我们将继续推动前沿原生技术的发展。鉴于我们正在[淘汰 React Native](/airbnb-engineering/sunsetting-react-native-1868ba28e30a) ，我们将在内部 Swift 和 Kotlin 库上投入更多资源。

[我们 70%的新原生 Android 代码是用 Kotlin 编写的，90%的 iOS 是用 Swift](https://twitter.com/michaelbachand/status/1027267287210815489) 编写的。*我们不断迁移到最新的语言功能。

**不包括遗留 React 本机代码。*

*如果你对 Swift 和 iOS 开发充满热情，我们鼓励你申请我们已经开放的* *的* [*不同职位！我们希望你能加入我们的团队！*](https://grnh.se/1cb1ce351)