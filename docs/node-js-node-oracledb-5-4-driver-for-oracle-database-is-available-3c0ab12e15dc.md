# node . js node-Oracle 数据库的 oracledb 5.4 驱动程序可用

> 原文：<https://medium.com/oracledevs/node-js-node-oracledb-5-4-driver-for-oracle-database-is-available-3c0ab12e15dc?source=collection_archive---------0----------------------->

**发布公告**:node-Oracle db 的新版本，用于访问 Oracle 数据库的 Node.js 和 TypeScript 模块，可从 [npm](https://www.npmjs.com/package/oracledb) 获得。

![](img/9ee8bcedf71a18461dc10a5714403854.png)

照片由[戴加·埃拉比](https://unsplash.com/@daiga_ellaby?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/plant?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

**顶级特性:Oracle 身份&访问管理令牌认证**

*   对 IAM 令牌认证的新支持允许 Oracle 云基础架构(OCI)的用户使用[认证令牌](https://oracle.github.io/node-oracledb/doc/api.html#tokenbasedauth)连接到 Oracle 数据库，而不需要在连接或池创建时提供用户名和密码。
*   添加了一个新方法[connection . is health()](https://oracle.github.io/node-oracledb/doc/api.html#ishealthy)。它与现有的 [connection.ping()](https://oracle.github.io/node-oracledb/doc/api.html#connectionping) 调用有一些相似之处，但是它是同步的，这使得它适合在诸如没有为异步调用编码的框架之类的地方使用。因为它是同步的，所以它不会对数据库做一次完整的往返，并且它不会捕获某些类型的连接失败。然而，不进行往返确实有助于应用程序的可伸缩性，因此每个调用都有其用途。和往常一样，我鼓励您在语句执行后做好错误检查，因为网络可能会在任何时候中断，甚至在成功的 ping 或健康检查之后。
*   几个方便的 pull 请求，特别是改进异步/await 风格调用的错误堆栈的请求来自 saw omir Osoba——非常感谢。

查看[变更日志](https://github.com/oracle/node-oracledb/blob/main/CHANGELOG.md)了解所有变更和错误修复。

# 安装或升级节点-oracledb

您可以通过更新 package.json 要求来安装或升级 node-oracle:

```
"dependencies": {
   "oracledb": "^5.4"
},
```

# 资源

*   Node-oracledb 安装说明在这里是[这里是](https://oracle.github.io/node-oracledb/INSTALL.html)。
*   Node-oracledb 文档在这里是。
*   Node-oracledb 变更日志这里是[这里是](https://github.com/oracle/node-oracledb/blob/main/CHANGELOG.md)。
*   关于 node-oracledb 的问题可以发布在 [GitHub](https://github.com/oracle/node-oracledb/issues) 或者 [Slack](https://node-oracledb.slack.com/) ( [链接加入 Slack](https://join.slack.com/t/node-oracledb/shared_invite/enQtNDU4Mjc2NzM5OTA2LWMzY2ZlZDY5MDdlMGZiMGRkY2IzYjI5OGU4YTEzZWM5YjQ3ODUzMjcxNWQyNzE4MzM5YjNkYjVmNDk5OWU5NDM) )。
*   在推特[或脸书](https://twitter.com/ghrd)上关注我们。

最后，非常欢迎对 node-oracledb 的贡献，参见[贡献](https://github.com/oracle/node-oracledb/blob/main/CONTRIBUTING.md)。