# 在 Oracle 移动云中验证 JSON

> 原文：<https://medium.com/oracledevs/validating-json-in-mcs-bc98ccaeec0d?source=collection_archive---------0----------------------->

有一天，我和一个客户一起工作，解决一个 API 的问题，当它被一个 MAX 应用程序访问时，似乎不工作了。当他在 MCS (Oracle 移动云服务)中测试 API 时，一切都很好。当他从 MAX 访问 API 调用时，列表控件为空，显示消息“没有找到数据”。

幸运的是，这位客户注意到 API 返回的 JSON 和模式指定的 JSON 格式之间有一点差异。API 返回的 JSON 值的模式不同于实际的返回值，这就是问题的根源。

# MCS 不会验证您的数据

默认情况下，MCS 不会验证您通过它传递的任何数据。开发人员通常认为，既然 MCS 允许您指定模式，或者参数的限制、模式或范围，那么 MCS 就是在验证这些值。事实并非如此。您可以通过测试 HelloValidation API 来了解这一点，该 API 在简单的问候请求中指定了一个 4 位数。请使用 2 位数，MCS 将很乐意为您提供示例数据。缺乏自动验证有两个主要原因:
1)验证需要时间，会降低服务响应时间。
2)如果您创建一个 REST 服务，它可以接受多种请求类型并提供多种响应类型( *application/json* 、*application/x-www-form-urlencoded*或 *multipart/form-data* )，那么自动验证有效负载几乎是不可能的。

然而，作为开发人员，我们知道在创建新服务时，较慢的响应时间是值得的。在开发阶段，我们需要了解服务是如何运行的，而不是执行速度。幸运的是，MCS 和 Node.js 确实允许验证，并且您可以在您的自定义代码中打开或关闭它。

# 向 MCS 代码添加验证

向代码中添加验证需要几个步骤。你首先需要的是你的 API。我已经为我的示例 API 创建了一个 RAML 文件，您可以使用它来创建与我在这个博客条目中使用的服务相同的服务。我将该 API 命名为 HelloValidation。

> #%RAML 0.8
> 标题:HelloValidation
> 版本:1.0
> base uri:/mobile/custom/hello validation
> 协议:【HTTPS】
> schemas:
> —Simple Greeting:|
> {
> " $ schema ":[http://json-schema.org/draft-04/schema#](http://json-schema.org/draft-04/schema#)"，
> "definitions": {}，
> " id ":""，
> " properties ":{
> " Simple Greeting 已验证的问候语
> 
> 协议:[HTTPS]
> 查询参数:
> id:
> 显示名称:用户 ID
> 描述:|
> 用户的唯一 ID
> 
> 类型:编号
> 最小值:1000
> 最大值:9999
> 示例:|
> 1234
> 
> 必需:true
> 响应:
> 200:

现在您需要一个 JSON 验证器包。有好几个，但是我选择了 npm 的“jsonschema”包，因为它看起来很容易使用。请注意，我并没有对所有可用的 JSON 验证器进行详尽(甚至粗略)的检查，所以我对 jsonschema 的使用并不是一种认可，只是一个方便的例子。请随意用您选择的验证器进行替换。

从 MCS 下载 HelloValidation 服务的 javascript 框架，并将其解压缩到本地硬盘上进行编辑。

打开一个命令窗口，导航到包含 HelloValidation 服务的目录。

键入以下命令:
*NPM install jsonschemma*

安装完成后，您将看到 node_modules 目录。我们现在需要让您的代码知道这个包的存在。打开 package.json 文件和 package-lock.json 文件并比较它们。更新 package.json 文件，使其看起来像下面这样(假设 jsonschema 的版本号在我写这篇文章之后没有改变)。

> {
> "name": "hellovalidation "，
> "version": "1.0 "，
> "description ":"向调用方提供经过验证的问候语"，
> "main": "hellovalidation.js "，
> " Oracle mobile ":{
> " dependencies ":{
> " API ":{ }，
> " connectors ":{ }
> }
> }，
> "dependencies "

接下来，您需要修改每个 GET 方法的代码，以提供经过验证的响应。我们还将使用此代码来发送无效的请求和响应。打开 *hellovalidation.js* 文件。我们要做的第一件事是为 *simplevalidation* 的 GET 方法的 *{id}* 查询参数添加一些验证。这是完成的函数，我将仔细阅读更有趣的代码行来解释到底发生了什么。

> module . exports = function(service){
> var validation active = true；
> 
> /**
> *打包该文件的归档中的 samples.txt 文件包含一些示例代码。
> */
> 
> service . Get('/mobile/custom/hello validation/simple greeting '，function(req，res) {
> //以数字形式获取“id”查询参数。默认为字符串
> var id = parse int(req . query . id)；
> 
> if(validationActive) {
> //初始化我们的验证器对象
> var Validator = require(' JSON schema ')。验证器；
> var v = new Validator()；
> 
> //为 1000 到 9999 之间的数值创建一个模式
> var id schema = { " type ":" number "，" minimum": 1000，" maximum ":9999 }；
> 
> console.info("用值:"+ id "验证 id)；
> //验证查询参数
> var vRes = v.validate(id，id schema)；
> if(vres . errors . length>0){
> //ID 出错。处理它
> console . info(" bad ID:"+ID)；
> } else {
> //验证通过。
> console.info("好 ID:"+ID)；
> }
> console . info(vRes)；//将验证结果写入日志。
> }
> 
> //创建我们的结果对象
> var result = { " simple greeting ":" Hello John " }；
> 
> if(validationActive) {
> //现在创建我们的结果模式
> var result schema = {
> " $ schema ":[http://json-schema.org/draft-04/schema#](http://json-schema.org/draft-04/schema#)"，
> "定义":{}，
> " id ":"[http://oracle.com/simplegreeting](http://oracle.com/simplegreeting)"，
> " properties ":{
> " simple greeting ":{
> " id ":"/properties/simple greeting "，
> "type": "string"
> }。
> }
> 
> var statusCode = 200//成功
> res.status(statusCode)。发送(结果)；
> })；
> }；

注意靠近代码顶部的一行:
*var validation active = true；*
这是我们打开和关闭验证的开关。当您将代码转移到产品中时，这允许您快速而轻松地关闭验证。当然，您可能希望在生产中保持验证打开，尤其是当您的服务被合作伙伴或公众调用时。它们完全有可能无意或故意发送错误的查询参数或有效负载。

很自然，在生产代码中，您会希望将错误发送回调用者，无论是在响应的头部还是主体中。这个例子并不是为了展示编码的最佳实践，只是为了展示将 JSON 验证添加到您的项目中是多么容易