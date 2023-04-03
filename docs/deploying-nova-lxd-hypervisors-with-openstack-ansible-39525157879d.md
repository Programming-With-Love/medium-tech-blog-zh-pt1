# 使用 OpenStack-Ansible 部署 Nova-LXD 虚拟机管理程序

> 原文：<https://medium.com/walmartglobaltech/deploying-nova-lxd-hypervisors-with-openstack-ansible-39525157879d?source=collection_archive---------3----------------------->

![](img/bc64eabf3333fdf11fc1e191a8836fe5.png)

Unsplash [https://pixabay.com/en/books-bookshelf-library-literature-1245744/](https://pixabay.com/en/books-bookshelf-library-literature-1245744/)

Ubuntu 团队开发了一个相对较新的管理程序: [LXD](https://linuxcontainers.org/lxd/) 。这项技术建立在 Linux 容器的基础上，也就是 T2 LXC T3。LXD 是一个守护进程，它在 LXC 库之上实现了 RESTful API。最近开发了一个新的 OpenStack nova 插件， [nova-lxd](https://linuxcontainers.org/lxd/getting-started-openstack/) 。这个插件是传统 qemu/libvirt nova 驱动程序的替代插件。

如果您已经着眼于部署这种新的 nova-lxd 驱动程序，以便在您现有的 OpenStack 云中启用一流的容器，但不确定如何将其与您的基础架构集成， [OpenStack-Ansible](http://docs.openstack.org/developer/openstack-ansible/) 可能是解决方案。

部署 OpenStack 云是一个复杂的过程。幸运的是，支持 OpenStack-Ansible 的有用社区支持你。OpenStack 是一个移动的目标，OpenStack-Ansible 帮助部署者集成构建 OpenStack 云所需的所有各种组件和依赖关系。

如果您想在您的实验室中试用这种新的虚拟机管理程序，我建议尝试 OpenStack-Ansible 作为部署方法。你可以在这里找到安装指南: [Openstack-Ansible 安装指南](http://docs.openstack.org/developer/openstack-ansible/install-guide/)。一定要检查主分支以获得新的 nova-lxd 特性，直到 Newton 发布被标记。

如果您只想在虚拟机中测试 OpenStack-Ansible，一体化部署方法是另一个很好的选择: [OpenStack-Ansible AIO 快速入门](http://docs.openstack.org/developer/openstack-ansible/developer-docs/quickstart-aio.html)

在引导部署主机之后，可以将以下内容添加到您的**/etc/open stack _ deploy/user _ variables . yml**文件中:

> nova_virt_type: lxd

您还需要在**/etc/open stack _ deploy/user _ secrets . yml**中添加一个条目 **lxd_trust_password**

如果您刚刚开始使用 OpenStack-Ansible 并遇到了困难，请随时通过#openstack-ansible 中 freenode 上的 IRC 寻求帮助。这是一个非常活跃和活跃的社区，反馈、错误报告和提交总是受欢迎的。