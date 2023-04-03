# 驯服双向 SSL 认证

> 原文：<https://medium.com/walmartglobaltech/taming-the-two-way-ssl-authentication-38b619a1c32a?source=collection_archive---------1----------------------->

![](img/52c93b5c711a562fc83db9cac3ac9b0d.png)

PeteLinforth, Public Domain ([https://pixabay.com/en/background-vault-render-3d-steel-1242666/](https://pixabay.com/en/background-vault-render-3d-steel-1242666/))

在沃尔玛基于 SOA 的架构中，使用单向 SSL 的服务到服务交互(其中客户端验证服务器是安全的)相当常见，并且是 RESTful 通信中的规范。在涉及 PCI 或 HIPAA 数据的情况下，存在双向 SSL 认证，其中服务器现在验证客户端是否已知。这种情况并不常见，第一次设置时会很有挑战性。我下面提供的大部分信息都可以在网上找到，但是要把它们拼凑起来需要一些时间。因此，我提供了一个要点，以及如果你使用一个 **Java** **Spring** 和 **JaxRS** (基于 REST 服务的 Apache 框架)设置需要做什么。

**双向 SSL 握手快速入门**

1.  初始连接建立
2.  接下来是与客户端协商密码套件，客户端提供一个受支持的密码套件列表供服务器选择。
3.  服务器选择一个密码组并发送带有公钥的服务器证书，在双向 SSL 中，它请求一个由它信任的 CA 列表中的任何一个签名的证书。
4.  客户端根据其信任存储验证服务器证书，并发送其自己的证书，该证书已由可信机构之一签名。
5.  服务器验证证书并确认。
6.  客户端发送一个秘密会话密钥，用于进一步对称加密数据。

**建立信任库和密钥库**

1.  确保您拥有带有私钥的客户端证书。有各种文件格式和在它们之间转换的方法——但是 Java 理解 JKS & PKCS12。 ***双向 SSL 认证*** 需要此私钥存储配置。一种方法是 SSL auth 只查看您的信任存储。
2.  确保将服务器的证书或其中间或根 CA 添加到您的信任库中。信任库是您所连接和信任的服务器的所有证书的存储库。
3.  一旦完成以上工作，它就可以集成到您的应用程序
    中了，如果您使用的是 Spring 和 Apache-JaxRS，可以在 pom.xml 中添加以下依赖项，并将管道配置添加到 Spring beans 配置中。这里的关键是使用一个 **JaxRS HTTP 管道**——它本质上定义了套接字连接。

> 提示:您可以通过使用浏览器的高级设置将您的客户端证书导入浏览器来检查您的浏览器 SSL 连接是否正常。在输入安全服务器 URL 时，如果握手成功，您将获得一个有效的 HTTP 成功或错误代码。但是，如果您的客户端证书有问题，浏览器发出的 SSL 握手将失败，您将得到一个浏览器错误。

**Maven 依赖关系**

```
<dependency>
 <groupId>org.apache.cxf</groupId>
 <artifactId>cxf-common-schemas</artifactId>
 <version>2.3.11</version>
</dependency>
<dependency>
  <groupId>org.apache.cxf</groupId>
  <artifactId>cxf-rt-transports-http</artifactId>      <version>2.7.7</version>
</dependency>
```

【JaxRS 连接的弹簧配置

```
//Bean initialization
//Schema Declaration 
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<beans ae hu" href="http://www.springframework.org/schema/beans" rel="noopener ugc nofollow" target="_blank">http://www.springframework.org/schema/beans"
xmlns:http="[http://cxf.apache.org/transports/http/configuration](http://cxf.apache.org/transports/http/configuration)"
xmlns:sec="[http://cxf.apache.org/configuration/security](http://cxf.apache.org/configuration/security)"
xsi:schemaLocation="[http://cxf.apache.org/configuration/security](http://cxf.apache.org/configuration/security) [http://cxf.apache.org/schemas/configuration/security.xsd](http://cxf.apache.org/schemas/configuration/security.xsd)">
//Conduit wiring
<http:conduit name="${serviceURL}.*">
<http:tlsClientParameters secureSocketProtocol="TLS" >**<sec:certAlias>myalias</sec:certAlias>**
<sec:keyManagers keyPassword="password" >    
<sec:keyStore type="JKS" password="Keystore_password" file="${file_path_to_keystore}"/>
</sec:keyManagers>
<sec:trustManagers>
<sec:keyStore type="JKS" password="trust_store_password" file="${file_path_truststore}"/>
</sec:trustManagers>
<sec:cipherSuitesFilter>
**<sec:exclude>.*_EXPORT_.*</sec:exclude>
<sec:exclude>.*_EXPORT1024_.*</sec:exclude>**<sec:include>.*_WITH_DES_.*</sec:include>
<sec:include>.*_WITH_AES_.*</sec:include>**<sec:exclude>.*_WITH_NULL_.*</sec:exclude>
<sec:exclude>.*_DH_anon_.*</sec:exclude>**
</sec:cipherSuitesFilter>
</http:tlsClientParameters>
<http:client AutoRedirect="true" Connection="Keep-Alive" CacheControl="no-cache"/>
</http:conduit>
```

**要注意的事情**

1.  JaxRS 为每个连接创建一个管道，默认情况下，如果您将 javax.net.ssl.keyStore 属性指定为系统属性，它不会查看该属性。
2.  如果没有从类路径中选择管道配置，那么在使用 JaxRS 创建 Web 客户机时，可以将该位置作为文件路径配置参数传递。
3.  确保别名条目正确，并且在密钥库中指定。
4.  [*导出密码容易受到 SSL 怪胎攻击*](https://blog.cryptographyengineering.com/2015/03/03/attack-of-week-freak-or-factoring-nsa/) ，应该排除。
5.  AES 和 DES 的实现很弱，因此最好使用此 [*网站*](https://www.ssllabs.com/ssltest/) 进行审查。
6.  建议使用强密码和密码加密服务。

希望这篇文章能帮助你节省时间，如果你正试图建立双向 SSL 通信的话！