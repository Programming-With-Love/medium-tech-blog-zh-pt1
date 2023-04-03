# 了解如何使用 Selenium 执行跨浏览器测试

> 原文：<https://medium.com/edureka/cross-browser-testing-using-selenium-90b1911c6d60?source=collection_archive---------1----------------------->

![](img/e73e7483e0f66b9aa9c79452e8e37dde.png)

Cross Browser Testing using Selenium — Edureka

随着自动化测试需求的增加，Selenium 就是这样一个工具，它非常适合网站的跨浏览器测试。检查网站在不同浏览器和操作系统上的兼容性和性能是非常必要的。因此，这篇关于使用 Selenium 进行跨浏览器测试的文章将帮助您深入理解这些概念。

以下是本文涵盖的主题:

*   什么是跨浏览器测试？
*   为什么需要跨浏览器测试？
*   如何进行跨浏览器测试？
*   使用 Selenium 演示

# 什么是跨浏览器测试？

跨浏览器测试就是在多种浏览器中测试应用程序，比如 IE、Chrome、Firefox，这样我们就可以有效地测试我们的应用程序。跨浏览器兼容性是指网站或 web 应用程序跨不同浏览器和操作系统运行的能力。

![](img/f2833d7d372ec05486e8658cba48f7fa.png)

**例如**——假设您有 20 个测试用例要手动执行。你可以在一两天内完成这项任务。但是，如果相同的测试用例必须在五种浏览器中执行，那么你可能需要一周的时间来完成。然而，如果您自动化这 20 个测试用例并运行它们，那么根据测试用例的复杂性，它不会花费超过一两个小时。这就是跨浏览器测试的用武之地。

现在，让我们进一步了解为什么需要在 Selenium 中进行跨浏览器测试。

# **你为什么需要跨浏览器测试？**

每个网站都由三种主要技术组成，即 HTML5、CSS3 和 JavaScript。然而，在后端有很多技术可以使用，比如 Python，Ruby 等等。但是，在前端和渲染中，只使用了这三种技术。

此外，每个浏览器使用完全不同的渲染引擎来计算这三项技术。例如，Chrome 使用 Blink，Firefox 使用 Gecko，IE 使用 edge HTML 和 Chakra，因此，相同的网站在所有这些不同的浏览器中会呈现完全不同的效果。这也正是你需要跨浏览器测试的原因。这意味着该网站在所有不同的浏览器版本和不同的操作系统中都应该运行良好。因此，为了确保它正常工作，需要进行跨浏览器测试。

除此之外，我还列出了一些描述跨浏览器测试需求的理由。

*   浏览器与不同操作系统的兼容性。
*   图像方向。
*   每个浏览器都有不同的 Javascript 方向，这有时会导致问题。
*   字体大小不匹配或未正确呈现。
*   与新 web 框架的兼容性。

现在让我们进一步了解如何执行跨浏览器测试。

# 如何进行跨浏览器测试？

跨浏览器测试基本上是在不同的浏览器上多次运行同一组测试用例。这种类型的重复任务最适合自动化。因此，通过使用工具来执行这个测试更节省成本和时间。现在让我们看看如何使用 selenium web driver 来执行它。

如果我们使用 Selenium WebDriver，我们可以使用 Internet Explorer、FireFox、Chrome、Safari 浏览器来自动化测试用例。

**步骤 2:** 为了在同一台机器上同时执行不同浏览器的测试用例，我们可以将 [TestNG 框架与 Selenium WebDriver 集成。](https://www.edureka.co/blog/selenium-webdriver-tutorial?utm_source=medium&utm_medium=content-link&utm_campaign=cross-browser-testing-using-selenium)

最后，你可以编写测试用例并执行代码。

现在，让我们看看如何在三种不同的浏览器上执行 Edureka 网站的跨浏览器测试

# 使用 Selenium WebDriver 的演示

```
package co.edureka.pages;

import java.util.concurrent.TimeUnit;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Parameters;
import org.testng.annotations.Test;

public class CrossBrowserScript {

WebDriver driver;

/**
* This function will execute before each Test tag in testng.xml
* [@param](http://twitter.com/param) browser
* [@throws](http://twitter.com/throws) Exception
*/
[@BeforeTest](http://twitter.com/BeforeTest)
[@Parameters](http://twitter.com/Parameters)("browser")
public void setup(String browser) throws Exception{
//Check if parameter passed from TestNG is 'firefox'
if(browser.equalsIgnoreCase("firefox")){
//create firefox instance
System.setProperty("webdriver.gecko.driver", "C:\\geckodriver-v0.23.0-win64\\geckodriver.exe");
driver = new FirefoxDriver();
}

//Check if parameter passed as 'chrome'
else if(browser.equalsIgnoreCase("chrome")){
//set path to chromedriver.exe
System.setProperty("webdriver.chrome.driver", "C:\\Selenium-java-edureka\\New folder\\chromedriver.exe");
driver = new ChromeDriver();

}
else if(browser.equalsIgnoreCase("Edge")){
//set path to Edge.exe
System.setProperty("webdriver.edge.driver","C:\\Selenium-java-edureka\\MicrosoftWebDriver.exe");;span style="font-family: verdana, geneva, sans-serif; font-size: 14px;"&amp;gt;//create Edge instance&amp;lt;/span&amp;gt;
driver = new EdgeDriver();
}
else{
//If no browser passed throw exception
throw new Exception("Browser is not correct");
}
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
}

[@Test](http://twitter.com/Test)
public void testParameterWithXML() throws InterruptedException{
driver.get("[https://www.edureka.co/](https://www.edureka.co/)");
WebElement Login = driver.findElement(By.linkText("Log In"));
//Hit login button
Login.click();
Thread.sleep(4000);
WebElement userName = driver.findElement(By.id("si_popup_email"));
//Fill user name
userName.sendKeys("your email id");
Thread.sleep(4000);
//Find password'WebElement password = driver.findElement(By.id("si_popup_passwd"));
//Fill password
password.sendKeys("your password");
Thread.sleep(6000);

WebElement Next = driver.findElement(By.xpath("//button[[@class](http://twitter.com/class)='clik_btn_log btn-block']"));
//Hit search button
Next.click();
Thread.sleep(4000);
WebElement search = driver.findElement(By.cssSelector("#search-inp"));
//Fill search box
search.sendKeys("Selenium");
Thread.sleep(4000);
//Hit search button

WebElement searchbtn = driver.findElement(By.xpath("//span[[@class](http://twitter.com/class)='typeahead__button']"));
searchbtn.click();
}
}
```

在上面的代码中，我在 [Edureka](https://www.edureka.co/) 网站上执行操作，如登录网站和搜索 Selenium 课程。但是，我想在三种不同的浏览器上检查跨浏览器兼容性，即谷歌 Chrome、Mozilla Firefox 和微软 Edge。这就是为什么我在代码中设置了所有 3 个浏览器的系统属性。之后，我使用定位器在网站上执行动作。这就是我的班级档案。现在，为了执行程序，您需要一个 TestNG XML 文件，它包含上述类文件的依赖项。下面的代码描述了 TestNG 文件。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "[http://testng.org/testng-1.0.dtd](http://testng.org/testng-1.0.dtd)">
<suite name="TestSuite" thread-count="2" parallel="tests" >
<test name="ChromeTest">
<parameter name="browser" value="Chrome"/>
<classes>
<class name="co.edureka.pages.CrossBrowserScript">
</class>
</classes>
</test>
<test name="FirefoxTest">
<parameter name="browser" value="Firefox" />
<classes>
<class name="co.edureka.pages.CrossBrowserScript">
</class>
</classes>
</test>
<test name="EdgeTest">
<parameter name="browser" value="Edge" />
<classes>
<class name="co.edureka.pages.CrossBrowserScript">
</class>
</classes>
</test>
</suite>
```

在上面的 XML 文件中，我为驱动器指定了不同的类，这样它将帮助我们实例化浏览器来执行网站上的测试用例。事情就是这样的。

至此，我们结束了这篇关于使用 Selenium Webdriver 进行跨浏览器测试的文章。我希望你理解了这些概念，并且它增加了你知识的价值。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=cross-browser-testing-using-selenium)

请留意本系列中的其他文章，它们将解释硒的各个方面。

> 1.[硒教程](/edureka/selenium-tutorial-77879a1d9af1)
> 
> 2.[Selenium web driver:TestNG For Test Case Management&报告生成](/edureka/selenium-webdriver-tutorial-e3e6219f21ad)
> 
> 3.[构建数据驱动、关键字驱动的&混合 Selenium 框架](/edureka/selenium-framework-data-keyword-hybrid-frameworks-ea8d4f4ce99f)
> 
> 4.[硒里的定位器](/edureka/locators-in-selenium-f6e6b282aed8)
> 
> 5. [XPath 教程](/edureka/xpath-in-selenium-cd659373e01a)
> 
> 6.[等待硒](/edureka/waits-in-selenium-5b57b56f5e5a)
> 
> 7.[为分布式硒测试设置硒网格](/edureka/selenium-grid-tutorial-ef342799c484)
> 
> 8.[硒使用 Python](/edureka/selenium-using-python-edc22a44f819)
> 
> 9.[跨浏览器测试使用 LambdaTest](/edureka/cross-browser-testing-9299b04ce277)
> 
> 10.[在 Selenium 中处理多个窗口](/edureka/handle-multiple-windows-in-selenium-727ba5f8f6a7)
> 
> 11.[Selenium 中的页面对象模型](/edureka/page-object-model-in-selenium-bc4d7c8c4203)
> 
> 12.[硒项目](/edureka/selenium-projects-b2df15d35fe2)
> 
> 13. [QTP vs 硒](/edureka/qtp-vs-selenium-338f3d3bbfa7)
> 
> 14.[硒 vs RPA](/edureka/selenium-vs-rpa-84159dbcd0f2)
> 
> 15. [Selenium WebDriver 架构](/edureka/selenium-webdriver-architecture-565e2db26dd5)
> 
> 16.[处理 Selenium 中的异常](/edureka/exceptions-in-selenium-369c38155e7d)
> 
> 17.[使用黄瓜&硒](/edureka/cucumber-selenium-tutorial-aefec05f4733)进行网站测试

*原载于 2019 年 4 月 30 日 https://www.edureka.co*[](https://www.edureka.co/blog/cross-browser-testing-using-selenium/)**。**