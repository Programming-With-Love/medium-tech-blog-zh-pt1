# 在 iOS 单元测试中使用存根与模拟

> 原文：<https://medium.com/capital-one-tech/using-stub-vs-mock-in-ios-unit-testing-634ec4cc6a10?source=collection_archive---------0----------------------->

## 为什么这些稍微相似的物体不能互换

![](img/412f1913ed46af46832328ac7bde6756.png)

存根和模拟对象之间有非常细微的差别。在我职业生涯的早期，我知道它们是什么，但不知道正确使用它们的重要性。在应该使用存根的地方使用模拟可能会导致脆弱和难以维护的测试。此外，在我们应该使用模拟的地方使用存根可以防止我们捕捉到意外的有害行为，这些行为会让本应该失败的测试通过。

为了帮助确保我们团队中的所有 iOS 工程师都在同一页面上，并理解存根和模拟之间的差异，我决定以博客帖子的形式交流这一点。在这篇博文中，我将讲述如何为 iOS 项目创建和使用可靠且易于维护的存根和模拟。

# 什么是存根和模拟？

首先，让我们定义我们的术语模拟和存根。

## 烟蒂

存根只是为我们的测试返回假数据。仅此而已。

## 模拟的

mock 比 stub 稍微复杂一些。它返回一些假数据，还可以验证是否调用了特定的方法。

# 存根和模拟之间的细微差别

测试*并不真正关心函数是否在存根上被调用，只要测试对象(或被测系统)从存根上获得它需要的数据并做正确的事情。*

对于一个模拟，我们能够*验证模拟对象上的*方法调用的事实意味着我们确实*关心*它做了什么。我们通常可以在模拟对象上指定我们期望*调用*什么方法。因此，如果在测试动作之后没有调用我们期望的方法，那么当我们验证模拟对象时，测试应该会失败。此外，如果发生了意想不到的事情——例如，在模拟对象上调用了一个意想不到的方法——验证模拟对象上的方法调用应该会使测试失败。

# Swift 单元测试示例

下面是创建模拟类进行测试的最常见的方法之一。假设我们正在测试一个具有多种功能的手机屏幕。

```
**// code
struct** MoreViewController {
    **let** viewRenderer: ViewRendererProtocol

    **func** loadFeatureOne() {
      viewRenderer.renderFeatureOne()
    } **func** loadFeatureTwo() {
      viewRenderer.renderFeatureTwo()
    }
}**// mock
class** MockViewRenderer: ViewRendererProtocol { **private(set)** **var** hasCalledRenderFeatureOne = **false
    func** renderFeatureOne() {
      hasCalledRenderFeatureOne = **true** } **private(set)** **var** hasCalledRenderFeatureTwo = **false
    func** renderFeatureTwo() {
      hasCalledRenderFeatureTwo = **true** }
}// usage
**func** test_loadFeatureOne_featureOneRendered() {
    **let** mockViewRenderer = MockViewRenderer()
    **let** sut = MoreViewController(viewRenderer: mockViewRenderer) // when
    sut.loadFeatureOne() // then
    XCTAssertTrue(mockViewRenderer.hasCalledRenderFeatureOne)
    XCTAssertFalse(mockViewRenderer.hasCalledRenderFeatureTwo)
}
```

以上是创建模拟/伪造对象的一种基本方法。但是随着我们项目的扩展，我们可以在上面的例子中发现一些问题。

有时，当我们向协议中添加一个新函数并更新它的 fake/mock 类时，很容易忘记为这个新行为添加断言。在上面的例子中，如果 ViewRendererProtocol 有十个特性，并且我们的每个测试只关心是否调用了一个函数，那么在测试结束时，我们会有一个很长的`XCTAssertFalse`列表。

# 开源拯救世界

有很多开源框架可以帮助我们创建模拟或伪造的对象。在本文中，我们将使用一个名为 [SwiftMock](https://github.com/mflint/SwiftMock) 的开源模仿框架。有了这个框架，我们就不用担心在新函数上调用断言了。它还帮助我们捕捉意外的函数调用，因此不需要“XCTAssertFalse”所有其他不应该被调用的函数。

SwiftMock 的思想是，它持有一个预期动作的数组(一个函数名的字符串数组)，当在 Mock 对象上调用函数时，动作被从数组中删除。最后，它验证最终的数组是否为空。如果为空，则意味着所有预期的动作都已被调用，并且没有任何意外的动作被调用。

这里有一个例子，展示了我们如何使用 SwiftMock 使用相同的代码库创建模拟，但这次功能的外观取决于功能切换值。

```
**// code to test
struct** MoreViewController { **let** featureToggle: FeatureToggleProtocol
    **let** viewRenderer: ViewRendererProtocol **func** loadFeatures() { **if** featureToggle.isFeatureOneOn() {
        viewRenderer.renderFeatureOne()
      } **if** featureToggle.isFeatureTwoOn() {
        viewRenderer.renderFeatureTwo()
      }
    }
}**// stub and mock
class StubFeatureToggle: FeatureToggleProtocol {
    var featureOneEnabled = false
    func isFeatureOneOn() -> Bool {
      return featureOneEnabled
    }** **var featureTwoEnabled = false
    func isFeatureTwoOn() -> Bool {
      return featureTwoEnabled
    }
}****class** MockViewRenderer: Mock<ViewRendererProtocol>, ViewRendererProtocol {
    **func** renderFeatureOne() {
      accept()
    } **func** renderFeatureTwo() {
      accept()
    }
}// test
**func** test_loadFeatures_featureOneRendered() {

    // given
    // .create() is one way a create a mock object for the SwiftMock framework
    **let** mockViewRenderer = MockViewRenderer.create()
    **let** stubFeatureToggle = StubFeatureToggle.create()
    stubFeatureToggle.featureOneEnabled = **true
    let** sut = MoreViewController(
      featureToggle: stubFeatureToggle,
      viewRenderer: mockViewRenderer) // expect
    mockViewRenderer.expect { object **in** object.renderFeatureOne() } // when
    sut.loadFeatures() // then
    mockViewRenderer.verify()}
```

SwiftMock 框架为我们提供了`**.**expect` 和`.verify()` 。注意，这个版本在验证阶段稍微简单一些，我们可以通过调用`verify()`对预期和意外的动作进行相同的检查。

对于存根，我们保持它应该的简单。当`isFeatureOneOn()`被调用时，`stubFeatureToggle`就返回 true。

# 所以我们为什么不把一切都变成一场模拟呢？

因为模拟可能是维护的负担，尤其是当我们的测试并不真正关心的时候。

现在，让我们重新看看同一个代码示例。开始时，可能只有一个功能。

```
**struct** MoreViewController { **let** featureToggle: FeatureToggleProtocol
    **let** viewRenderer: ViewRendererProtocol **func** loadFeatures() { **if** featureToggle.isFeatureOneOn() {
        viewRenderer.renderFeatureOne()
      }
    }
}
```

我们可能有一个单元测试，其中的`FeatureToggle`对象是一个模拟对象。

```
**func** test_loadFeatures_featureOneRendered() { **let** mockViewRenderer = MockViewRenderer.create()
    **let** mockFeatureToggle = MockFeatureToggle.create()
    mockFeatureToggle.featureOneEnabled = **true
    let** sut = MoreViewController(
      featureToggle: mockFeatureToggle,
      viewRenderer: mockViewRenderer) // expect
    mockFeatureToggle.expect { object **in** object.isFeatureOneOn() }
    mockViewRenderer.expect { object **in** object.renderFeatureOne() } // when
    sut.loadFeatures() // then
    mockFeatureToggle.verify()
    mockViewRenderer.verify()
}
```

在这种情况下，我们的测试期望调用`isFeatureOneOn()`，并且断言调用了`renderFeatureOne()`。

如果我们在屏幕上添加第二个特性，相同的`ViewController`现在将调用`featureToggle.isFeatureTwoOn()`。

```
**func** loadFeatures() { **if** featureToggle.isFeatureOneOn() {
      viewRenderer.renderFeatureOne()
    } **if** featureToggle.isFeatureTwoOn() {
      viewRenderer.renderFeatureTwo()
    }
}
```

由于`FeatureToggle.isFeatureTwoOn()`上的“意外调用”，这将中断现有的单元测试`test_loadFeatures_featureOneRendered()`。

```
**error: -[StubVsMockTests.StubVsMockTests test_loadFeatures_featureOneRendered] : failed — Unexpected call: isFeatureTwoOn()**
```

因此，我们必须在`test_loadFeatures_featureOneRendered()`上添加`featureToggle.isFeatureTwoOn()` 的期望值，以消除错误。

然后，如果我们添加第三个特性，`ViewController`将调用`featureToggle.isFeatureThreeOn()`，这将破坏我们的`featureOne`和`featureTwo`测试。为了通过测试，我们现在需要更新两倍的内容。

记住，我们的测试只关心`ViewController`是否调用了正确的渲染函数。它不关心`ViewController`是如何做到的。我们的测试是脆弱的，因为假的`FeatureToggle`是模拟的而不是存根。

# 结论

俗话说*小不忍则乱大谋*。Stub 和 mock 是软件测试领域中不应该被忽视的两个小概念。不正确地使用它们意味着你的单元测试会变得脆弱和/或不可靠。这会导致难以维护的代码库和/或糟糕的软件质量。后果还在继续。

然而，这些并不是新的挑战。有许多众所周知的工具和实践可以帮助我们在单元测试中使用存根和模拟。我们在这里刚刚谈到了其中的一些。只要你*理解*你所使用的概念、工具和实践，你将在维护和/或修复你的软件项目上节省大量的时间和精力。

*披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*