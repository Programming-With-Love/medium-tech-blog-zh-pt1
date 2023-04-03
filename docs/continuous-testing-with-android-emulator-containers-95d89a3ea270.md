# 使用 Android 模拟器容器进行持续测试

> 原文：<https://medium.com/androiddevelopers/continuous-testing-with-android-emulator-containers-95d89a3ea270?source=collection_archive---------0----------------------->

![](img/0c3655bf7fef9d0f2fa3c6e61cc37d5b.png)

借助我们预先构建的 [Android 模拟器容器](https://github.com/google/android-emulator-container-scripts/blob/master/REGISTRY.MD)，在持续集成(CI)或部署(CD)上设置和运行 Android 模拟器比以往任何时候都更加容易。这些容器允许您找到并运行仿真器的正确版本，而没有令人头痛的依赖性管理，这使得将自动化测试作为 CI/CD 系统的一部分进行扩展变得容易，而没有物理设备场的维护成本。

今年早些时候，我们发布了 [Android 模拟器下载和 Docker 图像生成器脚本](https://android-developers.googleblog.com/2019/10/continuous-testing-with-new-android.html)来帮助开发者部署和调试远程模拟器。这些脚本使得找到正确的系统映像、管理系统依赖性和运行 Android 模拟器变得更加容易。

我们现在更进一步，*实验性地*为每个主要的仿真器版本提供预建的 [Android 仿真器容器](https://github.com/google/android-emulator-container-scripts/blob/master/REGISTRY.MD)。这些容器消除了手工运行生成器的需要，节省了时间和复杂性。别担心，预构建的容器仍然支持那些用 Docker 脚本构建的容器所提供的所有特性，比如 [adb](https://github.com/google/android-emulator-container-scripts#communicating-with-the-emulator-in-the-container) 和 [web](https://github.com/google/android-emulator-container-scripts#make-the-emulator-accessible-on-the-web) access。

[运行这些容器需要 Linux KVM](https://en.wikipedia.org/wiki/Kernel-based_Virtual_Machine) ，可以通过在裸机或具有嵌套虚拟化的虚拟机上运行来实现。正确的选择取决于您的云提供商，因此请阅读我们的[文档](https://github.com/google/android-emulator-container-scripts/blob/master/REGISTRY.MD#requirements-and-recommendations)以获得建议。

下面的脚本演示了如何将 Android 模拟器容器集成到您的系统中，并使用它来运行测试。

Sample script to pull, run, and port forward an Android Emulator Container

查看我们的一般自述文件,了解更多关于开始使用和利用 Android 模拟器容器的信息。这是我们第一次提供预构建的仿真器容器，所以请在我们的[问题跟踪器](https://github.com/google/android-emulator-container-scripts/issues)上报告任何问题或功能请求。