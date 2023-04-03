# 在 Fluentd 配置中保护凭据

> 原文：<https://medium.com/oracledevs/securing-credentials-in-fluentd-configurations-c1971531d7e2?source=collection_archive---------0----------------------->

在配置 [Fluentd](http://fluentd.org) 时，我们经常需要提供凭证来访问事件源、目标和相关服务，比如像 [Slack](https://www.slack.com) 和 [PagerDuty](https://www.pagerduty.com/) 这样的通知工具。挑战在于，我们不希望凭据在 Fluentd 配置中是明文形式。

为什么这可能与你有关？好吧，如果你正在使用 K[ubernetes Engine for Oracle(OKE)](https://www.oracle.com/uk/cloud/cloud-native/container-engine-kubernetes/)， [Verrazano](https://www.oracle.com/uk/linux/verrazzano/) ，或者利用 OCI 非常适合更广泛地托管云原生解决方案这一事实，那么你很可能会遇到 Fluentd(即使它提供了一种方法，将日志捕获作为边盘注入到容器中，并将日志泵送到 OCI 日志/日志分析)。

![](img/bf26e1e2b340419dc19b8a4ae425037a.png)

# 使用环境变量

在《使用 Fluentd 登录操作》一书中，我们展示了如何从环境变量中获取敏感值，这样这些值就不会出现在配置文件中。然而，我们经常会遇到这样的问题，“这有多安全，环境变量不能被机器上的每个人看到吗？”

这个问题的答案归结为对环境变量如何工作有更深的理解。这里有一个很好的解释。简而言之，环境变量只能被创建该变量的进程看到，任何子进程都会收到父进程变量的副本。

这意味着，如果我们在一个 shell 中创建该变量，那么只有该 shell 和由该 shell 启动的任何进程可以看到该环境变量。因此，只要我们不将变量设置为系统级配置的一部分，我们就已经有了一定程度的安全性。因此，我们可以用一个设置所需环境变量的脚本来包装 Fluentd 的开始，然后一切都启动该脚本。

# 更好的方法？

当然，脚本为环境变量设置了一个值，这个值是从哪里来的，这个脚本有多安全？对此有几种选择…

*   在由访问权限严格控制的配置文件中设置该值。因此，除非你作为一组特定的特权运行，否则你看不到这个变量。
*   注入来自外部世界的价值观——我们可以通过使用[配置图](https://kubernetes.io/docs/concepts/configuration/configmap/)与 [Kubernetes](https://kubernetes.io) 合作来做到这一点。
*   通过访问存储敏感数据值(如凭据)的另一个源来创建间接层。通过使用能够禁止访问数据的秘密存储器。如果我们认为某个客户已经受到威胁，我们有办法更容易地切断访问。通过集中存储敏感细节，我们还可以更改凭据，应该能够获得凭据的每个人都可以从中受益。

# 合并金库

这后一种方法就是秘密管理者如 [Hashicorp 金库](https://www.vaultproject.io/)、[变戏法](https://www.conjur.org/)或 [OCI 金库](https://docs.oracle.com/en-us/iaas/Content/KeyManagement/Concepts/keyoverview.htm)等可以提供帮助的方法。你可以从 Hashicorp [这里](https://learn.hashicorp.com/tutorials/vault/getting-started-first-secret?in=vault/getting-started)提供的一个简单例子中看出这一点。要了解这一点，第一步是安装 Vault。为了方便起见(使用本书的演示示例)，我们将使用 Hashicorp Vault 的开源版本在本地运行设置的步骤。安装可以有多种不同的方式，我们已经使用 [Chocolatey](https://community.chocolatey.org/) 进行了安装，但是也可以使用其他的包管理器。巧克力命令是`choco install vault`。

安装好 Vault 后，我们用命令`vault server -dev`启动 Vault。请注意，我们正在开发人员模式下运行 Vault 服务器。这样做简化了本练习的设置工作，不应该在生产用例中这样做。这将导致如下消息。请注意关于设置环境变量 **VAULT_ADDR、**的说明，我们需要设置第二个 **VAULT_TOKEN** 来保存输出中包含的根令牌。

![](img/a5099c080a560d4b0c7f58f9dfe9beb5.png)

这些变量需要在想要与 Vault 交互的每个 shell 中设置(回到我们关于小心使用环境变量和 shell 脚本的观点)。这些值是必需的，因为它们由 Vault CLI 和语言提供的 SDK 使用。值得注意的是，虽然这些值是“敏感的”,但您可能希望在客户机上运行的任何其他软件中重用它们。通过使 Vault 服务器中的令牌失效来切断客户端、以控制暴露内容的方式在服务器上配置机密以及与机密交互的权限，可以降低风险。

# 储存秘密

为了演示这个过程，我们需要用一个秘密来初始化保险库——使用简单的 KV(密钥值)存储机制，我们可以用命令`vault kv put secret/slack token=xoxb-735037803329-1110660446995-q6GrvjYukkzzYlBVJpzzigj7o`来完成这个操作。这个命令告诉保险库使用 KV 存储类型来创建一个名为`secret/slack`的名称空间，并添加一个密钥-值对，我的密钥名为`token`，在这种情况下，值就是本书第 4 章演示的 Slack 令牌。

我们可以使用 CLI 来测试值的获取，使用命令 `vault kv get secret/creds`来检索结果。这将返回秘密和大量有用的元数据。幸运的是，我们可以添加一个额外的参数，通过包含`-field=token`来告诉 CLI 我们感兴趣的键，从而给出我们想要的值。完整的命令现在看起来像`vault kv get -field=passcode secret/creds`。

# 如何将令牌检索纳入 Fluentd

要将该解决方案整合到我们的配置中，我们有几个选项:

*   使用 Ruby SDK
*   使用[curl](https://curl.se/docs/manpage.html)(CLI 有一个很好的特性，可以将 CLI 指令翻译成最接近的 curl 等价物)
*   直接调用 [Vault CLI](https://www.vaultproject.io/docs/commands)

Ruby 代码可以在[这里找到](https://github.com/hashicorp/vault-ruby)——将代码合并到配置中是很混乱的，我们可以应用一些技巧使它变得更整洁。

我们可以使用命令行，比如[curl](https://curl.se/)(CLI 提供了一个方便的选项，可以显示相当于 CLI 调用的 curl 命令(将`-output-curl-string`添加到 CLI 中)。这有两个缺点，首先，CLI 可以处理响应，为我们提取秘密的特定属性，CURL 选项返回完整的响应。其次，HTTP 头需要我们的认证令牌，这将把它放入配置文件的明文中。

因此，将 CLI 嵌入我们的 Fluentd 配置看起来是最干净的选择。这意味着我们可以从这样的配置开始:

```
<match *> @type slack token xoxb-735037803329-1110660446995-q6GrvjYukkzzYlBVJpzzigj7o username unifiedfluent icon_emoji :ghost: # if you don't want to use icon_url, delete this param. channel demo message Node2 says - %s message_keys message title %s title_keys time flush_interval 1s time_key time </match>
```

类似于:

```
<match *> @type slack token ***"#{`vault kv get -field=token secret/slack`}"*** username unifiedfluent icon_emoji :ghost: # if you don't want to use icon_url, delete this param. channel demo message Node2 says - %s message_keys message title %s title_keys time flush_interval 1s time_key time </match>
```

上面强调了最重要的变化，但让我们快速了解几个关键细节。首先，我们在这里要求 Fluentd 调用 Ruby——用`"#{`表示，以 `}"`结尾。接下来，我们将利用这样一个事实，即当 [Ruby](https://docs.ruby-lang.org/en/2.0.0/Kernel.html#method-i-60) 遇到一个反向滴答``` 时，它将使用一个子 shell 执行指令(重要的是，记住环境变量的关系)。CLI 返回的字符串是所需的令牌。结果，Fluentd 看到这条线路被解析为`token xoxb-735037803329-1110660446995-q6GrvjYukkzzYlBVJpzzigj7o`。

# 为什么更好？

这提供了一个很好的解决方案，因为我们在 Fluentd 的配置文件中没有任何凭证。如果我们怀疑值被劫持，我们可以指示服务器拒绝任何使用令牌的请求。我们有一个中央位置来管理我们的秘密，这使得处理密码轮换变得更加容易。客户机-服务器上保存的不同凭证的数量可以大大减少。虽然最后一步是特定于 Fluentd 的，但总体原则可以应用于许多场景。

加入我们的[公共开发者松弛频道](https://bit.ly/devrel_slack)的讨论吧！