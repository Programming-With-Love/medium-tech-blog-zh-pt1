# 基于 RFC-6902 的对 JSON 对象的 HTTP 补丁操作

> 原文：<https://medium.com/globant/rfc-6902-based-http-patch-operation-on-json-objects-4babab66079c?source=collection_archive---------0----------------------->

![](img/e60fd6b71ba16d665db7c7ff65dc8116.png)

Photo by [gustavo Campos](https://unsplash.com/@gustavocpo?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**简介:**
*超文本传输协议(HTTP)* 定义了一组请求方法，也称为 *HTTP 动词*，用于指示要对给定资源执行的预期操作。一些最常见的方法有`[GET](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET)`、`[PUT](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PUT)`、`[POST](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST)`和`[DELETE](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/DELETE)`。虽然大多数软件工程师都很清楚这四种方法的特点，但有时我们需要超越，比如使用`[PATCH](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH)`、`[HEAD](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/HEAD)`等方法。

**HTTP 补丁操作:** HTTP`PUT`方法是用一个完整的新体覆盖一个资源，不能用来做局部的改动。为了克服这个限制并对资源进行部分修改，HTTP `PATCH`请求方法被引入到 [RFC 5789](https://www.rfc-editor.org/rfc/rfc5789) 中。

HTTP `PATCH`自动执行请求的更改。这意味着如果服务器不能满足所有请求的更改，它不会修改目标实体。

**PUT vs PATCH 困境:**
根据定义，`PUT`方法用请求负载替换目标资源的所有当前表示，而`PATCH`方法对资源应用部分修改。可以理解的是，`PUT`和`PATCH`都有点类似于 [CRUD](https://developer.mozilla.org/en-US/docs/Glossary/CRUD) 中的 ***update*** 概念，但主要区别在于:`PATCH`请求被认为是一组关于如何修改资源的 ***指令*** 。这与`PUT`形成对比；这是一个资源的完整 ***表示*** 。

**RFC-6902:**
互联网上有无数例子说明如何以简单的方式使用`PATCH`操作。但是特别地，当我们谈论修改 JSON 资源时，我们经常忽略下面的事实:

> 然而，对于`*PATCH*`，封闭的实体**包含一组指令**，描述当前驻留在原始服务器上的资源应该如何被修改以产生新版本。

简而言之，RFC-5789 指出，在请求体中，我们需要指定一组操作或指令来修改当前资源。那么我们如何在请求体中设置动作呢？应该怎么格式化？

这正是 RFC-6902 来救援的地方。该规范讨论了如何使用 **JSON 补丁**格式发送 ***指令集*** 以对资源进行部分更新。JSON 补丁是一种格式，用于表达通过 HTTP `PATCH`方法应用于目标 JSON 文档的*操作*的序列或数组。

请求体中的每个操作对象由 3 个主要属性组成:
【0】**—表示要执行的操作。根据规范，它的值必须是“*添加*”、“*移除*”、“*替换*”、“*移动*”、“*复制*”或“*测试*”、
【1】**路径** —包含引用执行操作的“目标位置”
【2】**的 JSON 指针值的字符串之一****

**考虑下面简单的 JSON:**

```
GET marvel/avengers/1
{
 "name": "Tony Stark",
 "address": "Malibu Point"
}
```

**假设我们想通过`PATCH`操作改变地址属性。通常，我们会发送如下的 HTTP `PATCH`请求:**

```
PATCH marvel/avengers/1
{
 "address": "Stark Tower LA",
 "addressType": "office"
}
```

**但是在上面的请求中，我们没有指定指令。如果我们想根据 RFC-6902 发送请求，`PATCH`请求将如下所示:**

```
PATCH marvel/avengers/1
[
 {
  "op": "replace",
  "path: "/address",
  "value": "Stark Tower LA"
 },
 {
  "op": "add",
  "path: "/addressType",
  "value": "office"
 },
]
```

**考虑到上述操作是成功的，我们将得到如下修改后的对象:**

```
GET marvel/avengers/1
{
 "name": "Tony Stark",
 "address": "Stark Tower LA",
 "addressType": "office"
}
```

****结论:** RFC-6902 声明:**

> **json 补丁是一种格式(由媒体类型“application/ json-patch+json”标识)，用于表达应用于目标 JSON 文档的操作序列；**适合与 HTTP 补丁方法**一起使用。**

**因此，RFC-5892 和 RFC-6902 之间的相关性可以用简单的话来理解，即我们可以使用 JSON-PATCH 作为 HTTP-PATCH 请求方法上的请求体。**

**此外，使用 JSON-PATCH 的优势可以在
-我们必须处理具有深层属性层次结构的复杂 JSON 结构
-我们需要限制用户并只允许修补选择性字段时观察到**