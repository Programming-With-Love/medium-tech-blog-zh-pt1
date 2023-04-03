# 了解 WebDriver 事件

> 原文：<https://medium.com/quick-code/understanding-webdriver-events-e58124575edd?source=collection_archive---------1----------------------->

*在本文中，了解 Unmesh Gundecha 的 WebDriver 事件，他是一位敏捷、开源的 DevOps 传播者，在各种工具和技术方面拥有丰富的经验。*

![](img/727b846e53a2ba857349c127c92d9a63.png)

Selenium WebDriver 提供了一个 API，用于跟踪使用 WebDriver 执行测试脚本时发生的各种事件。许多导航事件在 WebDriver 内部事件发生之前和之后被触发(例如导航到 URL 之前和之后以及浏览器反向导航之前和之后)，这些事件可以被跟踪和捕获。

为了抛出一个事件，WebDriver 为您提供了一个名为 EventFiringWebDriver 的类，为了捕获该事件，它为测试脚本开发人员提供了一个名为 WebDriverEventListener 的接口。测试脚本开发人员应该为接口中被覆盖的方法提供自己的实现。

# eventFiringWebDriver 和 eventListener 类简介

EventFiringWebDriver 类是 WebDriver 的包装器，它为驱动程序提供了触发事件的能力。另一方面，EventListener 类等待侦听 EventFiringWebDriver 并处理所有调度的事件。对于要触发的事件，可能有多个侦听器等待从 EventFiringWebDriver 类中进行侦听。所有事件侦听器都应该向 EventFiringWebDriver 类注册以获得通知。

下面的流程图解释了在测试用例执行过程中，要捕获 EventFiringWebDriver 引发的所有事件，必须做些什么:

## 创建 EventListener 实例

`EventListener`类处理由`EventFiringWebDriver`类调度的所有事件。创建一个`EventListener`类有两种方法:

通过实现`WebDriverEventListener`接口

通过扩展 WebDriver 库中提供的`AbstractWebDriverEventListener`类

作为一名测试脚本开发人员，选择走哪条路取决于您。

## 实现 WebDriverEventListener

`WebDriverEventListener`接口声明了所有的事件方法。`EventFiringWebDriver`类一旦意识到事件已经发生，就调用`WebDriverEventListener`的注册方法。

这里，已经创建了一个`IAmTheEventListener`命名类，并且已经实现了`WebDriverEventListener`。现在，您需要为其中声明的所有方法提供一个实现。目前，`WebDriverEventListener`中有 15 种方法。

用所有 15 个重写方法创建的类如下所示:

```
public class IAmTheEventListener implements WebDriverEventListener {@Overridepublic void beforeAlertAccept(WebDriver webDriver) {}@Overridepublic void afterAlertAccept(WebDriver webDriver) {}@Overridepublic void afterAlertDismiss(WebDriver webDriver) {}@Overridepublic void beforeAlertDismiss(WebDriver webDriver) {}@Overridepublic void beforeNavigateTo(String url, WebDriver webDriver) {System.out.println("Before Navigate To " + url);}@Overridepublic void afterNavigateTo(String s, WebDriver webDriver) {System.out.println("Before Navigate Back. Right now I'm at "+ webDriver.getCurrentUrl());}@Overridepublic void beforeNavigateBack(WebDriver webDriver) {}@Overridepublic void afterNavigateBack(WebDriver webDriver) {}@Overridepublic void beforeNavigateForward(WebDriver webDriver) {}@Overridepublic void afterNavigateForward(WebDriver webDriver) {}@Overridepublic void beforeNavigateRefresh(WebDriver webDriver)     {}@Overridepublic void afterNavigateRefresh(WebDriver webDriver) {}@Overridepublic void beforeFindBy(By by, WebElement webElement, WebDriver webDriver) {}@Overridepublic void afterFindBy(By by, WebElement webElement, WebDriver webDriver) {}@Overridepublic void beforeClickOn(WebElement webElement, WebDriver webDriver) {}@Overridepublic void afterClickOn(WebElement webElement, WebDriver webDriver) {}@Overridepublic void beforeChangeValueOf(WebElement webElement, WebDriver webDriver, CharSequence[] charSequences) {}@Overridepublic void afterChangeValueOf(WebElement webElement, WebDriver webDriver, CharSequence[] charSequences) {}@Overridepublic void beforeScript(String s, WebDriver webDriver)     {}@Overridepublic void afterScript(String s, WebDriver webDriver)     {}@Overridepublic void onException(Throwable throwable, WebDriver webDriver) {}}
```

## 扩展 AbstractWebDriverEventListener

创建监听器类的第二种方法是扩展`AbstractWebDriverEventListene r`类。`AbstractWebDriverEventListener`是实现`WebDriverEventListener`的抽象类。

虽然它并没有为`WebDriverEventListener`接口中的方法提供任何实现，但是它创建了一个虚拟实现，这样您创建的监听器类就不必包含所有的方法，只包含您作为测试脚本开发人员感兴趣的方法。

下面是创建的一个类，它扩展了`AbstractWebDriverEventListener`，并为其中的一些方法提供了实现。这样，您可以只重写您感兴趣的方法，而不是类中的所有方法:

```
package com.example;import org.openqa.selenium.WebDriver;import    org.openqa.selenium.support.events.AbstractWebDriverEventListener;public class IAmTheEventListener2 extends AbstractWebDriverEventListener {@Overridepublic void beforeNavigateTo(String url, WebDriver driver) {System.out.println("Before Navigate To "+ url);}@Overridepublic void beforeNavigateBack(WebDriver driver) {System.out.println("Before Navigate Back. Right now I'm at "+ driver.getCurrentUrl());}}
```

## 创建 WebDriver 实例

既然您已经创建了监听所有生成事件的监听器类，那么是时候创建您的测试脚本类并让它调用`IAmTheDriver.java`了。创建该类后，在其中声明一个 ChromeDriver 实例:

```
WebDriver driver = new ChromeDriver();
```

`ChromeDriver`实例将是驱动所有驱动事件的底层驱动实例。这不是什么新鲜事。下一节解释的步骤是让这个驱动程序成为`EventFiringWebDriver`的一个实例。

## 创建 EventFiringWebDriver 和 EventListener 实例

现在您已经有了基本的驱动程序实例，在构造`EventFiringWebDriver`实例时将它作为参数传递。您可以使用这个驱动程序实例来执行所有进一步的用户操作。

使用下面的代码，实例化`EventListener`、`IAmTheEventListener.java`或`IAmTheEventListener2.java`类。这将是所有事件调度到的类:

```
EventFiringWebDriver eventFiringDriver =new EventFiringWebDriver(driver);IAmTheEventListener eventListener =new IAmTheEventListener();
```

## 正在向 EventFiringWebDriver 注册 EventListener

对于由`EventListener`通知的事件执行，您必须将`EventListener`注册到`EventFiringWebDriver`类。现在，`EventFiringWebDriver`类将知道通知发送到哪里。这是通过下面一行代码完成的:`eventFiringDriver.register(eventListener);`

## 执行和验证事件

现在是您的测试脚本执行事件的时候了，比如导航事件。导航到谷歌，然后脸书。使用浏览器的后退导航返回到谷歌。测试脚本的完整代码如下:

```
public class IAmTheDriver {public static void main(String... args){System.setProperty("webdriver.chrome.driver","./src/test/resources/drivers/chromedriver");WebDriver driver = new ChromeDriver();try {EventFiringWebDriver eventFiringDriver = newEventFiringWebDriver(driver);IAmTheEventListener eventListener = new IAmTheEventListener();eventFiringDriver.register(eventListener);eventFiringDriver.get("http://www.google.com");eventFiringDriver.get("http://www.facebook.com");eventFiringDriver.navigate().back();} finally {driver.close();driver.quit();}}}
```

在前面的代码中，您修改了监听器类，以记录从`AbstractWebDriverEventListener`类继承的事件之前和之后的`navigateTo`和`navigateBack`。修改后的方法如下:

```
@Overridepublic void beforeNavigateTo(String url, WebDriver driver) {System.out.println("Before Navigate To: " + url+ " and Current url is: " + driver.getCurrentUrl());}@Overridepublic void afterNavigateTo(String url, WebDriver driver) {System.out.println("After Navigate To: " + url+ " and Current url is: " + driver.getCurrentUrl());}@Overridepublic void beforeNavigateBack(WebDriver driver) {System.out.println("Before Navigate Back. Right now I'm at " + driver.getCurrentUrl());}@Overridepublic void afterNavigateBack(WebDriver driver) {System.out.println("After Navigate Back. Right now I'm at " + driver.getCurrentUrl());}
```

现在，如果您执行您的测试脚本，输出将如下所示:

```
Before Navigate To: http://www.google.com and Current url is: data:,After Navigate To: http://www.google.com and Current url is: [https://www.google.com/?gws_rd=ssl](https://www.google.com/?gws_rd=ssl)Before Navigate To: http://www.facebook.com and Current url is: [https://www.google.com/?gws_rd=ssl](https://www.google.com/?gws_rd=ssl)After Navigate To: http://www.facebook.com and Current url is: [https://www.facebook.com/](https://www.facebook.com/)Before Navigate Back. Right now I'm at [https://www.facebook.com/](https://www.facebook.com/)After Navigate Back. Right now I'm at [https://www.google.com/?gws_rd=ssl](https://www.google.com/?gws_rd=ssl)
```

## 注册多个事件侦听器

您可以使用`EventFiringWebDriver`注册多个监听器。一旦事件发生，所有注册的侦听器都会得到通知。修改您的测试脚本来注册您的`IAmTheListener.java`和`IAmTheListener2.java`文件:

```
public class RegisteringMultipleListeners {public static void main(String... args){System.setProperty("webdriver.chrome.driver","./src/test/resources/drivers/chromedriver");WebDriver driver = new ChromeDriver();try {EventFiringWebDriver eventFiringDriver = newEventFiringWebDriver(driver);IAmTheEventListener eventListener = new IAmTheEventListener();IAmTheEventListener2 eventListener2 = newIAmTheEventListener2();eventFiringDriver.register(eventListener);eventFiringDriver.register(eventListener2);eventFiringDriver.get("http://www.google.com");eventFiringDriver.get("http://www.facebook.com");eventFiringDriver.navigate().back();} finally {driver.close();driver.quit();}}}
```

稍微修改一下侦听器，以区分日志语句。现在，如果您执行上述代码，您将看到以下输出:

```
Before Navigate To: http://www.google.com and Current url is: data:,Before Navigate To [http://www.google.com](http://www.google.com)After Navigate To: http://www.google.com and Current url is: [https://www.google.com/?gws_rd=ssl](https://www.google.com/?gws_rd=ssl)Before Navigate To: http://www.facebook.com and Current url is: [https://www.google.com/?gws_rd=ssl](https://www.google.com/?gws_rd=ssl)Before Navigate To [http://www.facebook.com](http://www.facebook.com)After Navigate To: http://www.facebook.com and Current url is: [https://www.facebook.com/](https://www.facebook.com/)Before Navigate Back. Right now I'm at [https://www.facebook.com/](https://www.facebook.com/)Before Navigate Back. Right now I'm at [https://www.facebook.com/](https://www.facebook.com/)After Navigate Back. Right now I'm at [https://www.google.com/?gws_rd=ssl](https://www.google.com/?gws_rd=ssl)
```

*如果您觉得这篇文章很有趣，您可以浏览* [*Selenium WebDriver 3 实用指南—第二版*](https://www.amazon.com/Selenium-WebDriver-Practical-Guide-End/dp/1788999762) *以获得跨浏览器、移动和数据驱动测试的真实示例，了解 Selenium WebDriver 3 的所有最新功能。*[*【Selenium web driver 3 实用指南——第二版*](https://www.packtpub.com/web-development/selenium-webdriver-3-practical-guide-second-edition) *涵盖了所有这些特性和源代码，包括一个允许您使用 HMTL5 应用程序的演示网站和全书中的其他示例。*