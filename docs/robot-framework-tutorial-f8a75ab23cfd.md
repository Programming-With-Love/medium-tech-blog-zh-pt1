# 你所需要知道的关于 Python 的机器人框架

> 原文：<https://medium.com/edureka/robot-framework-tutorial-f8a75ab23cfd?source=collection_archive---------0----------------------->

![](img/0e18752103094c50860ff2cf94dbae71.png)

Robot Framework in Python — Edureka

Python 编程语言有一个机器人框架，可以使用 selenium 这样的外部库进行 web 测试。在本文中，我们将通过一个使用 selenium 库进行 web 测试的用例，了解测试用例以及 python 中与 robot framework 相关的各种其他术语。本博客讨论了以下主题:

*   *Python 中的机器人框架是什么？*

1.  *机器人框架架构*
2.  *安装*

*   *标准库*
*   *内置工具*
*   *测试用例*

1.  *工作流程测试*
2.  *高级测试*
3.  *数据驱动测试*

*   *关键词*

1.  *图书馆关键词*
2.  *用户关键词*

*   *变量*

1.  *定义变量*
2.  *使用变量*

*   *组织测试用例*
*   *机器人框架-硒库*

1.  *安装*
2.  *浏览器驱动*

*   *用例——使用机器人框架和硒库进行网络测试*

# 机器人框架是什么？

Robot framework 是一个通用的开源自动化框架，用于验收测试、验收测试驱动的开发和机器人流程自动化。它使用关键字驱动的测试技术方法。这些功能可以通过 Java 或 Python 实现的测试库来扩展。

***验收测试***

它是一种测试系统能力的测试技术。验收测试的目的是根据业务需求评估系统的能力。

***验收测试驱动开发***

ATDD 或验收测试驱动开发是一种基于业务客户、开发人员和测试人员之间交流的开发方法。他们协同工作，并在实现功能之前进行验收测试。

***机器人过程自动化***

RPA 或机器人流程自动化是通过使用机器学习和人工智能功能的软件，方便地减少人类工作的过程。RPA 处理高级别的可重复任务。

## 机器人框架体系结构

robot 框架是独立于平台的，尽管核心框架是使用 python 实现的，但它也可以运行在 JPython(JVM)和 IronPython(.网)。

![](img/ec5185074dfe3e4013c2c6d2b7ab934a.png)

当流程开始时，测试数据采用易于编辑的格式。框架处理测试数据并生成日志和报告。交互由库处理，库可以使用额外的测试工具作为测试目标的驱动程序。

## **安装**

在 python 上安装机器人框架的推荐方法是使用 pip。您可以使用下面的命令来安装框架。

```
pip install robotframework
```

**验证安装**

成功安装后，您应该能够使用-version 选项看到解释器和机器人框架的版本。

```
robot --version 
rebot --version
```

# 标准库

以下测试库随 robot framework 一起分发。

*   布尔廷
*   收集
*   日期时间
*   对话
*   操作系统
*   过程
*   遥远的
*   屏幕上显示程序运行的图片
*   线
*   用于远程联接服务的标准协议或者实现此协议的软件(可为动词)
*   可扩展标记语言

## 内置工具

除了核心的测试执行引擎之外，还有一些内置在 robot 框架中的支持工具。python 中的 robot 框架提供了以下内置工具。

*   勒博
*   Libdoc
*   Testdoc
*   整洁的

机器人框架测试数据在下面列出的不同章节中定义。

![](img/f74bdd7594cee6b783637ce9cc505f85.png)

# 测试案例

测试用例可以在下面的测试中进行分类。

*   工作流测试
*   更高级别的测试
*   数据驱动测试

## 工作流测试

机器人框架测试用例通常以表格语法编写。让我们看一下例子来理解这一点。

*   用户可以创建帐户并登录
*   用户无法使用错误的密码登录

```
*** Test Cases ***
User can create an account and log in
    Create Valid User    python    P4ssw0rd
    Attempt to Login with Credentials    python    P4ssw0rd
    Status Should Be    Logged In

User cannot log in with bad password
    Create Valid User    pytHon    P4ssw0rd
    Attempt to Login with Credentials    pytHon    wrong
    Status Should Be    Access Denied
```

python 中的机器人框架遵循用简单英语编写的测试用例，而不是自动化测试用例。它遵循关键字驱动的方法，在行动和期望方面与自然语言产生共鸣。

测试用例由关键字和可能的参数构成。

## 更高级别的测试

机器人框架中的测试用例可以只使用高级关键字而不使用位置参数来构建。让我们通过下面的一个例子来理解高级测试。

```
*** Test Cases ***
User can change password
    Given a user has a valid account
    When she changes her password
    Then she can log in with the new password
    And she cannot use the old password anymore
```

## 数据驱动测试

数据驱动测试允许在不重复工作流程的情况下改变测试数据。robot 框架中的[Template]设置将测试用例设置为数据驱动测试。

```
*** Test Cases ***
Invalid password
    [Template]    Creating user with invalid password should fail
    abCD5            ${PWD INVALID LENGTH}
    abCD56789    ${PWD INVALID LENGTH}
    123DEFG       ${PWD INVALID CONTENT}
    abcd56789      ${PWD INVALID CONTENT}
    abCdEfGh      ${PWD INVALID CONTENT}
    abCD56+        ${PWD INVALID CONTENT}
```

# 关键词

机器人框架中的测试用例是用来自两个来源的关键字创建的。

*   库关键字
*   用户关键字

## 库关键字

所有最低级别的关键字都在标准库中定义，这些标准库可以使用 Python、Java 等编程语言来实现。

机器人框架带有测试库，可以分为标准库、外部库和自定义库。标准库在核心框架中，如内置、截图、操作系统等。外部库是单独安装的，比如用于 web 测试的 seleniumlibrary。

若要使用资源库中的关键词，必须导入资源库设置。让我们看一个例子来理解这一点。

```
*** Settings ***
Library           OperatingSystem
Library           lib/LoginLibrary.py
```

## 用户关键字

robot 框架的一个强大特性是，我们可以使用其他关键字定制高级关键字。让我们看一个例子来理解它是如何工作的。

```
*** Keywords ***
Clear login database
    Remove file    ${DATABASE FILE}

Create valid user
    [Arguments]    ${username}    ${password}
    Create user    ${username}    ${password}
    Status should be    SUCCESS

Creating user with invalid password should fail
    [Arguments]    ${password}    ${error}
    Create user    example    ${password}
    Status should be    Creating user failed: ${error}

Login
    [Arguments]    ${username}    ${password}
    Attempt to login with credentials    ${username}    ${password}
    Status should be    Logged In

# Keywords below used by higher level tests. Notice how given/when/then/and
# prefixes can be dropped. And this is a comment.

A user has a valid account
    Create valid user    ${USERNAME}    ${PASSWORD}

She changes her password
    Change password    ${USERNAME}    ${PASSWORD}    ${NEW PASSWORD}
    Status should be    SUCCESS

She can log in with the new password
    Login    ${USERNAME}    ${NEW PASSWORD}

She cannot use the old password anymore
    Attempt to login with credentials    ${USERNAME}    ${PASSWORD}
    Status should be    Access Denied
```

用户定义的关键词可以包括由其他用户定义的关键词或库关键词提供的动作。

# 变量

变量是机器人框架中任何测试用例的一个非常重要的部分。任何数据都是易变的测试用例，最好使用变量来定义。

让我们看看如何在测试用例中定义变量。

## 定义变量

```
*** Variables ***
${USERNAME}               janedoe
${PASSWORD}               J4n3D0e
${NEW PASSWORD}           e0D3n4J
${DATABASE FILE}          ${TEMPDIR}${/}robotframework-quickstart-db.txt
${PWD INVALID LENGTH}     Password must be 7-12 characters long
${PWD INVALID CONTENT}    Password must be a combination of lowercase and uppercase letters and numbers
```

除了用户定义的变量之外，robot 框架中还有内置变量，如＄{ TEMPDIR }和＄{/}我们在上面的示例中也使用了它们。

## 使用变量

我们可以在测试用例中的任何地方使用变量，它们通常被用作关键字中的参数。让我们看一下例子来理解这一点。

```
*** Test Cases ***
User status is stored in database
    [Tags]    variables    database
    Create Valid User    ${USERNAME}    ${PASSWORD}
    Database Should Contain    ${USERNAME}    ${PASSWORD}    Inactive
    Login    ${USERNAME}    ${PASSWORD}
    Database Should Contain    ${USERNAME}    ${PASSWORD}    Active

*** Keywords ***
Database Should Contain
    [Arguments]    ${username}    ${password}    ${status}
    ${database} =     Get File    ${DATABASE FILE}
    Should Contain    ${database}    ${username}t${password}t${status}n
```

现在我们知道了，我们如何使用关键字和变量来创建一个测试用例。让我们试着理解我们将如何组织测试用例。

# 组织测试用例

机器人测试用例是在测试用例文件中创建的，但是我们可以将它们组织到创建测试套件层次结构的目录中。

测试用例的集合被称为测试套件。每个包含测试用例的文件也形成了一个测试套件。可以使用目录在一个层次结构中组织测试用例，所有这些目录创建了高级测试套件，它们的名字来自目录名。

## **设置和拆卸**

如果您想要在测试之前或之后执行测试中的特定关键字，您可以使用设置表中的“测试设置”和“测试拆卸”设置。您还可以使用“Suite Setup”和“Suite Teardown”在测试套件之前或之后执行关键字。

您还可以像[Template]一样在测试用例中创建自定义的[Setup]和[Teardown]。让我们看一个例子来理解这一点。

```
*** Settings ***
Suite Setup       Clear Login Database
Test Teardown     Clear Login Database
```

## **使用标签**

机器人框架允许标签为测试用例提供免费的元数据。可以使用“强制标签”和“默认标签”在文件中设置标签。

可以像[Template]一样使用[Tags]为单个测试用例给出标签。让我们举一个例子来理解我们如何使用标签。

```
*** Settings ***
Force Tags        quickstart
Default Tags      example    smoke
```

在执行之后，报告将会有与测试用例相关联的标签，以及基于标签的统计数据。

# 机器人框架-硒库

robot 框架中的 selenium 库是一个 web 测试库，它在内部使用 selenium 工具。Selenium 库可以很好地与 python 2.7、3.4 和更新的版本一起工作。除了标准的 python 解释器，除了 IronPython，它还可以与 Pypy 和 JPython 一起工作。

## 装置

要安装 selenium 库，我们可以遵循使用 python 中的 pip 的典型方法。使用以下命令在 python 中安装 seleniumlibrary。

```
pip install robotframework-seleniumlibrary
```

## 浏览器驱动程序

安装完成后，您仍然需要为测试中要使用的操作系统和浏览器安装相关的驱动程序。一般方法是安装浏览器驱动程序，如 Chromedriver for chrome，但也可以使用名为 Webdrivermanager 的工具。

它会在需要的时候找到最新的版本，它会在正确的位置下载所有的链接/文件。它支持所有主要的操作系统，并支持像 chrome，opera 等浏览器的下载。

您可以使用以下命令安装 WebdriverManager

```
pip install webdrivermanager
webdrivermanager firefox chrome --linkpath /usr/local/bin
```

## **如何使用硒库**

要使用 selenium 库，必须像其他库一样使用“library”设置导入它。建议使用 robot framework 高层关键字编写内部使用 selenium 低层关键字的测试用例。

这里有一个简单的例子来展示如何在测试用例中使用 selenium 库。

```
*** Settings ***
Documentation     Simple example using SeleniumLibrary.
Library           SeleniumLibrary

*** Variables ***
${LOGIN URL}      [http://localhost:7272](http://localhost:7272)
${BROWSER}        Chrome

*** Test Cases ***
Valid Login
    Open Browser To Login Page
    Input Username    demo
    Input Password    mode
    Submit Credentials
    Welcome Page Should Be Open
    [Teardown]    Close Browser

*** Keywords ***
Open Browser To Login Page
    Open Browser    ${LOGIN URL}    ${BROWSER}
    Title Should Be    Login Page

Input Username
    [Arguments]    ${username}
    Input Text    username_field    ${username}

Input Password
    [Arguments]    ${password}
    Input Text    password_field    ${password}

Submit Credentials
    Click Button    login_button

Welcome Page Should Be Open
    Title Should Be    Welcome Page
```

# 用例——使用 Robot 框架和 Selenium 库进行 Web 测试

在本例中，我们将创建以下目录。

1.  application —这是一个简单的登录应用程序，有登录页面、欢迎页面和错误页面。
2.  测试——这将包含所有的测试用例。
3.  任务—这将包含任务。

## **应用**

## **HTML**

*   **index.html**

```
<!DOCTYPE html>
<html>
<head>
  <title>Login Page</title>
  <link href="demo.css" media="all" rel="stylesheet" type="text/css">
  <script type="text/javascript">
    function login(username, password) {
        if (username == "demo" && password == "mode") {
            window.location = "welcome.html";
        } else {
            window.location = "error.html";
        }
    }
  </script>
</head>
<body>
  <div id="container">
    <h1>Login Page</h1>
      <p>Please input your user name and password and click the login button.</p>
      <form name="login_form" onsubmit="login(this.username_field.value, this.password_field.value); return false;">
        <table>
          <tr>
            <td><label for="username_field">User Name:</label></td>
            <td><input id="username_field" size="30" type="text"></td>
          </tr>
          <tr>
            <td><label for="password_field">Password:</label></td>
            <td><input id="password_field" size="30" type="password"></td>
          </tr>
          <tr>
            <td>&nbsp;</td>
            <td><input id="login_button" type="submit" value="LOGIN"></td>
          </tr>
        </table>
      </form>
  </div>
</body>
</html>
```

*   **welcome.html**

```
<!DOCTYPE html>
<html>
<head>
  <title>Welcome Page</title>
  <link href="demo.css" media="all" rel="stylesheet" type="text/css">
</head>
<body>

<div id='container'>

<h1>Welcome Page</h1>

Login succeeded. Now you can <a href=".">logout</a>.

</div>

</body>
</html>
```

*   **error.html**

```
<!DOCTYPE html>
<html>
<head>
  <title>Error Page</title>
  <link href="demo.css" media="all" rel="stylesheet" type="text/css">
</head>
<body>

<div id="container">

<h1>Error Page</h1>

Login failed. Invalid user name and/or password.

  </div>

</body>
</html>
```

*   **demo.css**

```
body {
    font-family: sans-serif;
    color: black;
    background: #DDDDDD;
}
#container {
    width: 30em;
    height: 15em;
    margin: 5em auto;
    background: white;
    border: 1px solid gray;
    padding: 0.5em 2em;
}
```

*   **server.py**

```
from __future__ import print_function

from os import chdir
from os.path import abspath, dirname, join
try:
    from SocketServer import ThreadingMixIn
    from BaseHTTPServer import HTTPServer
    from SimpleHTTPServer import SimpleHTTPRequestHandler
except ImportError:
    from socketserver import ThreadingMixIn
    from http.server import SimpleHTTPRequestHandler, HTTPServer

ROOT = join(dirname(abspath(__file__)), 'html')
PORT = 7272

class DemoServer(ThreadingMixIn, HTTPServer):
    allow_reuse_address = True

    def __init__(self, port=PORT):
        HTTPServer.__init__(self, ('localhost', int(port)),
                            SimpleHTTPRequestHandler)

    def serve(self, directory=ROOT):
        chdir(directory)
        print('Demo server starting on port %d.' % self.server_address[1])
        try:
            server.serve_forever()
        except KeyboardInterrupt:
            server.server_close()
        print('Demo server stopped.')

if __name__ == '__main__':
    import sys
    try:
        server = DemoServer(*sys.argv[1:])
    except (TypeError, ValueError):
        print(__doc__)
    else:
        server.serve()
```

## **测试**

*   **valid_login.robot**

```
*** Settings ***
Documentation     A test suite with a single test for valid login.
...
...               This test has a workflow that is created using keywords in
...               the imported resource file.
Resource          resource.robot

*** Test Cases ***
Valid Login
    Open Browser To Login Page
    Input Username    demo
    Input Password    mode
    Submit Credentials
    Welcome Page Should Be Open
    [Teardown]    Close Browser
```

*   **invalid_login.robot**

```
*** Settings ***
Documentation     A test suite containing tests related to invalid login.
...
...               These tests are data-driven by their nature. They use a single
...               keyword, specified with Test Template setting, that is called
...               with different arguments to cover different scenarios.
...
...               This suite also demonstrates using setups and teardowns in
...               different levels.
Suite Setup       Open Browser To Login Page
Suite Teardown    Close Browser
Test Setup        Go To Login Page
Test Template     Login With Invalid Credentials Should Fail
Resource          resource.robot

*** Test Cases ***               USER NAME        PASSWORD
Invalid Username                 invalid          ${VALID PASSWORD}
Invalid Password                 ${VALID USER}    invalid
Invalid Username And Password    invalid          whatever
Empty Username                   ${EMPTY}         ${VALID PASSWORD}
Empty Password                   ${VALID USER}    ${EMPTY}
Empty Username And Password      ${EMPTY}         ${EMPTY}

*** Keywords ***
Login With Invalid Credentials Should Fail
    [Arguments]    ${username}    ${password}
    Input Username    ${username}
    Input Password    ${password}
    Submit Credentials
    Login Should Have Failed

Login Should Have Failed
    Location Should Be    ${ERROR URL}
    Title Should Be    Error Page
```

*   **资源.机器人**

```
*** Settings ***
Documentation     A resource file with reusable keywords and variables.
...
...               The system specific keywords created here form our own
...               domain specific language. They utilize keywords provided
...               by the imported SeleniumLibrary.
Library           SeleniumLibrary

*** Variables ***
${SERVER}         localhost:7272
${BROWSER}        Firefox
${DELAY}          0
${VALID USER}     demo
${VALID PASSWORD}    mode
${LOGIN URL}      [http://${SERVER}/](http://${SERVER}/)
${WELCOME URL}    [http://${SERVER}/welcome.html](/${SERVER}/welcome.html)
${ERROR URL}      [http://${SERVER}/error.html](/${SERVER}/error.html)

*** Keywords ***
Open Browser To Login Page
    Open Browser    ${LOGIN URL}    ${BROWSER}
    Maximize Browser Window
    Set Selenium Speed    ${DELAY}
    Login Page Should Be Open

Login Page Should Be Open
    Title Should Be    Login Page

Go To Login Page
    Go To    ${LOGIN URL}
    Login Page Should Be Open

Input Username
    [Arguments]    ${username}
    Input Text    username_field    ${username}

Input Password
    [Arguments]    ${password}
    Input Text    password_field    ${password}

Submit Credentials
    Click Button    login_button

Welcome Page Should Be Open
    Location Should Be    ${WELCOME URL}
    Title Should Be    Welcome Page
```

*   **小黄瓜 _ 登录.机器人**

```
*** Settings ***
Documentation     A test suite with a single Gherkin style test.
...
...               This test is functionally identical to the example in
...               valid_login.robot file.
Resource          resource.robot
Test Teardown     Close Browser

*** Test Cases ***
Valid Login
    Given browser is opened to login page
    When user "demo" logs in with password "mode"
    Then welcome page should be open

*** Keywords ***
Browser is opened to login page
    Open browser to login page

User "${username}" logs in with password "${password}"
    Input username    ${username}
    Input password    ${password}
    Submit credentials
```

*   **tasks.py**

```
from pathlib import Path
import shutil

from docutils.core import publish_cmdline
from invoke import task
from rellu.tasks import clean

assert Path.cwd() == Path(__file__).parent

[@task](http://twitter.com/task)
def project_docs(ctx):
    """Generate project documentation.

    These docs are visible at [http://robotframework.org/WebDemo/](http://robotframework.org/WebDemo/).
    """
    args = ['--stylesheet=style.css,extra.css',
            '--link-stylesheet',
            'README.rst',
            'docs/index.html']
    publish_cmdline(writer_name='html5', argv=args)
    print(Path(args[-1]).absolute())

[@task](http://twitter.com/task)
def move_docs(ctx):
    """Move report.html and log.html to docs

    These docs are visible [http://robotframework.org/WebDemo/](http://robotframework.org/WebDemo/).
    """
    log = Path('./log.html')
    report = Path('./report.html')
    dest = Path('.') / 'docs'
    print(log.absolute())
    shutil.copy(log.absolute(), str(dest))
    print(report.absolute())
    shutil.copy(report.absolute(), str(dest))
```

要运行我们的应用程序，只需运行 server.py 文件，登录页面将在 **https//:localhost:7272** 打开

![](img/75b204035dacb0360bc3e50308dfa5fc.png)

当演示运行时，您可以使用命令执行测试

```
robot tests
```

运行测试后，您将获得 HTML 格式的报告和日志文件。

**report.html**

![](img/dd2f08bf759bbbf146713133b9bcf1be.png)

**log.html**

![](img/f24a200a92b9a10a06de3607f621ec9d.png)

这就把我们带到了本文的结尾，在这里我们学习了如何使用 python 中的 robot framework 和 seleniumlibrary 在登录页面上执行 web 测试。我希望你清楚本教程中与你分享的所有内容。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=robot-framework-tutorial)

请留意本系列中的其他文章，它们将解释 Python 和数据科学的各个方面。

> 1.[Python 中的机器学习分类器](/edureka/machine-learning-classifier-c02fbd8400c9)
> 
> 2. [Python Scikit-Learn 备忘单](/edureka/python-scikit-learn-cheat-sheet-9786382be9f5)
> 
> 3.[机器学习工具](/edureka/python-libraries-for-data-science-and-machine-learning-1c502744f277)
> 
> 4.[用于数据科学和机器学习的 Python 库](/edureka/python-libraries-for-data-science-and-machine-learning-1c502744f277)
> 
> 5.[Python 中的聊天机器人](/edureka/how-to-make-a-chatbot-in-python-b68fd390b219)
> 
> 6. [Python 集合](/edureka/collections-in-python-d0bc0ed8d938)
> 
> 7. [Python 模块](/edureka/python-modules-abb0145a5963)
> 
> 8. [Python 开发者技能](/edureka/python-developer-skills-371583a69be1)
> 
> 9.[哎呀面试问答](/edureka/oops-interview-questions-621fc922cdf4)
> 
> 10.[Python 开发者简历](/edureka/python-developer-resume-ded7799b4389)
> 
> 11.[Python 中的探索性数据分析](/edureka/exploratory-data-analysis-in-python-3ee69362a46e)
> 
> 12.[带有 Python 的乌龟模块的贪吃蛇游戏](/edureka/python-turtle-module-361816449390)
> 
> 13. [Python 开发者工资](/edureka/python-developer-salary-ba2eff6a502e)
> 
> 14.[主成分分析](/edureka/principal-component-analysis-69d7a4babc96)
> 
> 15. [Python vs C++](/edureka/python-vs-cpp-c3ffbea01eec)
> 
> 16.[刺儿头教程](/edureka/scrapy-tutorial-5584517658fb)
> 
> 17. [Python SciPy](/edureka/scipy-tutorial-38723361ba4b)
> 
> 18.[最小二乘回归法](/edureka/least-square-regression-40b59cca8ea7)
> 
> 19. [Jupyter 笔记本小抄](/edureka/jupyter-notebook-cheat-sheet-88f60d1aca7)
> 
> 20. [Python 基础知识](/edureka/python-basics-f371d7fc0054)
> 
> 21. [Python 模式程序](/edureka/python-pattern-programs-75e1e764a42f)
> 
> 22.[Python 中的生成器](/edureka/generators-in-python-258f21e3d3ff)
> 
> 23. [Python 装饰器](/edureka/python-decorator-tutorial-bf7b21278564)
> 
> 24. [Python Spyder IDE](/edureka/spyder-ide-2a91caac4e46)
> 
> 25.[在 Python 中使用 Kivy 的移动应用](/edureka/kivy-tutorial-9a0f02fe53f5)
> 
> 26.[十大最好的学习书籍&练习 Python](/edureka/best-books-for-python-11137561beb7)
> 
> 27.[Python 中的套接字编程是什么](/edureka/socket-programming-python-bbac2d423bf9)
> 
> 28.[使用 PyGame 的 Python 中的贪吃蛇游戏](/edureka/snake-game-with-pygame-497f1683eeaa)
> 
> 29. [Django 采访问答](/edureka/django-interview-questions-a4df7bfeb7e8)
> 
> 30.[十大 Python 应用](/edureka/python-applications-18b780d64f3b)
> 
> 31.[Python 中的散列表和散列表](/edureka/hash-tables-and-hashmaps-in-python-3bd7fc1b00b4)
> 
> 32. [Python 3.8](/edureka/whats-new-python-3-8-7d52cda747b)
> 
> 33.[支持向量机](/edureka/support-vector-machine-in-python-539dca55c26a)
> 
> 34. [Python 教程](/edureka/python-tutorial-be1b3d015745)

*原载于 2019 年 10 月 17 日*[*https://www.edureka.co*](https://www.edureka.co/blog/robot-framework-tutorial/)*。*