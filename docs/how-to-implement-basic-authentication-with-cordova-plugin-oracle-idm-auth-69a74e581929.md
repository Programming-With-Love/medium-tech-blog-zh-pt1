# 如何用 cordova-plugin-oracle-idm-auth 实现基本认证

> 原文：<https://medium.com/oracledevs/how-to-implement-basic-authentication-with-cordova-plugin-oracle-idm-auth-69a74e581929?source=collection_archive---------0----------------------->

我们最近发布了[Cordova-plugin-Oracle-IDM-auth](https://github.com/oracle/cordova-plugin-oracle-idm-auth)Cordova 插件，支持基本认证、OAUTH、Web SSO 和 OpenID Connect 认证方法。

在这篇文章中，我将演示如何在 Oracle JavaScript Extension Toolkit(JET)混合移动应用程序中使用这个插件实现基本身份验证。您将能够在任何基于 Cordova 的应用程序中使用类似的 JavaScript 代码。

为了测试这个实现，您需要访问受基本身份验证保护的服务器资源。您可以通过在浏览器中输入资源的 URL 并验证是否提示您输入凭据来确认这是否正常工作。

让我们从搭建一个空白的 JET 应用程序开始。通常情况下，你的应用会基于一个更复杂的模板，如“navdrawer ”,但我们将使用默认的“空白”模板来保持简单:

```
$ ojet create BasicAuthSample --hybrid
```

根据您在开发环境中安装的内容选择合适的移动平台，当应用程序搭建完成后，更改目录并添加插件:

```
$ cd BasicAuthSample
$ ojet add plugin cordova-plugin-oracle-idm-auth
```

现在，在您最喜欢的 IDE 中打开生成的源代码。

此时，如果您打算部署到 iOS 设备，您必须确保您的应用程序的捆绑 ID 与您打算使用的任何预置描述文件相匹配。为此，编辑 hybrid/config.xml 第二行中的 *id* 属性。您还必须创建一个构建配置文件，如这里的[所述](https://docs.oracle.com/middleware/jet410/jet/developer/GUID-DBA8BB97-1989-45B0-8C86-4897D5F302A9.htm#JETDG-GUID-102C9964-6293-446F-87FE-58480AF697C2)。

首先，我们将创建一个简单的登录表单，它具有一些额外的特性，有助于说明正在发生的事情。将以下代码添加到 src/index.html 的`<body>` 中:

```
<div id="mainContent" class="oj-hybrid-applayout-page">
   <div class="oj-applayout-content">
     <div role="main" class="oj-hybrid-applayout-content">
       <div class="oj-hybrid-padding">
         <div id="login-form" class="oj-form" style="margin-top:50px">
           <oj-label for="user" show-required>Username</oj-label>
           <oj-input-text id="user" required value="{{userName}}"></oj-input-text>
           <oj-label for="pass" show-required>Password</oj-label>
           <oj-input-password id="pass" required value="{{password}}"></oj-input-password>
           <div>
             <oj-button id='loginBtn' disabled="[[loginBtnDisabled]]" on-click="[[login]]">Login</oj-button>
             <oj-button id='logoutBtn' disabled="[[logoutBtnDisabled]]" on-click="[[logout]]">Logout</oj-button>
             <oj-button id='accessBtn' disabled="[[logoutBtnDisabled]]" on-click="[[accessResource]]">Access</oj-button>
           </div>
           <div>
             <span data-bind="text: messages"></span>
           </div>
         </div>
       </div>
     </div>
   </div>
 </div>
```

接下来，我们将通过以下方式在这些 UI 组件后面添加一些 JavaScript:

1.  定义所需的喷射组件
2.  添加视图模型
3.  通过挖空将视图模型绑定到页面。

修改 src/js/main.js 中的“require”块，如下所示:

```
require(['ojs/ojcore', 'knockout', 'jquery', 'ojs/ojknockout', 'ojs/ojinputtext', 'ojs/ojlabel', 'ojs/ojbutton'],
   function (oj, ko, $) {
     $(function () {
       function init() {
         ko.applyBindings(new BasicAuthViewModel(), document.getElementById("mainContent"));
       }function BasicAuthViewModel() {
         var self = this;
         self.userName = ko.observable();
         self.password = ko.observable();
         self.messages = ko.observable();
         self.loginBtnDisabled = ko.observable(true);
         self.logoutBtnDisabled = ko.observable(true);
         self.login = function() {}
         self.logout = function() {}
         self.accessResource = function() {}
       }// If running in a hybrid (e.g. Cordova) environment, we need to wait for the deviceready
       // event before executing any code that might interact with Cordova APIs or plugins.
       if ($(document.body).hasClass('oj-hybrid')) {
         document.addEventListener('deviceready', init);
       } else {
         init();
       }
     });
   }
 );
```

此时，您可以通过使用 ojet 命令行将应用程序提供给连接的 Android 设备来测试这个简单的表单:

```
ojet serve android --device
```

如果你更喜欢 Android 模拟器，使用`--no-livereload`选项来避免 CORS 拒绝的可能性。为了服务于一个 iOS 模拟器，你需要启用密钥链访问，如这里的[所述](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)。当服务于 iOS 设备时，您必须使用`--build-config`选项指定您之前创建的构建配置文件的位置。

现在是时候添加基本的身份验证逻辑了，方法是:

1.  构建身份验证流属性
2.  定义一些回调
3.  初始化登录流程

将以下代码添加到 src/js/main.js 中的 BasicAuthViewModel:

```
var authProps = cordova.plugins.IdmAuthFlows.newHttpBasicAuthPropertiesBuilder('appName', LOGIN_URL, LOGOUT_URL).offlineAuthAllowed(true).build();var initiateLogin = function() {
   // Initiate the login process
   self.basicAuthFlow.login(challengeHandler).then(loginSuccess).catch(loginFailure);
 };var challengeFields, challengeProceedHandler;
 var challengeHandler = function(fields, proceed) {
   challengeFields = fields;
   challengeProceedHandler = proceed;
   var err = fields[cordova.plugins.IdmAuthFlows.AuthChallenge.Error];
   if (err) {
     // Login failure that allows retry
     self.messages('User login failure: ' + err[cordova.plugins.IdmAuthFlows.Error.ErrorCode] + '. Try again.');
   }
   self.loginBtnDisabled(false);
 };var loginSuccess = function() {
   self.messages('User logged in successfully.');
   self.logoutBtnDisabled(false);
   self.loginBtnDisabled(true);
 };var loginFailure = function(err) {if (err[cordova.plugins.IdmAuthFlows.Error.ErrorSource] == cordova.plugins.IdmAuthFlows.ErrorSources.Plugin) {
     // No further retries are possible at this stage.  Re-initiate login to keep the demo running.
     self.messages('Login error: ' + err[cordova.plugins.IdmAuthFlows.Error.ErrorCode] + '. Restarting login...');
     initiateLogin();} else {self.messages('System error: ' + err[cordova.plugins.IdmAuthFlows.Error.TranslatedErrorMessage]);}
 };cordova.plugins.IdmAuthFlows.init(authProps).then(function(flow) {
   self.messages('Authentication flow initialized.  Please enter credentials to login.');
   self.basicAuthFlow = flow;
   initiateLogin();
 }).catch(function(err) {
   // Handle errors during init
   self.messages('Error while initializing authentication flow: ' + err[cordova.plugins.IdmAuthFlows.Error.ErrorCode]);
 });
```

在构建流属性时，必须指定基本身份验证服务器的 LOGIN_URL 和 LOGOUT_URL。请注意，有必要将流的 *offlineAuthAllowed* 属性设置为 true，以便能够检索将包含在后续 REST web 服务调用中的头。

最后，向我们之前在 src/js/main.js 中创建的三个空函数添加一些逻辑:

```
self.login = function() {
   challengeFields[cordova.plugins.IdmAuthFlows.AuthChallenge.UserName] = self.userName();
   challengeFields[cordova.plugins.IdmAuthFlows.AuthChallenge.Password] = self.password();
   challengeProceedHandler(challengeFields);
   self.messages('User credentials submitted for login...');
 };self.logout = function() {
   self.basicAuthFlow.logout().then(function() {
     self.messages('Logout success.');
     self.logoutBtnDisabled(true);
     self.loginBtnDisabled(false);
     initiateLogin();
   }).catch(function() {
   self.messages('Error while logging out.');
   });
 };self.accessResource = function() {
   self.basicAuthFlow.getHeaders().then(function(headers) {
     self.messages('Accessing secured resource with headers: ' + JSON.stringify(headers));
     var request = new XMLHttpRequest();
     request.open('GET', SECURED_RESOURCE_URL);
     for (var key in headers) {
       if (headers.hasOwnProperty(key)) {
         request.setRequestHeader(key, headers[key]);
       }
     }
     request.onload = function() {
       if (request.readyState == 4) {
         self.messages('Result: ' + request.response);
       }
     };
     request.send();
   }).catch(function(err) {
     self.messages('Error while getting headers: ' + err[cordova.plugins.IdmAuthFlows.Error.ErrorCode]);
   });
 };
```

在构建请求时，必须指定受保护资源的 SECURED_RESOURCE_URL。

通过`ojet serve`重新部署应该会产生一个正常运行的应用程序，使用户能够通过基本身份验证登录来访问您的安全资源。

应用程序的当前状态显示在页面底部，包括错误代码。由应用程序开发人员提供错误代码的(翻译的)字符串，这在[插件文档](https://github.com/oracle/cordova-plugin-oracle-idm-auth/blob/master/docs/error-codes.md)中有描述。

成功登录后，点击*访问*按钮应显示成功响应，应用程序已使用基本认证访问安全资源。

我希望这篇博文有助于理解使用[Cordova-plugin-Oracle-IDM-auth](https://github.com/oracle/cordova-plugin-oracle-idm-auth)插件实现基本认证。请继续关注将来描述如何使用该插件实现 WebSSO、OAuth 和 OpenID Connect 的帖子。

非常感谢插件的首席开发人员 Anand Nath 在这篇文章中对技术细节的帮助。