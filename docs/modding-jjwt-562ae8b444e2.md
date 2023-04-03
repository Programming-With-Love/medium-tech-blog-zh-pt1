# 正在修改 JJWT

> 原文：<https://medium.com/walmartglobaltech/modding-jjwt-562ae8b444e2?source=collection_archive---------2----------------------->

![](img/a23562f56f52f6f7cd0bd08092bec451.png)

Credit : [Pixabay](https://pixabay.com/en/computer-security-padlock-hacker-1591018/)

**JSON Web 令牌:**

根据 *RFC#7519* 的说法，JSON Web Token (JWT)是一种简洁的、URL 安全的方式来表示在双方之间传输的声明。JWT 中的声明被编码为 JSON 对象，该对象被用作 JSON Web 签名(JWS)结构的有效载荷，或者被用作 JSON Web 加密(JWE)结构的明文，从而能够对声明进行数字签名或使用消息认证码(MAC)进行完整性保护和/或加密。

JWT 包含三个部分，以特定的顺序附加。

1.标头—包含有关令牌本身的元数据，例如:令牌类型、使用的签名算法(未签名时为*【无】*)

2.声明集/有效负载—JWT 声明集代表一个 JSON 对象，其成员是由 JWT 传达的声明。

3.签名—包含使用标题中提到的算法的标题和声明集的签名。

建筑:

*H* : urlBase64(表头)

P : urlBase64(有效载荷)

*S* : urlBase64(签名(H ||。|| P)) (||意味着连接)

Token : *H ||。|| P ||。|| S*

令牌的签名部分是可选的，但它需要有 3 个由“句号”连接的部分。

**基于令牌的认证**

对于无状态服务，认证是通过使用令牌来完成的。这些令牌必须包含足够的信息来验证请求发送者的身份。由于这些是代币，我们还需要验证发行者的真实性。令牌必须是这样的，即没有人能够伪造它们，并且只有预定方能够验证它。

令牌可以包含权限字段，以确定对网页或应用程序上的资源的访问。可能是“用户”或“管理员”。这些令牌并不意味着存储任何敏感信息，因为它们只是 base64 编码的 JSONs。(尽管它们可以选择性地使用 JWE 标准加密)。因此很容易创建有效载荷。但是，一个人不能伪造一个有效载荷。这由令牌的签名部分负责。

要获得令牌，用户首先需要提供其用户凭据。经过验证后，服务返回一个令牌，在使用其非对称密钥签名的有效负载中包含相应的身份验证和授权信息。这些令牌通常包含发行者的标识和它所针对的受众的标识。建议这些令牌是短期的。

**用例:**

开发 RESTful 服务是为了以加密实体的形式存储敏感信息。它支持实体的持久性，可由授权用户检索/修改。为了处理授权，需要在请求中提供由非对称密钥签名的 JWT 令牌，该非对称密钥由客户端提供或者由服务在注册时生成。该令牌被验证为对请求进行身份验证的一种方式。

**问题:**

为了构建 JWT 令牌，我们使用了规范的 java 实现——JJWT。我们构建一个令牌如下:

```
String compactJws = Jwts.builder() .setSubject(“Joe”) .signWith(SignatureAlgorithm.HS512, key) .compact();
```

验证令牌`compactJws`如下:

```
try { Claims claims = Jwts.parser() .setSigningKey(key) .parseClaimsJws(compactJws) .getBody(); //OK, we can trust this JWT} catch (SignatureException e) { //don't trust the JWT!}
```

该构建器界面提供了一个`signWith(Algorithm, key)`函数，该函数从库中定义的一组预定义和支持的算法中接受一个算法，并提供一个**清除**键进行签名。PCI DSS 3.0 指南指出:

> 3.5.1:“将对密钥的访问权限限制在最少数量的必要保管人”
> 
> 3 . 5 . 4:“**将密钥存储在尽可能少的位置**

此外，该应用程序旨在将角色分离到不同的层中。因此，我们应该拥有的只是提供令牌的业务逻辑中实际密钥的句柄。加密任务应该被隔离到一个加密层中，即业务逻辑不能与密钥本身有关。

类似地，为了验证，它提供了`setSigningKey(Key)`从头部提取算法并进行验证。同样，这把钥匙也需要弄清楚。

当前的问题是，我们如何使用 JJWT 仅仅用实际密钥的句柄签名，因为 JJWT 库有自己的提供者，使用 JCE 接口进行加密任务，并有特定的接口来接受输入。

在开发时，该库不支持将加密委托给不同服务的算法和提供程序的自定义实现，并且没有时间表，该库必须修改。

## 解决方案:

构建器和解析器接口需要修改。两个界面都引入了新的方法。

```
JwtBuilder setCryptoProvider(CryptoProvider cryptoProvider);JwtBuilder setConfigParams(Map<String, Object> config);
```

第一个方法允许库接受用户特定的自定义加密提供程序，并且可以包含任何逻辑，直到它实现库中添加的加密提供程序接口。

`cryptoProvider`界面–

它包含两种用于令牌构建和验证的基本方法。

```
/**** **@param** plain the string that needs to be signed* **@param** config configuration hashmap that requires exclusive information required for sign* This config map can be implementation specific as the user will have to implement the CryptoProvider and provide* an instance for using jjwt.* **@return** String the signed value as a String** Parsing of the config map is left for the implementation*/**public** String sign(String plain, Map<String, Object> config)**throws** SignatureException;/**** **@param** plain the string that has been signed* **@param** sign the sign that is to be verified for the given plain text* **@param** config configuration hashmap that requires exclusive information required for verify* This config map can be implementation specific as the user will have to implement the CryptoProvider and provide* an instance for using jjwt.* **@return** Boolean value identifying if the sign is valid or not*/**public** Boolean verify(String plain, String sign,Map<String, Object> config) **throws** SignatureException;
```

在添加到接口的第二个方法中传递的配置参数包含用户特定的信息。这是为了支持加密函数的用户特定实现。因此，它对库是不透明的。这些配置图可以盲目的传递给`cryptoProvider`的实现。如何使用它取决于实现类。它可以实现自己的令牌签名协议，将其委托给另一个服务或 HSM。所有需要的元数据都可以存储在配置映射中。

为了实际使用这些添加的函数，必须在库中更改代码，以便在`cryptoProvider`和配置都被显式设置时覆盖库的签名/验证调用。

因此，通过提供 CryptoProvider 实现和相关配置，默认行为可以委托给应用程序自己的实现，覆盖 JJWT 的 JCE 实现。

```
String token = Jwts.*builder*().setId(ID) .setIssuedAt(now) .setExpiration(exipration) .setCryptoProvider(cryptoProvider) .setConfigParams(config).compact();
```

config 是一个`Map<String, Object>()`和`cryptoProvider`是一个从库中实现`CryptoProvider`接口的类的实例。

类似的验证工作如下:

```
claims = Jwts.*parser*() .setCryptoProvider(cryptoProvider) .setConfigParams(config) .parseClaimsJws(token).getBody();
```

通过这种设计变更，可以抽象出加密责任，而不会影响向后兼容性。

链接:[https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)

修改版本:[https://github.com/prateeknischal/jjwt](https://github.com/prateeknischal/jjwt)