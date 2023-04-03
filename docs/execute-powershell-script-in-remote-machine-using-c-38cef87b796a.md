# 使用 C#在远程机器上执行 Powershell 脚本

> 原文：<https://medium.com/quick-code/execute-powershell-script-in-remote-machine-using-c-38cef87b796a?source=collection_archive---------2----------------------->

![](img/51bab6bb2c69e806801e9cb34b5a7925.png)

Photo by [Bram Van Oost](https://unsplash.com/@ort?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

当在解释器中运行脚本时，您可以通过使用 [Invoke-Command](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7.1) 参数在远程机器中运行 PowerShell 脚本。但是为了在 C#中实现类似的功能，我们使用了来自 System.Management.Automation.dll 的叫做 [RunSpace](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.runspaces.runspace?view=powershellsdk-7.0.0) 的东西。

首先，我们需要使用凭据创建 WSManConnectionInfo 对象来连接到远程机器。我们刚刚创建的 connectionInfo 对象应该在创建运行空间时作为参数发送。通过 runspace。Open()创建一个到远程机器的新连接。

在上面的要点中，正在运行的脚本实际上存在于正在执行 c#代码的机器中。为了在远程机器上运行 ps 脚本，我们可以使用

```
ps.AddScript(scriptLocation);
```

我们可以使用

```
ps.AddArgument(@"Argument1");
```

为了运行脚本，需要引用系统。管理。必须增加自动化。