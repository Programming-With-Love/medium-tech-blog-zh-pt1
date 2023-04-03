# Selenium 框架—数据驱动、关键字驱动和混合驱动

> 原文：<https://medium.com/edureka/selenium-framework-data-keyword-hybrid-frameworks-ea8d4f4ce99f?source=collection_archive---------1----------------------->

![](img/3f82da8eb817a2d51146cda282592538.png)

Selenium Framework — Edureka

在这篇关于 Selenium 框架的文章中，我将告诉您如何使用 Selenium 框架来优化您的代码结构，这将使您更接近成为 Selenium 认证专家。

# 什么是 Selenium 框架？

Selenium framework 是一种代码结构，它使代码维护更简单，代码可读性更好。框架包括将整个代码分解成更小的代码段，这些代码段测试特定的功能。

代码的结构使得“数据集”与测试 web 应用程序功能的实际“测试用例”相分离。它也可以以这样的方式构建，其中需要执行的测试用例从外部应用程序(如. csv)中调用。

有许多框架，但是 3 个常用的 Selenium 框架是:

*   数据驱动的框架
*   关键词驱动框架
*   混合框架

本文将通过一个演示来讨论这些框架。但是在继续之前，让我告诉你为什么需要一个 Selenium 框架，以及使用它们会有什么好处。

# 为什么我们需要一个 Selenium 框架？

如果没有一个合适的框架，将会有一个包含整个测试功能的测试用例。可怕的是，这个测试用例有能力产生多达一百万行代码。所以很明显，如此庞大的测试用例很难阅读。即使你以后想修改任何功能，修改代码也会很困难。

因为一个框架的实现，会产生更小但更多的代码片段，所以有各种各样的好处。

## Selenium 框架的优势

*   增加代码重用
*   提高代码可读性
*   更高的便携性
*   减少脚本维护

现在你已经知道了框架的基础，让我来详细解释一下它们。

# 数据驱动的框架

Selenium 中的数据驱动框架是将“数据集”与实际“测试用例”(代码)分离的技术。这个框架完全依赖于输入的测试数据。测试数据来自外部来源，如 excel 文件、CSV 文件或任何数据库。

![](img/601a64256a37d82e54b335198478e48e.png)

由于测试用例是从数据集中分离出来的，我们可以很容易地修改特定功能的测试用例，而不需要对代码进行大规模的修改。例如，如果您想要修改登录功能的代码，那么您可以只修改它，而不必修改同一代码中的任何其他相关部分。

除此之外，您还可以轻松控制需要测试多少数据。通过向 excel 文件(或其他来源)添加更多的用户名和密码字段，您可以很容易地增加测试参数的数量。

例如，如果我必须检查 web 页面的登录，那么我可以在 excel 文件中保存一组用户名和密码凭证，并将凭证传递给代码，以便在单独的 Java 类文件中对浏览器执行自动化操作。

# 使用 Apache POI 和 Selenium WebDriver

WebDriver 不直接支持 excel 文件的读取。因此，我们使用 **Apache POI** 来读写任何 Microsoft Office 文档。你可以从[这里](https://poi.apache.org/download.html)下载 Apache POI(一组 JAR 文件)。根据您的需求下载 zip 文件或 tar 文件，并将它们与 Selenium JARs 放在一起。

![](img/043aab337999a9d056b310f8c6f43c5e.png)

主代码和数据集之间的协调将由 *TestNG 数据提供者*负责，它是 Apache POI JAR 文件的一部分。出于演示目的，我创建了一个名为“LoginCredentials”的 excel 文件，其中用户名和密码存储在不同的列中。

![](img/59121d85f3bcd97e5cd5f814f032b1aa.png)

看一下下面的代码来理解测试用例。这是一个简单的代码，用于测试航班预订应用程序的登录功能。

```
package DataDriven;

import org.openqa.selenium.By;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

public class DDTExcel
{
 ChromeDriver driver;

 [@Test](http://twitter.com/Test)(dataProvider="testdata")
 public void DemoProject(String username, String password) throws InterruptedException
 {
 System.setProperty("webdriver.chrome.driver", "C:\\Users\\Vardhan\\Downloads\\chromedriver.exe");
 driver = new ChromeDriver();

 driver.get("[http://newtours.demoaut.com/](http://newtours.demoaut.com/)");

 driver.findElement(By.name("userName")).sendKeys(username);
 driver.findElement(By.name("password")).sendKeys(password);
 driver.findElement(By.name("login")).click();

 Thread.sleep(5000);

 Assert.assertTrue(driver.getTitle().matches("Find a Flight: Mercury Tours:"), "Invalid credentials");
 System.out.println("Login successful");
 }

 [@AfterMethod](http://twitter.com/AfterMethod)
 void ProgramTermination()
 {
 driver.quit();
 }

[@DataProvider](http://twitter.com/DataProvider)(name="testdata")
 public Object[][] TestDataFeed()
 {

 ReadExcelFile config = new ReadExcelFile("C:\\Users\\Vardhan\\workspace\\Selenium\\LoginCredentials.xlsx");

 int rows = config.getRowCount(0);

 Object[][] credentials = new Object[rows][2];

for(int i=0;i<rows;i++)
 {
 credentials[i][0] = config.getData(0, i, 0);
 credentials[i][1] = config.getData(0, i, 1);
 }

 return credentials;
 }
}
```

如果你从上面注意到了，我们有一个名为“TestDataFeed()”的方法。在这个方法中，我创建了另一个名为“ReadExcelFile”的类的对象实例。在实例化这个对象时，我已经输入了包含数据的 excel 文件的路径。我还定义了一个循环来从 excel 工作簿中检索文本。

但是，为了从给定的表号、列号和行号中读取数据，需要调用“ReadExcelFile”类。我的“ReadExcelFile”的代码如下。

```
package DataDriven;

import java.io.File;
import java.io.FileInputStream;

import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

public class ReadExcelFile
{
 XSSFWorkbook wb;
 XSSFSheet sheet;

 public ReadExcelFile(String excelPath)
 {
 try
 {
 File src = new File(excelPath);
 FileInputStream fis = new FileInputStream(src);
 wb = new XSSFWorkbook(fis);
 }

 catch(Exception e)
 {
 System.out.println(e.getMessage());
 }
 }

 public String getData(int sheetnumber, int row, int column)
 {
 sheet = wb.getSheetAt(sheetnumber);
 String data = sheet.getRow(row).getCell(column).getStringCellValue();
 return data;
 }

 public int getRowCount(int sheetIndex)
 {
 int row = wb.getSheetAt(sheetIndex).getLastRowNum();
 row = row + 1;
 return row;
 }
}
```

首先，注意我已经导入的库。我已经导入了 *Apache POI XSSF* 库，用于读取/写入数据到 excel 文件。这里，我创建了一个构造函数(相同方法的对象)来传递值:表号、行号和列号。

现在让我们继续讨论框架，即关键字驱动的框架。

# 关键词驱动框架

关键字驱动框架是一种技术，其中所有要执行的操作和指令都与实际测试用例分开编写。它与数据驱动框架的相似之处在于，要执行的操作再次存储在外部文件中，如 Excel 表。

![](img/8c5aad8e6ddcedf8107b2069fbe382ef.png)

我所说的操作只不过是需要作为测试用例的一部分来执行的方法。关键字驱动框架的好处是你可以很容易地控制你想要测试的功能。您可以在 excel 文件中指定测试应用程序功能的方法。因此，只有那些在 excel 中指定的方法名称才会被测试。

例如，对于登录 web 应用程序，我们可以在主测试用例中编写多个方法，其中每个测试用例将测试特定的功能。对于实例化浏览器驱动程序，可以有一种方法，对于查找用户名和密码字段，可以有多种方法，对于导航到网页，可以有另一种方法，等等。

![](img/97116dd0f812b4a21d72b898a98476c0.png)

看看下面的代码，了解框架的外观。如果您不理解，下面代码中注释掉的行可以作为解释。

```
package KeywordDriven;

import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.Test;

import java.util.concurrent.TimeUnit;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class Actions 
{
 public static WebDriver driver;

 public static void openBrowser()
 { 
 System.setProperty("webdriver.chrome.driver", "C:\\Users\\Vardhan\\Downloads\\chromedriver.exe");
 driver=new ChromeDriver();
 }

 public static void navigate()
 { 
 driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
 driver.get("[http://newtours.demoaut.com](http://newtours.demoaut.com)");
 }

 public static void input_Username()
 {
 driver.findElement(By.name("userName")).sendKeys("mercury"); 
 }

 public static void input_Password()
 {
 driver.findElement(By.name("password")).sendKeys("mercury");
 }

 public static void click_Login()
 {
 driver.findElement(By.name("login")).click();
 }

[@Test](http://twitter.com/Test)
 public static void verify_login()
 {
 String pageTitle = driver.getTitle();
 Assert.assertEquals(pageTitle, "Find a Flight: Mercury Tours:");
 }

 public static void closeBrowser()
 {
 driver.quit();
 }
}
```

正如您所看到的，需要测试的不同功能存在于等待调用的不同方法中。现在，基于 excel 文件中方法名称的存在，将从另一个类调用这些方法。同样，为了读取 excel 文件并返回结果，我编写了另一个类。它们都显示在下面。

调用方法的类文件是这样的。

```
package KeywordDriven;

public class DriverScript
{
 public static void main(String[] args) throws Exception 
 {
 //Declaring the path of the Excel file with the name of the Excel file
 String sPath = "C:\\Users\\Vardhan\\workspace\\Selenium Frameworks Demo\\dataEngine.xlsx"; 

 //Here we are passing the Excel path and SheetName as arguments to connect with Excel file
 ReadExcelData.setExcelFile(sPath, "Sheet1");

 //Hard coded values are used for Excel row & columns for now     
 //Hard coded values are used for Excel row & columns for now    
 //In later chapters we will replace these hard coded values with varibales    //This is the loop for reading the values of the column 3 (Action Keyword) row by row
 for (int iRow=1;iRow<=7;iRow++)
 {
 String sActions = ReadExcelData.getCellData(iRow, 1); 

 //Comparing the value of Excel cell with all the keywords in the "Actions" class
 if(sActions.equals("openBrowser"))
 { 
 //This will execute if the excel cell value is 'openBrowser'    
 //Action Keyword is called here to perform action
 Actions.openBrowser();
 }
 else if(sActions.equals("navigate"))
 {
 Actions.navigate();
 }
 else if(sActions.equals("input_Username"))
 {
 Actions.input_Username();
 }
 else if(sActions.equals("input_Password"))
 {
 Actions.input_Password();
 }
 else if(sActions.equals("click_Login"))
 {
 Actions.click_Login();
 } 
 else if(sActions.equals("verify_Login"))
 {
 Actions.verify_login();
 } 
 else if(sActions.equals("closeBrowser"))
 {
 Actions.closeBrowser();
 } 
 }
 }
}
```

读取 Excel 值的类文件是这样的。

```
package KeywordDriven;

import java.io.FileInputStream;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.apache.poi.xssf.usermodel.XSSFCell;

public class ReadExcelData
{
 private static XSSFSheet ExcelWSheet;
 private static XSSFWorkbook ExcelWBook;
 private static XSSFCell Cell; 

 //This method is to set the File path and to open the Excel file
 //Pass Excel Path and SheetName as Arguments to this method
 public static void setExcelFile(String Path,String SheetName) throws Exception 
 {
 FileInputStream ExcelFile = new FileInputStream(Path);
 ExcelWBook = new XSSFWorkbook(ExcelFile);
 ExcelWSheet = ExcelWBook.getSheet(SheetName);
 }

 //This method is to read the test data from the Excel cell
 //In this we are passing parameters/arguments as Row Num and Col Num
 public static String getCellData(int RowNum, int ColNum) throws Exception
 {
 Cell = ExcelWSheet.getRow(RowNum).getCell(ColNum);
 String CellData = Cell.getStringCellValue();
 return CellData;
 }
}
```

现在，让我们进入本文的最后一部分，我将向您展示如何构建一个混合框架。

# 混合框架

混合框架是一种我们可以充分利用数据驱动和关键字驱动的硒框架的技术。使用本文上面显示的示例，我们可以通过将要执行的方法存储在 excel 文件中(关键字驱动方法)并将这些方法名称传递给 *Java 反射类*(数据驱动方法)来构建一个混合框架，而不是在“DriverScript”类中创建一个 **If/Else** 循环。

看看下面代码片段中修改过的“DriverScript”类。这里，使用数据驱动的方法从 excel 文件中读取方法名称，而不是使用多个 If/ Else 循环。

```
package HybridFramework;

import java.lang.reflect.Method;

public class DriverScriptJava
{
 //This is a class object, declared as 'public static'
 //So that it can be used outside the scope of main[] method
 public static Actions actionKeywords;

 public static String sActions;

 //This is reflection class object, declared as 'public static' 
 //So that it can be used outside the scope of main[] method
 public static Method method[];

 public static void main(String[] args) throws Exception 
 {
 //Declaring the path of the Excel file with the name of the Excel file
 String sPath = "C:\\Users\\Vardhan\\workspace\\Selenium Frameworks Demo\\dataEngine.xlsx";

 //Here we are passing the Excel path and SheetName to connect with the Excel file     
 //This method was created previously
 ReadExcelData.setExcelFile(sPath, "Sheet1");

 //Hard coded values are used for Excel row & columns for now     
 //Later on, we will use these hard coded value much more efficiently    
 //This is the loop for reading the values of the column (Action Keyword) row by row 
 //It means this loop will execute all the steps mentioned for the test case in Test Steps sheet
 for (int iRow=1;iRow<=7;iRow++)
 {
 sActions = ReadExcelData.getCellData(iRow, 1);
 //A new separate method is created with the name 'execute_Actions'
 //You will find this method below of the this test 
 //So this statement is doing nothing but calling that piece of code to execute
 execute_Actions(); 
 }
 }

//This method contains the code to perform some action 
//As it is completely different set of logic, which revolves around the action only, it makes sense to keep it separate from the main driver script 
//This is to execute test step (Action)
private static void execute_Actions() throws Exception 
 {
 //Here we are instantiating a new object of class 'Actions'
 actionKeywords = new Actions();

 //This will load all the methods of the class 'Actions' in it. 
 //It will be like array of method, use the break point here and do the watch 
 method = actionKeywords.getClass().getMethods();

 //This is a loop which will run for the number of actions in the Action Keyword class 
 //method variable contain all the method and method.length returns the total number of methods
 for(int i = 0;i<method.length;i++)
 {
  //This is now comparing the method name with the ActionKeyword value received from the excel
  if(method[i].getName().equals(sActions))
 { //In case of match found, it will execute the matched method 
  method[i].invoke(actionKeywords);
   //Once any method is executed, this break statement will take the flow outside of for loop
  break;
 }
 }
 }
}
```

我希望本文对您有用，让您清楚地了解什么是 Selenium 框架，它有什么好处，以及如何使用这 3 个 Selenium 框架构建代码结构。

如果您想查看更多关于市场上最流行的技术的文章，比如人工智能、DevOps、伦理黑客，您可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=selenium-framework-data-keyword-hybrid-frameworks)

一定要注意本系列的其他文章，这些文章将解释硒的其他方面。

> 1.[硒教程](/edureka/selenium-tutorial-77879a1d9af1)
> 
> 2.[Selenium WebDriver:TestNG For Test Case Management&报表生成](/edureka/selenium-webdriver-tutorial-e3e6219f21ad)
> 
> 3.[硒中的定位器](/edureka/locators-in-selenium-f6e6b282aed8)
> 
> 4. [XPath 教程](/edureka/xpath-in-selenium-cd659373e01a)
> 
> 5.[硒中等待](/edureka/waits-in-selenium-5b57b56f5e5a)
> 
> 6.[搭建硒栅进行分布式硒测试](/edureka/selenium-grid-tutorial-ef342799c484)
> 
> 7.[硒用蟒蛇](/edureka/selenium-using-python-edc22a44f819)
> 
> 8.[使用 LambdaTest 的跨浏览器测试](/edureka/cross-browser-testing-9299b04ce277)
> 
> 9.[使用 Selenium 进行跨浏览器测试](/edureka/cross-browser-testing-using-selenium-90b1911c6d60)
> 
> 10.[在 Selenium 中处理多个窗口](/edureka/handle-multiple-windows-in-selenium-727ba5f8f6a7)
> 
> 11.[Selenium 中的页面对象模型](/edureka/page-object-model-in-selenium-bc4d7c8c4203)
> 
> 12.[硒项目](/edureka/selenium-projects-b2df15d35fe2)
> 
> 13. [QTP vs 硒](/edureka/qtp-vs-selenium-338f3d3bbfa7)
> 
> 14.[硒与 RPA](/edureka/selenium-vs-rpa-84159dbcd0f2)
> 
> 15. [Selenium WebDriver 架构](/edureka/selenium-webdriver-architecture-565e2db26dd5)
> 
> 16.[处理 Selenium 中的异常](/edureka/exceptions-in-selenium-369c38155e7d)
> 
> 17.[使用黄瓜&硒](/edureka/cucumber-selenium-tutorial-aefec05f4733)进行网站测试

*原载于 2018 年 4 月 5 日 www.edureka.co**的* [*。*](https://www.edureka.co/blog/selenium-framework-data-keyword-hybrid-frameworks)