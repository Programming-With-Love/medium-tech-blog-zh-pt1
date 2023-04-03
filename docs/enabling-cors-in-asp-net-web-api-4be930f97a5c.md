# 在 ASP.NET Web API 中启用 CORS

> 原文：<https://medium.easyread.co/enabling-cors-in-asp-net-web-api-4be930f97a5c?source=collection_archive---------0----------------------->

## 如何在 ASP.NET Web API 中启用 CORS 的指南

# 简介:-

ASP。NET 是一个开发 web 应用程序的框架，它扩展了。NET 平台的工具和库。ASP.NET 的 WebAPI 是一种流行的技术。

每个人都试图使用 AJAX 请求或服务器端来访问服务。由于浏览器安全策略中的安全限制，您的 web 浏览器无法向另一个域中的服务器发出 AJAX 请求。这通常被称为“同源方案”

换句话说，内置的浏览器保护可以防止一个域的网页对另一个域进行 AJAX 调用。当一个 WebAPI 被托管，而另一个来自不同域的应用程序试图通过 AJAX 请求访问它时，问题就出现了。在这种情况下，在 WebAPI 中启用跨来源资源共享( [CORS](https://www.interviewbit.com/web-api-interview-questions/#cors-in-web-api) )至关重要。

CORS 是一种 W3C 标准，允许您绕过浏览器的同源策略，该策略限制从一个域访问属于另一个域的资源。使用适当的 Web API 包，您可以为您的 Web API 启用 CORS。

![](img/babedd38cea520e036611436330317b1.png)

方案、主机和端口号构成了请求的来源。如果两个请求具有相同的方案、主机和端口号，则认为它们来自同一个来源。如果所有这些都不相同，那么这些请求就被称为跨来源，这意味着它们不是来自同一个来源。

web API 可以帮助你创建一个基于 AJAX 的 ASP.NET 程序。web API 框架使得构建可以在各种实体上运行的服务变得简单。因此，web API 使得开发人员可以更容易地创建几乎可以在任何浏览器和计算机上运行的 ASP.NET 应用程序。Web APIs 让您可以访问 HTTP 的所有特性，比如 URIs、请求/响应头、内容格式化、缓存等等。

因此，通过 Web APIs 使用 RESTful web 服务构建 ASP.NET Web 应用程序要比使用 WCF(Windows Communication Foundation)rest 服务简单得多，后者需要为不同的设备指定额外的配置设置。

# *在 Web API 中启用 CORS 的方法:-*

## **使用 JSONP:-**

JSONP 是带填充的 JSON 的缩写。它有助于通过浏览器的同源策略实现跨域请求。它将一个 JSON 响应封装在一个 JavaScript 函数中，也就是回调函数)并作为脚本发送回浏览器。这允许您绕过同源策略，将 JSON 从外部服务器加载到网页上的 JavaScript 中。
**例如:-**
让我们假设我们有下面的 JSON:-

```
//JSON
{
 ‘rollNo’ : ‘1’,
 ‘name’ : ‘Vaibhav’,
 ‘Maths’ : ‘100’,
 ‘Physics’ : ‘66’,
 ‘Chemistry’ : ‘97’,
 ‘Biology’ : ‘88’
}
```

当服务器收到 JSONP 中的“callback”参数时，它以不同的方式包装结果，并像这样返回:-

```
//JSONP
newStudent({
 ‘rollNo’ : ‘1’,
 ‘name’ : ‘Vaibhav’,
 ‘Maths’ : ‘100’,
 ‘Physics’ : ‘66’,
 ‘Chemistry’ : ‘97’,
 ‘Biology’ : ‘88’
});
```

我们必须首先在 WebAPI 中允许 CORS，然后从另一个程序使用 AJAX 请求调用服务。要允许 CORS，我们需要从 NuGet 下载并安装 JSONP 包。我们需要安装软件包 **WebApiContrib。Jsonp** 为 ASP.NET Web API 提供了一个 JSONP MediaTypeFormatter 实现。

安装 Jsonp 包后，将以下代码添加到 App_StartWebApiConfig.cs 文件中:-

```
var FormatterJSONP = new JsonpMediaTypeFormatter(config.Formatters.JsonFormatter); 
config.Formatters.Add(FormatterJSONP);
```

它创建一个 JsonpMediaTypeFormatter 实例，并将其添加到 config formatters 对象中。
现在 CORS 已经在服务器中被激活，另一个应用程序必须发送 AJAX 请求到一个不属于我们的域。在下面的代码片段中，数据类型被设置为 jsonp，这与跨域请求兼容。

```
<script src=”[http://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3.min.js](http://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3.min.js)"></script> 
<script> 
 $(document).ready(function () { 
 $.ajax({ 
 type: ‘POST’, 
 url: ‘[http://localhost:3000/api/Recipes’](http://localhost:3000/api/Recipes'), 
 cache: ‘true’, 
 dataType: ‘jsonp’, 
 success: function (json) { 
 var pageContent = “”; 
 $.each(json, function (key, item) { 
 pageContent = pageContent + “<tr><td>” + item.Number + “</td><td>” + item.Name + “</td><td>” + item.Recipe + “</td></tr>”; 
 }); 
 $(‘#Recipes’).append(pageContent); 
 }, 
 error: function (parsedJSON, Status, thrownError) { 

 $(‘body’).append( 
 “Status of parsedJson : “ + parsedJSON.status + ‘</br>’ + 
 “errorStatus: “ + Status + ‘</br>’ + 
 “thrownError: “ + thrownError); 

 } 
 }); 
 }); 
</script>
```

**使用 JSONP 的缺点:-**

*   这涉及到很多安全问题，包括我们的 cookies 被盗。这是一个非常令人关注的问题。
*   旧浏览器不支持 JSONP。因此，它并不兼容所有的浏览器。

## 使用微软。WebApi.Cors:-

首先，我们需要安装微软。NuGet 包中的 AspNet.WebApi.Cors 包。为此，进入工具菜单= >库包管理器= >包管理器控制台，运行下面的命令:-
**Install-Package Microsoft。之后，我们将使用 EnableCorsAttribute 类来注册/启用 Cors，它有四个参数，其中最后一个是可选的。这四个参数如下**

*   **来源:-**
    这里，我们必须指定接受请求的来源或域。如果您有几个域，可以用逗号分隔它们。此外，如果您希望批准任何域请求，请使用“*”作为通配符。
*   **请求头:-**
    启用哪个请求头由请求头参数指定。将该值设置为“*”以启用任何标题。
*   **HTTP 方法:-**
    Methods 参数指定可以访问资源的 HTTP 方法。当使用多个 HTTP 方法时，如“get，put，post”，使用逗号分隔的值。使用通配符值“*”来启用所有 HTTP 方法。
*   **exposedHeaders:-**
    默认情况下，浏览器不会向应用程序显示所有的响应头。Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma 是默认情况下可访问的响应头。

您必须使用 exposedHeaders 在浏览器中显示其他标题。您可以使用下面的代码片段构建自定义标题:

```
[EnableCors(origins: “*”, headers: “*”, methods: “*”, exposedHeaders: “SampleHeader”)] 
public class SampleController : ApiController 
{ 
}
```

CORS 对 Web API 的支持可配置为三个级别:-

**全局级别:-**
我们将允许全局级别的 CORS，这意味着它将应用于所有的控制者及其操作。在 App Start/WebApiConfig.cs 文件中，添加以下代码片段。它使用传入的以下参数生成 EnableCorsAttribute 类的实例:-
**"**[**http://localhost:3000/sample app/form 1 . aspx**](http://localhost:3000/SampleApp/Form1.aspx)**"**
对于此域，服务器已启用 CORS。

```
public static void Register(HttpConfiguration config) 
{ 
 EnableCorsAttribute cors = new EnableCorsAttribute(“[http://localhost:3000/SampleApp/Form1.aspx](http://localhost:3000/SampleApp/Form1.aspx)", “*”, “GET,POST”); 
 config.EnableCors(cors); 
}
```

星号表示它支持所有请求头。
“GET，POST”:这意味着它只确认 GET 和 POST http 动词。如果服务器收到除“GET，POST”之外的请求，它会抛出一个异常。

**控制器级别:-**
我们可以在控制器级别允许 CORS，这意味着其中的所有操作都准备好为跨域请求服务。将 EnableCors 属性添加到控制器的顶部，并传递适当的参数(如上所述)。

```
 [EnableCors(origins: “[http://localhost:3000/SampleApp/Form1.aspx](http://localhost:3000/SampleApp/Form1.aspx)", headers: “*”, methods: “*”)] 
public class SampleController : ApiController 
{ 

}
```

**动作级别:-**
与控制器级别类似，我们可以在动作级别允许 CORS，这意味着 CORS 为准备好服务跨域请求的特定动作而激活。我们必须将 EnableCors 属性添加到操作的顶部，并传递适当的参数(如上所述)。

```
public class SampleController : ApiController 
{ 
 [EnableCors(origins: “[http://localhost:3000/SampleApp/Form1.aspx](http://localhost:3000/SampleApp/Form1.aspx)", headers: “*”, methods: “*”)] 

 public IEnumerable<string> Get() 
 { 
 return new string[] { “string1”, “string2” }; 
 } 
}
```

**在 WebAPI 1.0 中启用 CORS:-**
如果您正在使用 Web API 1.0，您需要修改 Global.asax 文件以包含以下代码。在本例中，我们使用应用程序 BeginRequest()事件来允许 CORS，它检查源名称，然后向响应对象添加头。

```
protected void Application_BeginRequest() 
{ 
 string[] origin_Allowed = new string[] { “[http://localhost:3001](http://localhost:3001)", “[http://localhost:3013](http://localhost:3013)" };
 var origin = HttpContext.Current.Request.Headers[“Origin”]; 
 if (origin != null && origin_Allowed.Contains(origin)) 
 { 
 HttpContext.Current.Response.AddHeader(“Access-Control-Allow-Origin”, origin); 
 HttpContext.Current.Response.AddHeader(“Access-Control-Allow-Methods”, “GET,POST”); 
 } 
}
```

从网络上启用 CORS。项目的配置文件:-

```
<system.webServer>
 <handlers>
 <remove name=”ExtensionlessUrlHandler-Integrated-4.0" />
 <remove name=”OPTIONSVerbHandler” />
 <remove name=”TRACEVerbHandler” />
 <add name=”ExtensionlessUrlHandler-Integrated-4.0" path=”*.” verb=”*” type=”System.Web.Handlers.TransferRequestHandler” preCondition=”integratedMode,runtimeVersionv4.0" />
 </handlers>
//Add this part of code in the file 
 <httpProtocol>
 <customHeaders>
 <add name=”Access-Control-Allow-Origin” value=”*” />
 <add name=”Access-Control-Allow-Credentials” value=”true”/>
 <add name=”Access-Control-Allow-Headers” value=”Content-Type” />
 <add name=”Access-Control-Allow-Methods” value=”GET, POST, PUT, DELETE, OPTIONS” />
 </customHeaders>
 </httpProtocol>
 <! — End of new addition →
 </system.webServer>
```

这将同时为项目中的所有控制器启用 CORS。

**在跨来源请求中传递凭据:-**

在 CORS 请求中，凭据必须以不同的方式处理。当发出跨来源请求时，默认情况下，浏览器不会提交任何凭据。Cookies 和 HTTP 认证系统就是凭证的例子。客户端必须将 XMLHttpRequest.withCredentials 设置为 true，才能通过跨来源请求提交凭据。
直接使用 XMLHttpRequest:

```
//C#
var temp = new XMLHttpRequest();
temp.open(‘get’, ‘[http://localhost:3001/api/sample'](http://localhost:3001/api/sample'));
temp.withCredentials = true;
```

```
//jQUERY
$.ajax({
 type: ‘get’,
 url: ‘[http://localhost:3001/api/sample'](http://localhost:3001/api/sample'),
 xhrFields: {
 withCredentials: true
 }
```

此外，凭据必须由服务器启用。将[EnableCors]特性的 SupportsCredentials 属性设置为 true，以允许 Web API 中的跨来源凭据:

```
//C#
[EnableCors(origins: “[http://localhost:3001/api/sample](http://localhost:3001/api/sample)", headers: “*”, 
 methods: “*”, SupportsCredentials = true)]
```

如果此属性有效，HTTP 响应中将包含 Access-Control-Allow-Credentials 标头。该头通知浏览器服务器接受跨来源凭据。如果响应不包含有效的 Access-Control-Allow-Credentials 头，浏览器不会向应用程序公开响应，AJAX 请求将失败。
应避免将 SupportsCredentials 设置为 true，因为这意味着不同域中的网站会在用户不知情的情况下代表他们将登录用户的凭据发送到您的 Web API。根据 CORS 规范，如果 SupportsCredentials 为 true，则将 origins 设置为“*”也是无效的。

**自定义 CORS 策略提供者:-**
ICorsPolicyProvider 接口由[EnableCors]标记实现。创建一个从属性派生并实现 ICorsPolicyProvider 的类，以拥有您自己的实现。
比如:-
【attribute usage(attribute targets。方法|属性目标。Class，AllowMultiple = false)]

```
//C#
public class CustomCORSPolicy : Attribute, ICorsPolicyProvider 
{
 private CorsPolicy myPolicy;public CustomCORSPolicy()
 {
 // Creating a CORS policy.
 myPolicy = new CorsPolicy
 {
 AllowAnyMethod = true,
 AllowAnyHeader = true
 };
// Adding allowed origins.
 myPolicy.Origins.Add(“[http://localhost:3001/](http://localhost:3001/)");
 myPolicy.Origins.Add(“[http://localhost:3013/](http://localhost:3013/)");
 }
public Task<CorsPolicy> GetCorsPolicyAsync(HttpRequestMessage request)
 {
 return Task.FromResult(myPolicy);
 }
}
```

现在，您可以在放置[EnableCors]的任何地方使用该属性。

```
[CustomCORSPolicy]
public class SampleController : ApiController
{
 …
```

您可以注册一个 ICorsPolicyProviderFactory 对象来创建 ICorsPolicyProvider 对象，而不是使用属性。

```
//C#
public class CustomCorsPolicyFactory : ICorsPolicyProviderFactory
{
 ICorsPolicyProvider providerCORS = new MyCorsPolicyProvider();
public ICorsPolicyProvider GETCorsPolicyProvider(HttpRequestMessage request)
 {
 return providerCORS;
 }
}
```

启动时调用 SetCorsPolicyProviderFactory 扩展方法来设置 ICorsPolicyProviderFactory:

```
public static class WebApiConfig
{
 public static void Register(HttpConfiguration configuration)
 {
 configuration.SetCorsPolicyProviderFactory(new CustomCorsPolicyFactory());
 configuration.EnableCors();// …
 }
}
```

## 飞行前请求:-

某些类型的请求，比如 DELETE 或 PUT，必须更进一步，在继续之前从服务器获得许可。浏览器使用预检请求来请求权限。

浏览器在实际请求之前发送一个微小的请求，称为预检请求。它包括诸如使用的 HTTP 方法以及是否存在任何自定义 HTTP 头之类的详细信息。预检允许服务器在发送之前看到实际请求的外观。然后，服务器将告诉浏览器是否提交请求，或者是否向客户端返回一个错误。

创建预检概念是为了在不破坏依赖于浏览器同源策略的现有服务器的情况下进行跨源请求。如果预检到达启用 CORS 的服务器，服务器将识别请求并做出适当的反应。

然而，如果预检到达不知道或不关心 CORS 的服务器，服务器将不会提交适当的预检响应，并且实际的请求将永远不会被提交。不受怀疑的服务器受到保护，不会处理它们不喜欢的跨来源请求。

预检请求有三个特征:它使用 HTTP 选项方法，它包括原始请求报头，并且它包括访问控制请求方法报头。

## **飞行前请求错误:-**

即使在 Web API 中正确配置了所有内容，当从脚本发出预检请求时，预检请求通常会返回 HTTP 错误代码 405。这可以通过对 web.config 和 Global.asax 文件进行一些更改来解决。

```
//Web.config file
<system.webServer>
 <handlers>
 <remove name=”ExtensionlessUrlHandler-Integrated-4.0" />
 <remove name=”OPTIONSVerbHandler” />
 <remove name=”TRACEVerbHandler” />
 <add name=”ExtensionlessUrlHandler-Integrated-4.0" path=”*.” verb=”*” type=”System.Web.Handlers.TransferRequestHandler” preCondition=”integratedMode,runtimeVersionv4.0" />
 <! — Add this line of code → 
 <add name=”OPTIONSVerbHandler” path=”*” verb=”OPTIONS” modules=”ProtocolSupportModule” requireAccess=”None” responseBufferLimit=”4194304" /> 
 <! — End of new addition → 
 </handlers>
 <httpProtocol>
 <customHeaders>
 <add name=”Access-Control-Allow-Origin” value=”*” />
 <add name=”Access-Control-Allow-Credentials” value=”true”/>
 <add name=”Access-Control-Allow-Headers” value=”Content-Type” />
 <add name=”Access-Control-Allow-Methods” value=”GET, POST, PUT, DELETE, OPTIONS” />
 </customHeaders>
 </httpProtocol>
 </system.webServer>
```

```
// Global.asax
public class WebApiApplication : System.Web.HttpApplication
 {
 protected void Application_Start()
 {
 AreaRegistration.RegisterAllAreas();
 GlobalConfiguration.Configure(WebApiConfig.Register);
 FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
 RouteConfig.RegisterRoutes(RouteTable.Routes);
 BundleConfig.RegisterBundles(BundleTable.Bundles);
 }
<! — Added code →
 protected void Application_BeginRequest(object Sender, EventArgs eventE)
 {
 if (HttpContext.Current.Request.HttpMethod == “OPTIONS”)
 {
 HttpContext.Current.Response.Flush();
 }
 }
 <! — End of added code →
 }
```

## 禁用 CORS:-

如果在全局层或控制器层启用了 CORS，则所有活动都将启用 CORS。但是，如果出于安全原因想要禁用 CORS 一些行为，disable CORS 属性就很方便了。它禁用 CORS，这意味着其他域将无法调用该操作。

```
[DisableCors()] 
public string Get(int id) 
{ 
 return “string”; 
}
```

CORS 是一个服务器端应用程序，与浏览器协同工作。因此，也需要浏览器支持 CORS。

# 结论:-

Web API 是一个用于 ASP.NET 开发的可扩展平台，它从服务器为我们提供信息。它完全建立在 HTTP 协议之上，以 RESTful 方式描述、公开和使用都很简单。

在 the.NET 框架上，它被认为是创建 RESTful 应用程序的理想论坛。CORS(跨源资源共享)是一种协议，允许用户在浏览器中从一个网站向另一个网站发出请求，这通常被另一种称为 SOP(同源策略)的法规所禁止。

它允许客户端或浏览器向服务器发送安全的跨源请求和数据。来自不同背景的请求被称为跨来源请求。对于 JavaScript，CORS 只是解决了同源约束。可以使用适当的 web API 工具包或 OWIN 中间件来允许 web API 的 CORS。

在本文中，我们看到了支持 CORS 的不同方式。我们提出了两种允许 CORS 的方法:JSONP 和 Microsoft Cors kit。出于保护目的，微软 Cors 的浏览器不兼容性战胜了 JSONP。