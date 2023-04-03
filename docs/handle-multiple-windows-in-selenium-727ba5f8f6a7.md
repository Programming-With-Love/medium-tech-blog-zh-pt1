# 如何在 Selenium 中处理多个窗口？

> 原文：<https://medium.com/edureka/handle-multiple-windows-in-selenium-727ba5f8f6a7?source=collection_archive---------2----------------------->

![](img/7663c4d426e83327141ccc369618fc75.png)

Handling Multiple Windows in Selenium — Edureka

在这个技术飞速发展的世界里，人们必须总是想办法跟上时代的步伐。随着世界朝着软件开发的方向发展，测试在使过程无缺陷方面起着至关重要的作用。Selenium 就是这样一个工具，可以帮助发现并解决错误。这篇关于如何在 Selenium 中处理多个窗口的文章让您了解如何在测试应用程序时处理多个窗口。

因此，我们首先来看看本文将要涉及的主题:

*   什么是 Selenium Webdriver？
*   如何在 Selenium 中运行测试用例？

1.  先决条件

*   如何处理多个窗口？

1.  句法

*   演示

因此，在我们进一步了解如何在 Selenium 中同时处理多个窗口之前，我们先来看看 Selenium Webdriver 是什么，并了解它是如何工作的。

# 什么是 Selenium Webdriver？

我敢打赌，大多数软件测试人员一定都使用过这个让他们的生活变得更轻松的神奇工具，对于那些不知道什么是 Selenium 的人，我将帮助您迈出探索这个工具的第一步。

![](img/2edc7a38afc56c870f72178b5dfe0c58.png)

***Selenium*** 是一个自动化工具，其唯一目的是测试任何组织开发的 web 应用程序。现在，这个让它流行的工具到底有什么吸引人的地方呢？

*   Selenium 是一个开源的可移植框架，有助于 web 应用程序的自动化测试。
*   Selenium 中的测试脚本可以用您选择的任何编程语言编写，比如 Java、Python、C#等等。
*   Selenium 有自己的命令集，称为 Selenese，它保存一系列命令。
*   它可以在各种网络浏览器上运行，比如 Chrome、Safari、Firefox、IE 等等。
*   selenium web driver*直接与浏览器交互，而不是与服务器交互。*
*   *它既快速又高效。*
*   *Selenium 在进行*功能测试*和回归测试时非常灵活。*
*   *另一个有趣的因素是，Selenium 使用了*元素定位器* ，这有助于在网页上找到元素。它们是:类、名称、XPath、ID、链接文本、DOM、部分链接文本和 *CSS 选择器*。*
*   *它还使用像 Maven、JUnit 这样的插件来更快更有效地测试应用程序。*

*现在让我们看看如何使用 Selenium 测试应用程序。*

# *如何在 Selenium 中运行测试用例？*

*为了测试 Selenium 中的任何应用程序，我们遵循某些最终有助于执行所需任务的过程。*

*运行测试用例的基本前提是:*

## *运行 Selenium 测试用例的先决条件*

*   *我们需要的第一件事是选择正确的编程语言来编写测试脚本。由于 Java 是最简单和最容易理解的语言之一，我们将把 JRE 库添加到项目中。*
*   *我们需要一个 IDE 来运行测试脚本。有 NetBeans、Eclipse 和许多其他 IDE，但我们更喜欢使用 Eclipse IDE，因为它在执行 Java 项目时非常有效。*
*   *一些 Selenium 插件，如 Selenium 独立服务器、Selenium jar 文件和 Selenium IDE。*
*   *不同浏览器的浏览器驱动程序，如 Chrome、IE、Firefox 等。*

*因此，为了测试一个应用程序，我们需要了解编程语言的基础，在我们的例子中是 Java。所以首先，*

*   *初始化 web 驱动程序并创建一个相同的对象。*
*   *将浏览器驱动实例化为新的 ChromeDriver(在这种情况下，我们正在开发 ChromeDriver，您可以选择 Firefox 或 IE ),并指定 ChromeDriver 所在的路径，后跟可执行文件的扩展名。*
*   *获取您想要测试的特定网页的 URL*
*   *使用 Selenium 中的一个元素定位器来查找元素*

*因此，一旦我们知道如何在 Selenium 中运行测试用例，我们将尝试通过同时跨多个窗口工作来改变这个过程。为了做到这一点，我们将看到一些帮助我们做到这一点的函数。*

# *如何处理多个窗口？*

*现在，什么是窗口句柄函数？如何在 Selenium 中测试应用程序时处理多个窗口？嗯，这个可以回答你所有的问题。*

## *什么是窗口句柄？*

*窗口句柄是保存所有窗口地址的唯一标识符。这基本上是一个指向窗口的指针，它返回字符串值。这个窗口句柄函数有助于获得所有窗口的句柄。保证每个浏览器都有一个唯一的窗口句柄。*

****语法****

*   *get.windowhandle():帮助获取当前窗口的句柄*
*   *get.windowhandles():帮助打开所有窗口的句柄*
*   *set:帮助设置一个字符串形式的窗口句柄。set<string>set = driver . get . window handles()</string>*
*   *切换到:帮助在窗口之间切换*
*   *操作:帮助在窗口上执行某些操作。*

*这些是我们将在本演示中看到的一些新功能。除此之外，其余的功能有助于流程的自动化。*

# *演示*

*在本演示部分，我们将自动化几个网站，并检查如何处理多个窗口。*

*我们将自动化页面，例如，*

*   *用于测试窗口句柄功能的 ToolsQA 网站*
*   *我们也将自动化我们的官方网站 edureka.co 和执行一些行动*
*   *Naukri.com 是最受欢迎的在线求职门户网站之一*

*现在，我们首先从测试网页工具开始*

*   *我们将首先指定我们将要工作的浏览器驱动程序，并使用以下命令指定它所在的路径:system . set property(" web driver . chrome . driver "，" D:\ \ \ \ chrome driver . exe ")；*
*   *将 web 驱动程序实例化为新的 chrome 驱动程序。*
*   *获取我们想要测试的网页的 URL。*
*   *检查元素并使用元素定位器在网页上找到它，在这种情况下，它是 ID。*
*   *在这之后，我们需要打开多个子窗口。所以，我要用 for 循环来做这件事。*

```
*package selenium;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class demo1 {
public static void main(String[] args) throws InterruptedException
{
System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver.exe");
WebDriver driver = new ChromeDriver();
driver.get("[http://toolsqa.com/automation-practice-switch-windows/](http://toolsqa.com/automation-practice-switch-windows/)");
WebElement clickElement = driver.findElement(By.id("button1"));

for(int i = 0; i < 3; i++)
{
clickElement.click();
Thread.sleep(3000);
}

}
}*
```

*下一步，我们将添加更多的命令，并注意执行中的变化。*

*   *除了我们需要打印父窗口和子窗口的窗口句柄之外，这几乎与第一个项目类似。*
*   *使用命令获取父窗口的句柄:String parentWindowHandle = driver . getwindowhandle()；*
*   *打印父窗口的窗口句柄。*
*   *使用作为元素定位器的 ID 在网页上查找元素。*
*   *打开多个子窗口。*
*   *遍历子窗口。*
*   *使用 sleep 命令 Thread.sleep(3000)暂停执行几秒钟，其中时间以纳秒为单位。*
*   *使用命令获取当前打开的所有窗口的句柄:Set<string>allWindowHandles = driver . getwindowhandles()；它返回一组句柄。</string>*
*   *打印所有窗口的句柄。*

```
*package selenium;

import java.util.Set;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class demo2 {
public static void main(String[] args) throws InterruptedException
{

System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver.exe");
WebDriver driver = new ChromeDriver();
driver.get("[http://toolsqa.com/automation-practice-switch-windows/](http://toolsqa.com/automation-practice-switch-windows/)");
String parentWindowHandle = driver.getWindowHandle();
System.out.println("Parent window's handle -> " + parentWindowHandle);
WebElement clickElement = driver.findElement(By.id("button1"));

for(int i = 0; i < 3; i++)
{
clickElement.click();
Thread.sleep(3000);
}

Set<String> allWindowHandles = driver.getWindowHandles();

for(String handle : allWindowHandles)
{
System.out.println("Window handle - > " + handle);
}

}

}*
```

*现在，我们将通过向上面的程序添加几个命令来定制 web 页面。*

*   *在这个程序中，我们将测试同一个 web 页面工具 QA，并将另一个 URL 传递给父窗口。*
*   *将浏览器驱动实例化为新的 Chromedriver。*
*   *获取父窗口的窗口句柄并打印出来。*
*   *使用作为元素定位器的 ID 在页面上查找元素。*
*   *使用 for 循环迭代正在创建的子窗口的数量。*
*   *打开所有窗户的把手。*
*   *打印第一个窗口的窗口句柄。*
*   *使用 *SwitchTo* 命令切换到所需窗口，并将网页“google.com”的 URL 传递到当前窗口。*

```
*package selenium;

import java.util.Set;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class demo4 {

public static void main(String[] args) throws InterruptedException
{
System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver.exe");
WebDriver driver = new ChromeDriver();
driver.get("[http://toolsqa.com/automation-practice-switch-windows/](http://toolsqa.com/automation-practice-switch-windows/)");
String parentWindowHandle = driver.getWindowHandle();
System.out.println("Parent window's handle -> " + parentWindowHandle);
WebElement clickElement = driver.findElement(By.id("button1"));

for(int i = 0; i < 3; i++)
{
clickElement.click();
Thread.sleep(3000);
}

Set<String> allWindowHandles = driver.getWindowHandles();

for(String handle : allWindowHandles)
{
System.out.println("Switching to window - > " + handle);
System.out.println("Navigating to google.com");
driver.switchTo().window(handle); //Switch to the desired window first and then execute commands using driver
driver.get("[http://google.com](http://google.com)");
}

}
}*
```

*在这之后，我们将看到如何在不改变父窗口的情况下改变子窗口。*

*   *这个过程几乎类似于前一个程序，但只是在通过网址，google.com，我们将切换到父窗口，并关闭它。*
*   *在此之后，我们将定义最后一个窗口的窗口句柄并切换到该窗口，并传递 ToolsQA 页面的 URL。这样这个 URL 只在最后一个窗口打开，其他两个子窗口仍然显示 google.com 页面。*

```
*package selenium;

import java.util.Set;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class demo5 {
public static void main(String[] args) throws InterruptedException
{

System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver.exe");
WebDriver driver = new ChromeDriver();
driver.get("[http://toolsqa.com/automation-practice-switch-windows/](http://toolsqa.com/automation-practice-switch-windows/)");
String parentWindowHandle = driver.getWindowHandle();
System.out.println("Parent window's handle -> " + parentWindowHandle);
WebElement clickElement = driver.findElement(By.id("button1"));

for(int i = 0; i < 3; i++)
{
clickElement.click();
Thread.sleep(3000);
}

Set<String> allWindowHandles = driver.getWindowHandles();
String lastWindowHandle = "";
for(String handle : allWindowHandles)
{
System.out.println("Switching to window - > " + handle);
System.out.println("Navigating to google.com");
driver.switchTo().window(handle); //Switch to the desired window first and then execute commands using driver
driver.get("[http://google.com](http://google.com)");
lastWindowHandle = handle;
}

//Switch to the parent window
driver.switchTo().window(parentWindowHandle);
//close the parent window
driver.close();
//at this point there is no focused window, we have to explicitly switch back to some window.
driver.switchTo().window(lastWindowHandle);
driver.get("[http://toolsqa.com](http://toolsqa.com)");
}
}*
```

*现在，我们将测试其中一个顶级求职门户网站 Naukri.com*

*   *将系统属性设置为 Chromedriver 并指定其路径*
*   *将 webdriver 实例化为新的 chromedriver*
*   *获取网页的 URL 并最大化页面*
*   *获取父窗口的窗口句柄*
*   *获取所有窗口的窗口句柄*
*   *接下来，我们声明了一个迭代器类型的对象，用它来切换到子窗口并在其上执行操作*
*   *我们检查主窗口是否等于子窗口，如果(！mainweb.equals(child))。如果主窗口不等于子窗口，条件成立，我们切换到下一个子窗口。*

```
*package selenium;
import org.testng.annotations.Test;
import java.util.Iterator;
import java.util.Set;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
public class MultipleWindowsClass{
[@Test](http://twitter.com/Test)
public void testMultipleWindows() throws InterruptedException{
System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver.exe");
// To open browser
WebDriver driver = new ChromeDriver();
// To maximize browser
driver.manage().window().maximize();
// To open Naukri website with multiple windows
driver.get("[http://www.naukri.com/](http://www.naukri.com/)");
// It will return the parent window name as a String
String mainWindow=driver.getWindowHandle();
// It returns no. of windows opened by WebDriver and will return Set of Strings
Set<String> set =driver.getWindowHandles();
// Using Iterator to iterate with in windows
Iterator<String> itr= set.iterator();
while(itr.hasNext()){
String childWindow=itr.next();
// Compare whether the main windows is not equal to child window. If not equal, we will close.
if(!mainWindow.equals(childWindow)){
driver.switchTo().window(childWindow);
System.out.println(driver.switchTo().window(childWindow).getTitle());
driver.close();
}
}
// This is to switch to the main window
driver.switchTo().window(mainWindow);
}
}*
```

*接下来，我们将在我们的官方网站 edureka.co 上执行一些操作*

*   *我们将把 webdriver 初始化为新的 chromedriver。*
*   *获取网页的 URL。*
*   *我们将使用 **JavaScriptExecutor** ，它是一个接口，提供了通过 Selenium WebDriver 执行 Javascript 的机制。*
*   *获取父窗口的窗口句柄。*
*   *使用 XPath 查找元素，XPath 是一个元素定位器，它将键发送到 web 页面上的特定位置。*
*   *使用 javascriptexecutor 命令向下滚动页面:js . execute script(" window . scroll by(X 轴，Y 轴)")；*
*   *获取所有窗口的句柄并打印出来。*
*   *接下来，我们声明了一个 iterator 类型的对象，用它来切换到子窗口并在其上执行操作。*
*   *如果(！idspninfopath _ NV)，我们将检查该条件。mainweb.equals(child))，如果这个条件成立，我们切换到子窗口并打印它的标题。*

```
*package selenium;

import java.util.Iterator;
import java.util.Set;

import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.Keys;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class demo3 {

public static void main(String[] args) throws InterruptedException
{
System.setProperty("webdriver.chrome.driver","S:\\\\\\\\chromedriver.exe");
WebDriver driver = new ChromeDriver();
JavascriptExecutor js = (JavascriptExecutor) driver;
driver.get("[https://www.edureka.co/community](https://www.edureka.co/community)");
String mainweb = driver.getWindowHandle();
driver.findElement(By.xpath("//a[[@class](http://twitter.com/class)='qa-logo-link edureka']")).sendKeys(Keys.SHIFT,Keys.ENTER);
Thread.sleep(100);
js.executeScript("window.scrollBy(0,400)");
Set <String> set = driver.getWindowHandles();
System.out.println(set);
Iterator <String> itr = set.iterator();
while(itr.hasNext())
{
js.executeScript("window.scrollBy(0,400)");
String child = itr.next();
if(!mainweb.equals(child))
{
driver.switchTo().window(child);
System.out.println(driver.switchTo().window(child).getTitle());
// driver.close();
}
}
driver.switchTo().window(mainweb);

}

}*
```

*接下来，我们将通过自定义来自动化相同的网页。*

*   *这个过程与前一个几乎相似，但是在这个过程中，我们打印当前页面的标题。*
*   *使用 javascriptexecutor 向下滚动页面。*
*   *使用元素定位器 XPath 查找元素，并将键(字符串形式)发送到特定的元素位置。*
*   *声明 web 元素*链接*来点击页面上的特定链接，在本例中，我们希望链接在新窗口中打开。*
*   *在此之后暂停执行几秒钟。*
*   *获取所有窗口的窗口句柄并按顺序打印。*
*   *切换到父窗口，检查标题是否匹配。如果是，使用 javascriptexecutor 向下滚动页面。使用元素定位器在网页上找到另一个元素，并指定新窗口的位置。*
*   *切换回父窗口并向下滚动页面。*

```
*package selenium; 
import java.util.Set; 
import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor; 
import org.openqa.selenium.Keys; 
import org.openqa.selenium.Point; 
import org.openqa.selenium.WebDriver; 
import org.openqa.selenium.WebElement; 
import org.openqa.selenium.chrome.ChromeDriver; 
import org.openqa.selenium.interactions.Actions; 
//import org.openqa.selenium.support.ui.Select; 
public class selenium { public static void main(String[] args) throws Exception 
{ 
System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver.exe"); 
WebDriver driver = new ChromeDriver(); 
driver.get("[https://www.edureka.co/](https://www.edureka.co/)"); 
String title = driver.getTitle(); System.out.println(title); 
//driver.get("[http://www.google.co](http://www.google.co)"); 
JavascriptExecutor js = (JavascriptExecutor) driver; 
driver.findElement(By.cssSelector("#search-inp")).sendKeys("Selenium Certification Course"); 
js.executeScript("window.scrollBy(0,40)"); 
driver.findElement(By.xpath("//span[[@class](http://twitter.com/class)='typeahead__button']")).click(); 
WebElement link = driver.findElement(By.xpath("//li[[@class](http://twitter.com/class)='ga-allcourses']//a[[@class](http://twitter.com/class)='giTrackElementHeader'][contains(text(),'Courses')]")); 
Actions newwin = new Actions(driver); 
newwin.keyDown(Keys.SHIFT).click(link).keyUp(Keys.SHIFT).build().perform(); 
//Thread.sleep(2000); 
//js.executeScript("window.scrollBy(0,400)"); 
Thread.sleep(3000); 
Set<String> windows = driver.getWindowHandles(); 
System.out.println(windows); 
System.out.println("a1"); 
for (String window : windows) 
{ 
driver.switchTo().window(window); 
if (driver.getTitle().contains("Best Training & Certification Courses for Professionals | Edureka")) 
{ 
System.out.println("a2"); 
js.executeScript("window.scrollBy(0,1000)"); 
System.out.println("b1"); 
driver.findElement(By.xpath("//*[[@id](http://twitter.com/id)=\"allc_catlist\"]/li[3]/a")).click(); 
driver.manage().window().setPosition(new Point(-2000, 0)); 
} 
} 
Thread.sleep(3000); 
Set<String> windows1 = driver.getWindowHandles(); 
System.out.println(windows1); 
System.out.println("a3"); 
for (String window : windows1) 
{ 
driver.switchTo().window(window); 
System.out.println("a4"); 
js.executeScript("window.scrollBy(0,400)"); 
} 
} 
}*
```

*现在让我们检查最后一个程序的输出。*

*首先，我们将初始化浏览器，获取我们想要测试的 web 页面的 URL，并在页面上找到搜索框元素，向搜索框发送关键字，然后单击搜索图标。*

*![](img/d84820a90ea0a6bd25c298db11a5a147.png)*

*之后，我们将使用 action 命令在新窗口中打开课程链接。*

*![](img/6b4461a0c63ea5f5778e8cbe385dad6d.png)*

*完成后，我们将使用 javascriptexecutor 向下滚动子窗口。*

*![](img/3a9abc9acf5c9e31a379110ff79c56a7.png)*

*然后，打印第一个窗口的标题和两个窗口的句柄。*

*![](img/04e084fe2ec6d362301e5ac390a8286b.png)*

*如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=handle-multiple-windows-in-selenium)*

*请留意本系列中的其他文章，它们将解释硒的各个方面。*

> *1.[硒教程](/edureka/selenium-tutorial-77879a1d9af1)*
> 
> *2.[Selenium web driver:TestNG For Test Case Management&报告生成](/edureka/selenium-webdriver-tutorial-e3e6219f21ad)*
> 
> *3.[构建数据驱动、关键字驱动的&混合 Selenium 框架](/edureka/selenium-framework-data-keyword-hybrid-frameworks-ea8d4f4ce99f)*
> 
> *4.[硒里的定位器](/edureka/locators-in-selenium-f6e6b282aed8)*
> 
> *5. [XPath 教程](/edureka/xpath-in-selenium-cd659373e01a)*
> 
> *6.[硒中等待](/edureka/waits-in-selenium-5b57b56f5e5a)*
> 
> *7.[为分布式硒测试设置硒网格](/edureka/selenium-grid-tutorial-ef342799c484)*
> 
> *8.[硒使用 Python](/edureka/selenium-using-python-edc22a44f819)*
> 
> *9.[使用 LambdaTest 进行跨浏览器测试](/edureka/cross-browser-testing-9299b04ce277)*
> 
> *10.[使用 Selenium 进行跨浏览器测试](/edureka/cross-browser-testing-using-selenium-90b1911c6d60)*
> 
> *11.[Selenium 中的页面对象模型](/edureka/page-object-model-in-selenium-bc4d7c8c4203)*
> 
> *12.[硒项目](/edureka/selenium-projects-b2df15d35fe2)*
> 
> *13. [QTP vs 硒](/edureka/qtp-vs-selenium-338f3d3bbfa7)*
> 
> *14.[硒 vs RPA](/edureka/selenium-vs-rpa-84159dbcd0f2)*
> 
> *15. [Selenium WebDriver 架构](/edureka/selenium-webdriver-architecture-565e2db26dd5)*
> 
> *16.[处理 Selenium 中的异常](/edureka/exceptions-in-selenium-369c38155e7d)*
> 
> *17.[使用黄瓜&硒](/edureka/cucumber-selenium-tutorial-aefec05f4733)进行网站测试*

**原载于 2019 年 4 月 16 日*[*https://www.edureka.co*](https://www.edureka.co/blog/handle-multiple-windows-in-selenium/)*。**