# 远程访问工具的状态，第 1 部分

> 原文：<https://medium.com/walmartglobaltech/state-of-the-rat-part-1-cfec6c967e2f?source=collection_archive---------2----------------------->

![](img/ffbe927d18b7de12df9323ebb1792e48.png)

作者:佐伊·班尼特

远程访问工具(rat)是允许用户从另一个位置远程访问其计算机系统的软件。这些工具的一些关键功能包括文件共享、云存储、视频/文本聊天功能等。尽管对于商业用户来说它可能被认为是真实的，但是威胁参与者可以利用这些系统来实施恶意攻击和窃取数据。在本报告中，我们将重点关注以下被恶意行为者常用的远程访问工具:RemotePC、Ultraviewer、MSP360、PDQDeploy 和 ZohoAssist。

# RemotePC

RemotePC 是由 iDrive Inc .创建的免费商业工具，允许用户从其他设备远程访问和控制他们的 Windows、Mac 或 Linux 计算机。该软件的功能包括消息工具、文件传输和多显示器支持。目前已有机密报告指出其在威胁行为体违规中的相关性和用途。

漏洞报告/CVE

关于 RemotePC 的最新漏洞报告是在 2022 年 7 月(CVE-2021–34688 和 CVE-2021–34687)[7]。以前的版本允许多种安全威胁，如拒绝服务、绕过身份验证、权限提升、信息泄露和中间人攻击。

已验证的签名者:

```
iDrive Inc.
ProSoftnet Corporation
```

关联的文件名:

```
RemotePC
RPC DND Console
RemotePCService
RemotePC Suite
RPC Performance Service
RpcOTADND_Console.exe
Remotepcservice.exe
RemotePCUIU.exe
RPCPerformanceService.exe
RemotePC.exe
```

关联的域:

```
version.remotepc[.]com
web1.remotepc[.]com
www1.remotepc[.]com
```

关联 IP:

```
172.67.37.123
64.90.202.200
64.90.202.245
```

*关联哈希:*

```
8e6357da8f7666f608b38c36aacca109348ada83cf10179dd253d90d04fdbf1e
9c1c06d4cd5e02f306bd4fea5f8d74c9ac1a00f81c36f61a687fd7c75cd9dafe
04a672ae9aaf36afe78c4d20f39090e423c86379c9b8bc994c87321cb2347b2d
f20dc5a076e1e7e3ad731748a2e57e2cc04397b7e18b4aa825cd439375af63e6
3d11cf1d5f83678258e790b34e99f5c71c2dc3f14a27dd5f14192ab10b4d0217
```

在正常情况下，RemotePC 通常从以下路径执行:

```
%SAMPLEPATH%\RemotePC.exe
%USERPROFILE%\AppData\Local\Temp\is-VHFM6.tmp\RemotePC.tmp
%USERPROFILE%\AppData\Local\Temp\is-KO4JJ.tmp\RemotePC1.exe
%USERPROFILE%\AppData\Local\Temp\is-UG36L.tmp\RemotePC1.tmp
C:\Windows\SysWOW64\taskkill.exe
C:\Program Files (x86)\RemotePC\RPDUILaunch.exe
C:\Program Files (x86)\RemotePC\PreUninstall.exe
C:\Program Files (x86)\RemotePC\RPCFirewall.exe
C:\Program Files (x86)\RemotePC\SuiteLauncher.exe
C:\Program Files (x86)\RemotePC\RemotePCLauncher.exe
C:\Windows\SysWOW64\sc.exe
C:\Windows\System32\msiexec.exe
C:\Program Files (x86)\RemotePC\RPCDownloader.exe
C:\Program Files (x86)\RemotePC\RemotePCService.exe
C:\Program Files (x86)\RemotePC\RPCPrinterDownloader.exe
C:\Windows\System32\cmd.exe
C:\Windows\System32\sc.exe
C:\Program Files (x86)\RemotePC\RemotePCUIU.exe
C:\ProgramData\RemotePC\Codec\RemotePCPerformance.exe
C:\Windows\regedit.exe
C:\Windows\SysWOW64\regsvr32.exe
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\RegAsm.exe
C:\Program Files (x86)\RemotePC\RemotePCPerformance\RPCPerformanceService.exe
C:\Program Files (x86)\RemotePC\RemotePCPerformance\RpcApp\Tools\RpcUtility.exe
C:\Windows\System32\regsvr32.exe
C:\Windows\Microsoft.NET\Framework64\v2.0.50727\RegAsm.exe
C:\Windows\System32\bcdedit.exe
C:\Windows\SysWOW64\netsh.exe
C:\Program Files (x86)\RemotePC\RemotePCPerformance\PluginInstaller.exe
C:\Program Files (x86)\RemotePC\RemotePCPerformance\RemotePCPerformancePlugins.exe
C:\Program Files (x86)\RemotePC\RemotePCPerformance\RemotePCPerformancePrinter.exe
C:\Program Files (x86)\RemotePC\RemotePCPerformance\RpcPrinter\InstallPrinter.exe
%SAMPLEPATH%\3d11cf1d5f83678258e790b34e99f5c71c2dc3f14a27dd5f14192ab10b4d0217.exe
%USERPROFILE%\AppData\Local\Temp\is-G102D.tmp\3d11cf1d5f83678258e790b34e99f5c71c2dc3f14a27dd5f14192ab10b4d0217.tmp
%USERPROFILE%\AppData\Local\Temp\is-H2LAQ.tmp\RemotePC1.exe
%USERPROFILE%\AppData\Local\Temp\is-K8CKA.tmp\RemotePC1.tmp"C:\Windows\system32\rundll32.exe" C:\Windows\system32\shell32.dll,OpenAs_RunDLL C:\Program Files\RemotePC\rootcert.pem
"C:\Windows\System32\msiexec.exe" /qn /i "C:\ProgramData\RemotePC\PrinterSetup\Printer.msi"
```

# 超级浏览器

Ultraviewer 由 duc finkely 有限公司签署，是一个具有各种功能的工具，包括支持所有版本的 Windows、聊天框和远程文件共享。

威胁行为者利用 Ultraviewer 的最新事件之一是，美国电话电报公司艾伦实验室在一个名为“fatal rat”[1]的远程访问特洛伊木马中检测到该功能。该恶意软件允许攻击者在执行远程感染系统的命令之前，运行检查系统内虚拟机的测试。这种恶意软件的一个关键因素是它主动卸载了 Ultraviewer 并安装了 AnyDesk。在可疑的情况下，恶意软件将通过 IPC$暴力强制弱密码在受害者的网络上传播。如果成功，恶意软件会将自身复制到专用文件夹%Folder%\hackshen.exe 中，并远程执行复制的文件。

UltraViewer 的另一个用途是作为考试黑客计划的工具，德里警方逮捕了三名俄罗斯黑客，他们受雇为各种知名考试(如 GMAT、IBM、CCISO 等)提供答案。)[2].这些黑客声称已经帮助了数百名候选人，自 2019 年以来一直在进行。考试当天，黑客会发送一个“Ultraviewer”软件的链接，让他们的客户下载。从那里，客户端将使用 Ultraviewer 向输入测试正确答案的黑客授予访问权限。

已验证的签名者:

```
DucFabulous Co., Ltd
```

关联的文件名:

```
UltraViewerDesktop
UltraViewerService
UltraViewer_Service.exe
is-AQ77Q.tmp
UltraViewer_Desktop.exe
is-273TE.tmp
```

关联 IP:

```
20.99.132.105:443 (TCP)
91.199.212.52:80 (TCP)
172.64.155.188:80 (TCP)
104.18.32.68:80 (TCP)
23.216.147.64:443 (TCP)
13.107.4.50:80 (TCP)
192.168.0.1:137 (UDP)
23.216.147.76:443 (TCP)
```

*关联哈希:*

```
e18e537dd5869f41e09eee5e598a6fb0817f79b3b7d38d9fdd36015d9f5596ec
c92d5dfc09749554afd9175bf2dc31995f38565bd43d63497acf98ab0a0866f1
7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a
```

*在正常情况下，Ultraviewer 通常从以下路径执行:*

```
%SAMPLEPATH%\UltraViewer_setup_6.5_en.exe
%USERPROFILE%\AppData\Local\Temp\is-3QM76.tmp\UltraViewer_setup_6.5_en.tmp
C:\Windows\System32\wuapihost.exe
%SAMPLEPATH%\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.exe
%USERPROFILE%\AppData\Local\Temp\is-61CG1.tmp\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.tmp
%USERPROFILE%\AppData\Local\Temp\is-T02CO.tmp\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.tmp
%USERPROFILE%\AppData\Local\Temp\is-CQM9G.tmp\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.tmp
%USERPROFILE%\AppData\Local\Temp\is-6SFKI.tmp\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.tmp
%USERPROFILE%\AppData\Local\Temp\is-VIO6F.tmp\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.tmp
%USERPROFILE%\AppData\Local\Temp\is-376TQ.tmp\7db985064e0bf2f94ee071a83f57f8611e06039f0adcced38065deedf621526a.tmp
```

# MSP360

MSP360 原名 CloudBerry Lab，是一款不仅具有远程访问功能，还具有灾难恢复和备份管理功能的软件。该平台可以跨各种操作系统(Windows、MacOS、Linux)和平台使用，包括 VMWare、Google Workspace 和 Microsoft365。目前，已经有涉及该软件的机密报告，msp360[。]com 中的威胁参与者违规。

已验证的签名者:

```
CloudBerry Lab
MSPBytes, Corp.
Sectigo Public Code Signing CA
```

关联的文件名:

```
MSP Connect
CloudRaService.exe
CloudRaWpf.exe
Connect.exe
ConnectStandaloneSetup_v3.0.0.60_netv4.5.1.exe
```

关联 IP:

```
13.107.4.52:80 (TCP)
52.251.79.25:443 (TCP)
23.216.147.76:443 (TCP)
```

*关联哈希:*

```
35c46ce77a20732eac2db689befa652107670df51a96d6de48365765c3579010
27032e70a9bac6889d4775ca73aa31bf50213e3d585d899d5450d1d396bc7eff
609ee1b83cc15f4bc0a3036bb18ad7bc47089619a75e5d69ebb3b6ed93a4a420
```

*注册表项:*

```
*HKLM\SYSTEM\ControlSet001\Services\Connect Service
HKEY_LOCAL_MACHINE\SOFTWARE\CloudBerryLab\CloudBerry Remote Assistant*
```

*在正常情况下，MSP360 通常从以下路径执行:*

```
%user%\Desktop\CloudBerry Remote Assistant.lnk
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\CloudBerryLab\CloudBerry Remote Assistant\Uninstall.lnk
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\CloudBerryLab\CloudBerry Remote Assistant\CloudBerryLab Web Site.lnk
C:\ProgramData\CloudBerryLab\CloudBerry Remote Assistant\Logs\CloudBerry Remote Assistant.log
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\CloudBerryLab\CloudBerry Remote Assistant\CloudBerry Remote Assistant.lnk
C:\Program Files\Connect
C:\Program Files\Connect\AudioProcessingModuleCs.dll
C:\Program Files\Connect\Cloud.Backup.RM.SIO.dll
C:\Program Files\Connect\Cloud.Base.dll
C:\Program Files\Connect\Cloud.Client.dll
C:\Program Files\Connect\Cloud.RA.dll
C:\Program Files\Connect\Cloud.Ra.AppConfig.dll
C:\Program Files\Connect\Cloud.Ra.Client.dll
C:\Program Files\Connect\Cloud.Ra.Common.XmlSerializers.dll
C:\Program Files\Connect\Cloud.Ra.Common.dll
C:\Program Files\Connect\Cloud.Ra.CommonHelpers.dll
C:\Program Files\Connect\Cloud.Ra.DirectConnection.dll
C:\Program Files\Connect\Cloud.Ra.FileTransfer.dll
C:\Program Files\Connect\Cloud.Ra.Firewall.dll
C:\Program Files\Connect\Cloud.Ra.Server.dll
C:\Program Files\Connect\Cloud.Ra.ServiceContract.dll
C:\Program Files\Connect\Cloud.Ra.TransportController.dll
C:\Program Files\Connect\Cloud.Ra.Video.dll
C:\Program Files\Connect\Cloud.Ra.WinApi.dll
C:\Program Files\Connect\CloudRaCmd.exe
C:\Program Files\Connect\CloudRaCmd.exe.config
C:\Program Files\Connect\CloudRaSd.exe
C:\Program Files\Connect\CloudRaSd.exe.config
C:\Program Files\Connect\CloudRaService.InstallLog
C:\Program Files\Connect\CloudRaService.InstallState
C:\Program Files\Connect\CloudRaService.exe
C:\Program Files\Connect\CloudRaService.exe.config
C:\Program Files\Connect\CloudRaUtilities.exe
C:\Program Files\Connect\CloudRaUtilities.exe.config
C:\Program Files\Connect\Connect.exe
C:\Program Files\Connect\Connect.exe.config
C:\Program Files\Connect\ICSharpCode.SharpZipLib.dll
C:\Program Files\Connect\InstallUtil.InstallLog
C:\Program Files\Connect\LZ4.dll
C:\Program Files\Connect\MagnifierCapture.dll
C:\Program Files\Connect\NAudio.dll
C:\Program Files\Connect\NAudio.xml
C:\Program Files\Connect\Newtonsoft.Json.dll
C:\Program Files\Connect\Open.Nat.dll
C:\Program Files\Connect\Open.Nat.xml
C:\Program Files\Connect\install.log
C:\Program Files\Connect\librtc.dll
C:\Program Files\Connect\license.txt
C:\Program Files\Connect\mainicon.ico
C:\Program Files\Connect\x86
C:\Program Files\Connect\x86\librtc.dll
C:\ProgramData\Connect
C:\ProgramData\Connect\ExternalRequests
C:\ProgramData\Connect\Logs
C:\ProgramData\Connect\Logs\Connect.log
"C:\Program Files\CloudBerryLab\CloudBerry Remote Assistant\"
```

# PDQ 部署

PDQ Deploy 是一个软件工具，允许用户创建定制的部署包。本质上，用户可以在任何系统上远程安装软件。

威胁参与者使用的 PDQ 部署:

一个名为 Avos Locker 的勒索软件即服务(RaaS)软件利用多种远程访问工具来实施攻击和利用漏洞。攻击者利用安全模式配置来禁用大多数 Windows 第三方驱动程序和端点安全软件[3]。因此，Avos Locker 攻击者将机器重启到安全模式，并运行 IT 管理工具 AnyDesk。攻击者还利用 PDQ Deploy 向他们的目标机器推送批处理脚本。作为回报，他们将通过部署 Avos Locker 勒索软件来实施攻击。

已验证的签名者:

```
PDQ.COM Corporation
```

关联的文件名:

```
PDQDeploy Setup
PDQDeploy Service
PDQDeployService.exe
PDQDeployConsole.exe
PDQDeploySetup.exe
PDQDeploy.19.3.310.0.exe
PDQDeploySetup.exe
Deploy_19.3.310.0.exe
```

关联 IP:

```
192.168.0.54:137 (UDP)
23.61.187.27:80 (TCP)
20.99.132.105:443 (TCP)
23.216.147.76:443 (TCP)
23.49.139.27:80 (TCP)
23.216.147.64:443 (TCP)
```

*关联哈希:*

```
07ccb95db2924e2e2b70dfb2a1275d15d36bbe014390a4a5619557698e3a077a
03406847b2d1fb9ce71ac96f59f2c751b5502889e0233c2d32f0269f804077a4
```

在正常情况下，PDQ Deploy 通常从以下路径执行:

```
%SAMPLEPATH%\Deploy_19.3.310.0.exe
C:\Windows\Downloaded Installations\Admin Arsenal\PDQ Deploy\19.3.310.0\PDQDeploySetupPrep.exe
%SAMPLEPATH%\PDQDeploySetup.exe
```

# ZohoAssist

ZohoAssist 是一个远程访问工具，强调通过 web 浏览器启动和安排远程支持会话以及故障排除问题。

威胁参与者使用的 ZohoAssist:

**月神蛾钓鱼攻击(2022 年 7 月 12 日)**

Luna Moth ransom group，也称为 Silent Ransom Group，利用 Atera、Anydesk、Syncro 和 Splashtop 等商业远程访问工具实施了网络钓鱼欺诈。该小组得到了 Sygnia 事故响应小组的认可。Luna Moth 的策略包括用 Zoho Masterclass 或 Duolingo 的虚假订阅来引诱受害者。[4]受害者最初被发送伪造的发票以更新最初没有购买的所选服务的订购。当提示对收费提出异议或取消订阅时，受害者会通过电话联系客户服务，这将是威胁行为者之一。然后，威胁参与者会提供在受害者的系统上安装远程访问工具的指令。此外，在获得访问权限后，威胁参与者会安装其他工具，如 Rclone、sharps 和 SoftPerfect 网络扫描仪，以窃取用户数据。

**Zoho ManageEngine 漏洞事件(2022 年 2 月 16 日)**

名为“未被指控”的威胁参与者利用了一个 Zoho ManageEngine 漏洞，该漏洞允许他们执行代码。未经红十字国际委员会网络认证。[5]跟踪到的漏洞 CVE-2021–40539 允许参与者执行权限提升，并通过 web 外壳泄漏注册表配置单元和 active directory 文件。

已验证的签名者:

```
Zoho Corporation Private Limited
```

关联的文件名:

```
Zoho Assist
Connect.exe
za_connect.exe
ZohoURSService.exe
ZAService.exe
zaservice.exe
ZohoAssist
ZohoMeeting.exe
```

关联的域:

```
downloads.zohocdn.com
assist.cs.zohohost.com
zohoassist.com
zohohost.com
assistlab.zoho.com
assist.zoho.com
join.zoho.com
```

关联 IP:

```
136.143.191.95:443 (TCP)
136.143.190.0/23
```

*关联哈希:*

```
4f98f565336d5bb142239c4007ec1d9492caf4c31020176de380c8b31c9129ed
b72f8cb789ebb129640e8fc8616e3e1422448196d29f0d3533688ab4fc126965
```

*注册表项:*

```
HKLM\System\CurrentControlSet\Services\Zoho Assist-Remote Support
HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Zoho Assist
HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Zoho Assist Customer Plugin
HKEY_CURRENT_USER\Software\Classes\zohoassistlaunch
HKEY_CURRENT_USER\Software\Classes\zohoassistlaunchv2HKLM\System\CurrentControlSet\Services\Zoho Assist-Remote Support
HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Zoho Assist
HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Zoho Assist Customer Plugin
HKEY_CURRENT_USER\Software\Classes\zohoassistlaunch
HKEY_CURRENT_USER\Software\Classes\zohoassistlaunchv2
```

在正常情况下，Zoho Assist 通常从以下路径执行:

```
'C:\Program Files (x86)\ZohoMeeting\agent.exe' -agent -k 106052736 -s gwlabin1.zohoassist.com -altgw gwlab-wa.zohoassist.com -fileTransferGateways ft1-in1.zohoassist.com -ms assistlab.zoho.com -ssl true -email Arjun -authkey 0tWHPnTrsFAmRLJgEsaMp9Bizvry/FqxYLVMuFpj59nsRLUlmLb+ewcWgZXJiAx3exDxNdyuWDAhFXagn9DnaA== -authtype 1 -SERVICEAGENT -demo_mode false -demo_tech false -ShowInit 0 -group AUL -productID 1 -js join.zoho.com -c_check false
```

# 参考

1:[https://cyber security . att . com/blogs/labs-research/new-composite-rat-in-town-fatal rat-analysis](https://cybersecurity.att.com/blogs/labs-research/new-sophisticated-rat-in-town-fatalrat-analysis)

2:[https://indianex press . com/article/cities/Delhi/Russian-hackers-JEE-GMAT-examinated-7708815/](https://indianexpress.com/article/cities/delhi/russian-hackers-jee-gmat-exams-arrested-7708815/)

3:[https://news . sophos . com/en-us/2021/12/22/avos-locker-remote-access-box-even-running-in-safe-mode/](https://news.sophos.com/en-us/2021/12/22/avos-locker-remotely-accesses-boxes-even-running-in-safe-mode/)

4:[https://www . bleeping computer . com/news/security/new-luna-moth-hackers-breach-orgs-via-fake-subscription-renewals/](https://www.bleepingcomputer.com/news/security/new-luna-moth-hackers-breach-orgs-via-fake-subscription-renewals/)

5:[https://threat post . com/zoho-zero-day-manager engine-active-attack/177178/](https://threatpost.com/zoho-zero-day-manageengine-active-attack/177178/)

6:[https://www . ultra viewer . net/en/200000026-summary-of-ultra viewer-s-security-information . html](https://www.ultraviewer.net/en/200000026-summary-of-ultraviewer-s-security-information.html)

7:[https://www.opencve.io/cve?cvss=&搜索=远程电脑](https://www.opencve.io/cve?cvss=&search=remotepc)