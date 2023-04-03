# 如何在 SwiftUI 和 UIKit 之间选择？

> 原文：<https://medium.com/globant/can-swiftui-overcome-uikit-fb669d9fdc54?source=collection_archive---------0----------------------->

![](img/bb6700177678eb7021e32bfc1adbec57.png)

[Image Source](https://www.freepik.com/free-vector/app-development-illustration_10354235.htm#query=ios&position=1&from_view=search)

随着时间的推移，世界正在快速变化并采用新技术。苹果已经在 iOS 13 版本中引入了 **swiftUI。我们仍在使用 UIKit 开发 iOS 应用程序，swiftUI 仍处于开发阶段苹果在 swiftUI 方面每天都在改变。大家心里都有一个疑问，SwiftUI 未来能战胜 UIKit 吗？答案是肯定的。让我们深入比较 swiftUI 与 UIKit 的优缺点。**

# **UIKit**

UIKit 是苹果的旧框架，用于开发 iOS 应用程序。它有**故事板和界面构建器的 xib**。

*" Xcode 中的界面构建器编辑器使设计完整的用户界面变得简单，无需编写任何代码。只需将窗口、按钮、文本字段和其他对象拖放到设计画布上，就可以创建一个正常工作的用户界面。”*

这就是[苹果的文档](https://developer.apple.com/documentation/uikit)对界面构建器的描述。

**我们来了解一下 UIKit 的一些优缺点:**

## **优点:**

*   即使是初学者也很容易学会。
*   使用 UIKit 和故事板，你可以创建原型，这些原型可以在故事板界面上可视化。UIKit 中的故事板可以减少代码。
*   它有大量的可用对象，因此很容易构建任何功能和任务。
*   UIKit 已经有 10 多年的历史了，所以它使用起来非常稳定，并且有大量的特性和社区支持。
*   资源和材料很容易获得，所以很容易被采用。

## **缺点:**

*   源代码控制非常困难，在大多数情况下，故事板会导致冲突，而且它包含 xml 源代码，在这种情况下，很难解决合并冲突，因为大多数开发人员对 xml 代码没有控制权。
*   在创建应用程序时，你必须记住所有设备之间的兼容性和自动布局问题。
*   随着应用程序中屏幕数量的增加，很难在单个故事板中进行管理。
*   通过 UIKit 中的代码制作 UI 也是一个漫长的过程，它还需要编写大量的代码，这使得代码更加复杂，难以理解。

# **SwiftUI**

SwiftUI 在 iOS 13 版本中发布。

" *SwiftUI 借助 Swift 的强大功能，以尽可能少的代码，帮助您在所有苹果平台上构建美观的应用程序。借助 SwiftUI，您可以在任何苹果设备上，只使用一套工具和 API，为所有用户带来更好的体验。*

这是苹果的文档对 SwiftUI 的描述。

**我们来了解一下 Swift UI 中引入的一些高级功能。**

**高级应用体验和工具**

使用新功能增强您的应用，例如改进的列表视图、更好的搜索体验以及对控件焦点区域的支持。通过新的 Canvas API(drawRect 的现代 GPU 加速版)获得对低级绘图原语的更多控制。

**无障碍改善**

通过使用新的 Rotor API 在一个简单的列表中显示屏幕上最相关的项目来加速交互。当前的可访问性焦点状态，如 VoiceOver 光标，现在可以被读取，甚至以编程方式更改。借助新的可访问性表示 API，您的自定义控件可以轻松继承现有标准 SwiftUI 控件的全部可访问性支持。

**MAC OS 上 SwiftUI 的改进**

新的性能和 API 可用性改进，包括对多列表格的支持，使您的 macOS 应用程序更加出色。

**始终在线视网膜显示屏支持**

在 Apple Watch Series 5 和更高版本上，始终开启的 Retina 显示屏允许 watchOS 应用程序保持可见，即使手表表面变暗，也能让关键信息一目了然。

**iPadOS 的部件**

现在，小部件可以放在主屏幕上的任何地方，并增加到一个新的超大尺寸的小部件。

SwiftUI 已经证明了自己是 iOS 开发领域的绝对游戏规则改变者，因为 SwiftUI 提供了易于理解的语法，并且结构合理。

不仅如此，SwiftUI 是一个更强大的库。SwiftUI 只会继续发展成为一种强大的语言，因为苹果会继续将重心从 UIKit 转移开。

SwiftUI 采用了苹果从 UIKit 学到的一切，并为开发者提供了 UIKit 中没有的更好的功能。这些特性之一包括创建更强大的应用程序，同时使用比 UIKit 更少的代码。

## **swift ui 和 UIKit 的比较:**

以下是 SwiftUI 和 UIKit 的主要区别。

*   **陈述性 vs 命令性:**

SwiftUI 是一个声明性框架，但 UIKit 是一个命令性框架。

让我们通过下面的代码示例来看看命令式和声明式编程。

# **命令式(UIKit):**

```
class ViewController: UIViewController, UITableViewDataSource {
     private var tableView: UITableView!
      var listArray: [ListItem] = [
          ListItem(name: “List item — 1”),
          ListItem(name: “List item — 2”)
          ] override func viewDidLoad() {
          super.viewDidLoad()
          setupTableView()
   } private func setupTableView() {
       tableView = UITableView()
       tableView.dataSource = self
       tableView.translatesAutoresizingMaskIntoConstraints = false
       view.addSubview(tableView)
       let topAndBottomMargins = CGFloat(16)
       NSLayoutConstraint.activate([
       tableView.topAnchor.constraint(equalTo: view.bottomAnchor,    constant: topAndBottomMargins),
       tableView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
       tableView.leadingAnchor.constraint(equalTo: view.leadingAnchor)
       ])
   } func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
       return listArray.count
   }
 func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
      let cell = tableView.dequeueReusableCell(withIdentifier: “Cell”, for: indexPath) as! TableViewCell
      cell.textLabel?.textColor = .gray
      cell.textLabel?.text = listArray[indexPath.row].name
      return cell
   }
}
```

# 声明性示例(SwiftUI):

```
struct ContentView: View {
 var listArray: [ListItem] = [
     ListItem(name: “List item — 1”),
     ListItem(name: “List item — 2”)
    ]var body: some View {
     List(listArray) { person in
     Text(person.name)
        .font(.subheadline)
        .foregroundColor(.gray)
     }
   }
}
```

通过这个例子，我们可以清楚地看到 SwiftUI 的代码比 UIKit 少，使用 swift UI，数据可以自动与 UI 元素绑定，因此我们不需要跟踪用户界面的状态。作为程序员，我们需要做的就是用 SwiftUI 组件设置数据元素。SwiftUI 负责确保屏幕反映描述。

*   **性能:**

swiftUI 比 UIKit 快得多，因为 Swift UI 视图使用 ***struct*** 而不是 ***class*** 。它还提供了一种声明式编程的机制和一个完整的组合框架，可以更快地在屏幕上呈现用户界面的变化。

*   **界面构建技术:**

SwiftUI 没有像 Storyboard 和 xib 这样的界面构建器，swift 代码本身描述的是布局而不是 xml 文件。

SwiftUI 提供自动实时预览视图，使开发速度更快，而不是每次都在模拟器上检查应用程序。

swiftUI 中没有自动布局，不存在与内容布局相关的问题。它可以兼容所有苹果设备。在 swiftUI 中可以轻松处理黑暗模式。

修饰符有助于用户界面元素的定制。您可以通过几行代码轻松绘制不同的形状，并且还可以轻松实现视图上的动画。

*   **团队合作和代码维护:**

由于 swiftUI 代码少，没有故事板之类的界面构建器，所以合并代码和避免冲突非常容易。它还可以加速开发过程，减少开发时间和资源。

## **使用 Swift UI 的好处:**

*   简单易学，代码简单干净。
*   它可以使用 UIHostingController 与 UIKit 混合使用。
*   它允许你轻松地管理主题。开发人员可以轻松地将黑暗模式添加到他们的应用程序中，并将其设置为默认主题，用户也可以轻松地启用黑暗模式。
*   SwiftUI 用 BindableObject、ObjectBinding 和整个 Combine 框架为反应式编程爱好者提供了机制。这可以减少更多的代码和变量处理。
*   它提供实时预览。当您需要检查布局时，您不需要每次都运行应用程序。它使开发过程更快。
*   您不需要关心 swiftUI 中的@IBOutlet 连接。
*   SwiftUI 快速且可扩展。

## **swift ui 的缺点:**

*   SwiftUI 只能从 iOS 13 最新版本及以上版本使用。SwiftUI 通过与使用 Interface Builder 构建和运行的应用程序(如 UIKit、故事板等)无缝集成，缓解了这一问题。).您可以在基于故事板的用户界面中使用 SwiftUI 视图，反之亦然。在将应用程序缓慢过渡到 SwiftUI 时，这是一个非常有用的工具
*   Swift 是 2014 年推出的，所以栈溢出的数据不多。这意味着你在解决复杂的错误时得不到太多的帮助
*   它不允许您在 Xcode 预览中分析视图层次。

# **结论**

SwiftUI 有一种与 UIKit 完全不同的方法来编写代码。 **SwiftUI 清晰易读，使用方便**。毫无疑问，在不久的将来，开发者将开始使用 swiftUI 应用程序。

即使您仍然是 UIKit 的热情支持者，您也可以安全地将 SwiftUI 的功能添加到您的 UIKit 项目中。我希望这篇文章是有用的，并回答了最常见的 SwiftUI 和 UIKit 相关的问题。

**参考文献:**

https://developer.apple.com/xcode/swiftui/