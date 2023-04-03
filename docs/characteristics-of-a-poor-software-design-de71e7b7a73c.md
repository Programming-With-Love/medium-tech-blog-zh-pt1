# 糟糕的软件设计的特征

> 原文：<https://medium.com/globant/characteristics-of-a-poor-software-design-de71e7b7a73c?source=collection_archive---------1----------------------->

## 成为一名神盾局程序员:(第 1 部分)

![](img/8a4512ba53c01368b9865c35ae0b6e57.png)

Image*Courtesy*: [https://www.joetheitguy.com/tag/user-error/](https://www.joetheitguy.com/tag/user-error/)

```
In this 2 part series, I talk about SOLID principle, definition and its usage to avoid bad programming design.
```

阅读博客的标题，你一定会想，当有大量的材料谈论好的软件设计实践时，这个世界上谁会愿意谈论坏的设计呢？我听我的朋友们谈论了很多软件设计的最佳实践。但是你不知道糟糕的设计是什么样子，怎么知道什么是好的；)

在我作为软件开发人员的职业生涯中，我遇到过一些设计糟糕的模块。在处理这些模块、修复错误和尝试添加新功能时，我意识到了这些设计原则的重要性。我更重视它们，因为遵循它们帮助我解决了开发过程中面临的挑战。

就像我说的，当你意识到设计很差时，最常见的情况是维护别人已经写好的代码。

我在这种情况下遇到的常见挑战有

*   在开发一个功能的过程中破坏另一个功能。
*   现有代码不支持未来的增强。
*   理解和维护代码的复杂性。
*   难以孤立地测试功能
*   难以添加新功能

听起来很熟悉？如果是的话，有没有想过“如何避免它们？”。嗯，了解这些的根本原因很重要。

避免错误的最简单的方法是理解和识别坏的编程习惯，并采用好的编程习惯来更好地开发应用程序。

根据公认的开发软件设计原则的 Robert C. Martin 所说，一个糟糕的设计有 4 个重要的特征。这些是:

*   ****不动****
*   ****脆弱性****
*   ****粘度****

*![R](img/014bdf87541820768dedb41ed7caca7d.png)R**igidity:*如果一个类中的每一个小变化都转化为其他依赖类中的级联变化，那么代码中就存在耦合。这使得代码僵化。让我们通过下面的例子来理解这一点，在这个例子中，类名被显式地使用，而不是抽象。****

```
*public class ReportClient {public void reportType(String reportType, Map filterParams) {
      // for saving few lines let us assume reportType is already 
      set to one of "CSV","PDF", "XML"        
      ReportGenerator rg = new ReportGenerator();
      if (reportType == "CSV") {
      rg.downloadCSVReport(filterParams);
      } else if (reportType == "PDF") {
      rg.downloadPDFReport(filterParams);
      } else if (reportType == "XML") {
      rg.downloadXMLReport(filterParams);
      }
   }
}public class ReportGenerator {
     // constructor
     public ReportGenerator() {
     }
     // common method to fetch data from database
     protected ReportData extractData(Map filterParams) {
     ReportData reportData;
     // logic to fetch data and return it to calling method
     return reportData;
     } public void downloadCSVReport(Map filterParams) {
     // Step 1\. extract data from DB
     // write data in CSV format
     // download the report with content type as MS Excel sheet
     } public void downloadPDFReport(Map filterParams) {
     // Step 1\. extract data from DB
     // write data in PDF format
     // download the report with content type as PDF
     } public void downloadXMLReport(Map filterParams) {
     // Step 1\. extract data from DB
     // write data in XML format
     // download the report with content type as XML
     }
 }*
```

*上面的例子是一个以各种格式导出数据的工具，比如银行对账单。例如，pdf、csv 和 xml。extractData()方法常用于导出所有格式的报告。*

*一切进展顺利，直到客户要求在报告的 PDF 格式中添加一些特定字段。现在，因为 extract 方法对于所有格式都是通用的，所以所有格式的查询都会发生变化，因此 ReportData DTO 也会发生变化。现在，作为一名开发人员，如果我编写一个 if 语句并为 PDF 报告创建一个单独的提取逻辑，会出现问题吗？但是这并不容易改变，因为 ReportGenerator 类没有关于 reportType 的信息。嗯，我可以将 reportType 传递给 ReportGenerator 类的构造函数，修复这个问题。但是这会导致两个类中都存在多余的 if 语句。*

*嗯，太复杂了！！很难仅仅为了在报表中添加几个字段而修改代码。这叫做设计的**刚性**。具有紧密耦合的系统可能会导致这种僵化，并使得在产品中实现增强和添加新功能变得困难。*

***设计刚性的弊端** :
1。很难给出变化
2 的近似值。会导致测试比预期更多的组件
3。会让经理们害怕允许微小的改变，并因此而忍受应用程序中的缺陷*

*![I](img/42a639b2bf45105277a7a1bcdb31cd15.png)I**m 移动性** : 在上面的例子中，我们有一些方法可以提取数据并以特定的格式导出，比如 CSV 格式。不管内容和列是什么，编写 CSV 的逻辑几乎是相同的。因此，假设产品中的其他模块想要重用以 CSV 格式写入数据的逻辑。但是，由于我们这样做的代码与这个特定的报告生成代码紧密相关，因此它不能在其他模块中重用。此外，提取该逻辑并将其提供给其他模块也需要大量的工作。(请记住，不要将可重用性与在项目中制作相同代码的多个副本相混淆。)*

*当组件紧密耦合或高度依赖彼此，并且不能在另一个地方重用时，代码被称为不可移动的。在上面的例子中，虽然生成 CSV 的实现是可用的，但它既不能作为实用方法访问，也不能作为可分发的实现访问。*

***设计不灵活的弊端** :
1。难以重用最常见的功能。*

*2.如果需要重用该模块，那么将其从原始设计中分离出来的努力和风险将会很高*

*3.导致在项目的多个地方创建相同代码的副本，这也意味着原始代码中的错误被复制。在原始代码中修复这个 bug 并不会在其他地方自动修复。需要单独努力来解决这些问题*

*![F](img/a01b33e9057ab7863124b59bd639192d.png)  F   **脆弱性** : ** *当代码中的任何新变化破坏了系统中意想不到的部分时，设计就是脆弱的。这种软件很难维护，因为一个补丁引入的问题比解决的问题还多，从而使情况变得更糟。****

*脆弱的代码会以你无法预测的怪异方式崩溃。脆弱性可以通过构建高度模块化、高度内聚和松散耦合的软件来避免*。**

***设计脆弱的缺点** :
1。一些模块会持续出现在错误列表中。更多的时间被花费在修正错误上，而不是修正实际的问题。
3。程序员不愿意对代码进行修改，因为由于他们在修复错误时遇到的问题，他们对这样一段代码的信心非常低。*

*![](img/7d4275838a1c5972ad5813f0b6db4a83.png)*

*![V](img/0271374a29826bd1ace6379fadd570dd.png)  V   **粘滞度**:在实施任何变更时，可以有两种方式来实施修复。一个是保留设计，另一个是破坏设计的捷径。 ***粘性指的是开发者可以将保留设计的代码添加到系统中的难易程度。*** 如果添加 hack 比设计保留代码更容易，那么系统具有高粘性。*

*随着时间的推移，越粘的代码变得不可维护，因为它充满了漏洞(使用漏洞比采用正确的设计方法更容易修复)。因此，需要重新设计代码。但是重新设计和重构这种代码在时间和金钱上是昂贵的，而不是用正确的设计编写一个新工具。*

***粘度在设计上的缺点** :
1。开发环境缓慢低效
2。导致编译时间长，测试反馈时间长，集成困难*

*如果设计有以上提到的一个或多个特征，一个糟糕的软件设计可以被识别出来。*

*如果我们知道一个糟糕的设计是什么样子，如果我们知道避免这些特征的指导原则，那么想出一个好的软件设计并不困难:)。*

*这些指导方针通常被称为坚实的原则。是的固体！S.O.L.I.D .是软件设计原则，它使程序员能够做出重要的决定，并提供有效处理设计过程复杂性的方法，同时保持事情简单。要详细了解神盾局的原则，请阅读我的下一篇文章！*