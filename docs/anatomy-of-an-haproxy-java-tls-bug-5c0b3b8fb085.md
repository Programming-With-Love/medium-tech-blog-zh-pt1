# 剖析一个 HAProxy Java TLS bug

> 原文：<https://medium.com/square-corner-blog/anatomy-of-an-haproxy-java-tls-bug-5c0b3b8fb085?source=collection_archive---------4----------------------->

*TL；DR: Java 严格解释 TLS 规范，当连接被不干净地关闭时，不允许会话恢复。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

在将基于 HAProxy 的内部负载平衡项目部署到包含数百个服务和后端的配置的暂存环境中时，HAProxy 立即将 CPU 使用率确定为 100%。由于还没有流量通过 HAProxy，唯一可能的原因是对每个服务后端进行的健康检查。

Square 使用 TLS 进行所有服务到服务的通信，包括健康检查，因此 HAProxy 被配置为使用 SSL，而不进行证书验证。假设 CPU 使用高峰是由于不断创建到后端服务器的新连接的成本，则创建了一个补丁来添加对 HAProxy 的健康检查持久连接的支持。这解决了问题，但由于 HAProxy 是开源的，让上游集成补丁将是最好的解决方案。

一旦提交到邮件列表，HAProxy 的创建者 Willy Tarreau 指出，涉及 CPU 密集型密钥生成的新 TLS 会话应该只在初始连接上创建，后续连接应该使用[会话恢复](https://hpbn.co/transport-layer-security-tls/#tls-session-resumption)。灯泡，书桌，废话。

幸运的是，HAProxy 公开了一个 stats 套接字，其中包含了后端每秒交换的 SSL 密钥数量。

```
socat -t120 ./stats.sock stdio <<< “show info” | grep SslBackendKeyRate
```

持续超过 300 次，这意味着 HAProxy 每秒钟要建立 300 次新的(昂贵的)SSL 会话。由于 HAProxy 是一个单线程，事件驱动的服务器，这是饱和的 CPU，它甚至很难返回统计信息。

通过慢慢地将配置文件缩减到最低限度并监控`SslBackendKeyRate`，最终发现问题只影响 Java 服务。

使用 tcpdump 和 Wireshark，对 HAProxy 和后端之间的 SSL 流量进行了快照:

```
sudo tcpdump -w out -i any -s 1600 ‘(tcp[((tcp[12:1] & 0xf0) >> 2):1] = 0x16)’In Wireshark: Analyze -> Decode As -> SSL for the desired ports decodes the unencrypted SSL packet information.
```

在数据包转储中，似乎 HAProxy 将从后端接收一个会话 ID，并在下一个连接中重用它，但后端仍会坚持为新连接进行完整的密钥交换。

OpenSSL 客户端能够确认会话恢复仍然在后端正常工作:

```
echo “Q” | openssl s_client -connect {HOST}:{PORT} -reconnect | grep Session-ID
```

最后，一旦制造出一个小的[复制案例](https://github.com/steved/haproxy-java-ssl-check)，就找到了确凿的证据。

通过启用 Java 的 SSL 调试(`-Djavax.net.debug=all`)，日志显示当连接关闭时会话是无效的。

```
qtp1952751122–12, fatal error: 80: Inbound closed before receiving peer’s close_notify: possible truncation attack?javax.net.ssl.SSLException: Inbound closed before receiving peer’s close_notify: possible truncation attack?
%% Invalidated: [Session-2, TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384]
```

查看 HAProxy 的健康检查代码，有几处连接被关闭，但最值得注意的是这几行:

```
/* Close the connection.. We absolutely want to perform a hard close
* and reset the connection if data is pending, otherwise we end
* up with many TIME_WAITs and eat all the source port range quickly.
* To avoid sending RSTs all the time, we first try to drain pending
* data.
*/
__conn_data_stop_both(conn);
conn_data_shutw_hard(conn);
```

`conn_data_shutw_hard`依次调用 SSL 会话上的 shutw 函数，并设置不干净标志:

```
if (!clean)
        /* don't sent notify on SSL_shutdown */
        SSL_set_quiet_shutdown(conn->xprt_ctx, 1);
```

[SSL_set_quiet_shutdown](https://wiki.openssl.org/index.php/Manual:SSL_CTX_set_quiet_shutdown(3)) 设置一个标志，当 SSL 会话关闭时，不会向服务器发送“关闭通知”数据包。

这种行为在 [TLS 1.1 规范](https://tools.ietf.org/html/rfc5246#section-7.2.1)中变得有效:

```
close_notifyThis message notifies the recipient that the sender will not send
any more messages on this connection. Note that as of TLS 1.1,
failure to properly close a connection no longer requires that a
session not be resumed. This is a change from TLS 1.0 to conform
with widespread implementation practice.
```

在来自 Java 的日志消息中，指出 SSL 会话无效是因为 2013 年发现的针对 TLS 的攻击，称为“[截断攻击](https://en.wikipedia.org/wiki/Transport_Layer_Security#Truncation_attack)”Java 通过要求完整的 TLS 关闭序列来允许会话恢复，从而缓解了这一问题。

最终，补丁本质上是五个字符的改变:

通过至少尝试干净地关闭 SSL 会话，将(几乎)总是发送“关闭通知”,并且可以干净地恢复会话。CPU 使用率降至正常水平。该修补程序已被接受并在 1.7.4 版中发布。

你可以在这里找到完整的线索:

[https://www . mail-archive . com/ha proxy @ formi lux . org/msg 25078 . html](https://www.mail-archive.com/haproxy@formilux.org/msg25078.html)