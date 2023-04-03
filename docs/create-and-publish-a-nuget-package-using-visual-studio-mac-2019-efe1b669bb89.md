# 使用 visual studio mac 2019 创建并发布 NuGet 包

> 原文：<https://medium.com/globant/create-and-publish-a-nuget-package-using-visual-studio-mac-2019-efe1b669bb89?source=collection_archive---------1----------------------->

![](img/34ad26ae74524b220a5d4bc6d99b8bcc.png)

Photo by [HowC#](http://www.howcsharp.com/103/enable-nuget-package-restore.html)

英寸 Net 中，NuGet 是一种共享代码的机制。NET 被创建、托管和使用，并为每个角色提供工具。NuGet 是一个包管理器，主要用于重用代码。NuGet 包是一个包含编译代码(dll)的带有`.nupkg`扩展名的 ZIP 文件。

**创建 NuGet** 的先决条件:

1.  安装任何版本的 Visual Studio 2019 从[visualstudio.com](https://www.visualstudio.com/)与. NET 核心相关的工作负载。

**创建一个. net 库项目:**

1.  打开 Visual studio
2.  选择文件选项

![](img/883e12c4f36d1017af3ffe0ef1693539.png)

3.选择新解决方案选项。

![](img/02ae9ffabbf42ef89333f14089dc010a.png)

4.选择。Net 标准库模板，然后单击下一步。

![](img/a9a8fd21b8cda57d23872f02ae2631b3.png)

5.目标框架应该是标准 2.1，然后单击下一步。

![](img/fb2da3d6e5590d7efe93c54b3df7bf1a.png)

6.指定项目的名称，然后单击创建。

![](img/70e3ee694b6bd036f04355d080ee3e87.png)

**创建 NuGet 包的步骤:**

1.  打开 Visual studio。
2.  按照上面的步骤创建一个. net 库项目
3.  右键单击该项目。

![](img/5d7ef4611b2064a7efda9d6f4c81c1d8.png)

4.从菜单中选择选项字段，将会打开一个窗口。

![](img/341e6a91fe84af74202c0fb80c920c25.png)

5.在最后一个 NuGet 包部分选择构建选项。

![](img/35cbed077c36e4ef042a3b618a64db66.png)

当您选中复选框时，它会显示配置元数据的警告，因此在创建包之前，需要配置元数据。

6.从窗口中选择元数据选项，您可以看到元数据将有两个选项卡常规和详细信息

![](img/e067071d23ed19b56a8646c3274ac2fa.png)

7.选择 General，为您的包指定一个唯一的标识符，并填写任何其他所需的属性。

> *Id:是包 Id 这是包的标识符，它必须是唯一的*
> 
> *版本:是包版本*
> 
> *作者:设置作者姓名，默认值为* `*AssemblyName*`
> 
> *描述:描述是包的定义，包的意图是什么。*

![](img/59a636e48f3fea7e3e1e3597a67abcbb.png)

8.现在选择“详细信息”选项卡，除了“详细信息”部分中的“所有者”字段外，所有字段都是可选的。

![](img/f3169d0ce4e92c1589a08a548d2c2e6b.png)

9.给出所有者的名称(缺省值是程序集名称)，然后在同一个窗口中单击 build 选项。

![](img/f3334458aa0fa55f194cd8fb3bf9b54f.png)

您将能够通过添加这些细节来创建 NuGet 包，但是请记住，对于发布包来说，指定许可证 url 是很重要的。然而，当你发布你的预算时，你会得到一个警告，说`he 'licenseUrl' element will be deprecated. Consider using the 'license' element instead.`

为避免此警告，请提供 LicenseExpression，而不是*<packagelicense expression>MIT</packagelicense expression>*

要添加许可证，请遵循以下步骤:

12.右键点击项目->选择*编辑项目文件*选项- >移除*<PackageLicenseUrl>*->粘贴*<PackageLicenseExpression>MIT</PackageLicenseExpression>*

要阅读有关许可证的信息，请点击此链接[https://docs . Microsoft . com/en-us/nu get/reference/nu spec # license URL](https://docs.microsoft.com/en-us/nuget/reference/nuspec#licenseurl)

13.现在选中创建一个 NuGet 包选项并关闭窗口。

![](img/b82ff6bef9f652aa082ca1c030ca5da4.png)

**生成包:**

14.从顶部菜单中选择构建选项。

![](img/6d58247b7873a25c322d9bc0f7c70e5c.png)

15.单击 pack 选项，它将构建解决方案并打包为 NuGet 包。

![](img/2360ba70d079eeda5046be15a2f76723.png)

16.现在观察构建输出窗口，您将看到扩展名为. nupkg 的包名。

![](img/8eb1be32f5ab472c7f5f95df61ded429.png)

**发布预算**

17.要发布 NuGet 包，请访问[https://www.nuget.org/](https://www.nuget.org/)

18.使用您的帐户登录

![](img/8f4f6251e261eb4aa11a9eb521e1683a.png)

19.登录后，通过提供一个唯一的用户名注册您的帐户

![](img/f5c0fb181725d0bfb1bc79554752141e.png)

20.输入用户名后，点击注册，然后下面的屏幕将出现。

![](img/de12e2f0f009c4677c5320b1ad729409.png)

21.现在，在 Visual studio 中右键单击该项目，并选择“在 Finder 中显示”选项->转到 Bin。->转到调试->选择。nupkg 文件，并将其拖动到上面屏幕中的浏览选项。

您的包将被成功上传。

**参考文献:**

[](https://docs.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package-using-visual-studio?tabs=nuget) [## 创建并发布. NET 标准 NuGet 包 Windows 上的 Visual Studio

### 在 Windows 上的 Visual Studio 中从. NET 标准类库创建 NuGet 包是一个简单的过程，并且…

docs.microsoft.com](https://docs.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package-using-visual-studio?tabs=nuget) [](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package-dotnet-cli) [## 使用 dotnet CLI 创建 NuGet 包

### 无论您的软件包做什么或包含什么代码，您都可以使用一个 CLI 工具，nuget.exe 或…

docs.microsoft.com](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package-dotnet-cli) [](https://docs.microsoft.com/en-us/nuget/what-is-nuget) [## NuGet 是什么，它有什么作用？

### 任何现代开发平台的基本工具是一种机制，开发人员可以通过它来创建、共享和…

docs.microsoft.com](https://docs.microsoft.com/en-us/nuget/what-is-nuget)  [## licenses.nuget.org

### 随着许可证表达式的引入，出现了一个需求，即需要一个可靠的服务来提供…

docs.microsoft.com](https://docs.microsoft.com/en-us/nuget/nuget-org/licenses.nuget.org)