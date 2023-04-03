# ODP.NET 追踪—基础知识

> 原文：<https://medium.com/oracledevs/odp-net-tracing-the-basics-5b110c266bce?source=collection_archive---------1----------------------->

![](img/34dda90376e0b0293f4126a47dc7d218.png)

我之前写过一些关于 ODP.NET 追踪的新特性。然后，我意识到并不是每个人都知道如何启用和配置 ODP.NET 跟踪。在这篇博文中，我将教授一些基础知识，并分享一些代码和配置，您可以将它们剪切并粘贴到您的。NET 应用程序，以便在需要时轻松生成 ODP.NET 跟踪。

# 基础知识

ODP。网络跟踪有两个您将始终使用的通用设置:

*   `TraceFileLocation` —跟踪文件目录位置
*   `TraceLevel` —跟踪级别

`TraceFileLocation`不需要设置，因为它将使用默认的操作系统临时目录位置。但是，大多数人设置一个值来使跟踪文件更容易找到。

默认情况下，`TraceLevel`的值为零，表示禁用跟踪。这是你大多数时候想要的，因为跟踪会增加你的应用程序的开销，并且会降低性能。当您遇到希望了解更多信息或需要 Oracle 诊断和修复的问题时，可以通过将值设置为`TraceLevel`来启用跟踪。ODP.NET 有许多跟踪级别来跟踪提供者在外部和内部执行的不同类型的操作。

通常，您可以设置跟踪级别来跟踪所有 ODP.NET 操作。这是最有可能为调试获取足够信息的方法。对于托管的 ODP.NET 和 ODP.NET 核心，该级别为 7。对于非托管 ODP.NET，该级别为 127。

# 给我一些配置或配置(剪切和粘贴)

您可以在许多位置设置这些值。最容易的地方是在。NET 配置文件或代码中。不想重建就用前者。如果不使用. NET 配置文件，请使用后者。下面是托管 ODP 的一个配置示例。网络:

```
<oracle.manageddataaccess.client>
    <version number="*">     
        <settings>              
             <setting name=”TraceLevel” value=”7" />
             <setting name=”TraceFileLocation” value=”D:\traces\”/>
        </settings> 
    </version>
</oracle.manageddataaccess.client>
```

确保将此样本添加到的`<configuration>`部分。NET 配置文件。根据需要设置跟踪文件位置目录位置。

非托管 ODP.NET 使用相同的设置，但位于不同的子部分`<oracle.unmanageddataaccess.client>`。举个例子，

```
<oracle.unmanageddataaccess.client>
    <version number="*">     
        <settings>              
             <setting name=”TraceLevel” value=”127" />
             <setting name=”TraceFileLocation” value=”D:\traces\”/>
        </settings> 
    </version>
</oracle.unmanageddataaccess.client>
```

保存这些修改后，重启应用程序。调用 ODP.NET API 后，将创建并填充跟踪文件。

ODP。网芯不用。NET 配置文件。未来的版本将提供跟踪设置的配置文件。

ODP。NET Core 主要使用代码进行配置。ODP.NET`OracleConfiguration`类包含跟踪级别和跟踪文件位置设置，可以使用以下示例在 C#中进行设置:

```
OracleConfiguration.TraceFileLocation = @"D:\traces";
OracleConfiguration.TraceLevel = 7;
```

如果您想跟踪所有 ODP.NET 调用，请确保在您的应用程序调用任何其他 ODP.NET API 之前添加此代码片段。如果您的 ODP.NET 版本支持动态跟踪，请将这些调用放在有问题的代码区域之前。

在 ODAC 19c 和更高版本中，这些`OracleConfiguration`跟踪设置在托管和非托管 ODP.NET 中也可用。

# 结论

我希望你已经了解了一点 ODP.NET 跟踪是如何工作的。当您需要启用 ODP.NET 追踪时，可以将这些代码和配置片段复制并粘贴到您自己的应用程序中。