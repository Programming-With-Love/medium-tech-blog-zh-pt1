# LXD 小贴士

> 原文：<https://medium.com/walmartglobaltech/lxd-bits-and-tips-558933d544f2?source=collection_archive---------4----------------------->

![](img/bfad4fcee922709ce633ff3cd8aa2f2b.png)

Credit: [PeteLinforth / 2994 images](https://pixabay.com/en/users/PeteLinforth-202249/)

如果你是第一次去 LXD(谁不是呢？)，用法和交互跟 LXC 有点不一样。除了命令行差异之外，要实现充分的故障排除和实例管理，还有很多东西需要学习。

# 重要目录

## /var/log/lxd

这显然是一个重要的目录。从表面上看，它不需要太多的解释，但是应该注意的是，这里有一些有趣的花絮。虽然 **/var/log/lxd/lxd.log** 涵盖了 lxd 守护进程本身的操作，但是每个实例都有子目录。特别是， **lxc.conf** 可以提供关于容器如何配置的有用见解。

## /var/lib/lxd

这个目录存放了许多重要的文件。

**lxd.db** :一个 SQLite 数据库。这可能包含 lxd 配置变量，也可能包含关于实例的信息。

**containers/<instance>/root fs**:这是一个实例的根文件系统。

**图像**:保存图像和相关的清单数据。

**安全**:表观轮廓和一些其他数据。

还有一些其他目录和文件或多或少也是不言自明的。

# 重要命令

运行 **lxc help** 将向您显示大多数命令。这里列出了一些特别有用的命令。

```
**lxc config show <instance> --expanded**
```

这显示了关于实例的导入配置数据，这些数据在其他地方不容易获得。最重要的是，配置的 cgroup 变量会影响实例资源的可用性。

```
**lxc exec <instance> sh**
```

如果您熟悉 lxc-attach 命令，那么这对于 lxd 实例是不存在的。复制该功能的最佳方式是执行 sh。

```
**lxc file <command> <instance>**
```

直接推/拉/编辑实例上的文件。

```
**lxc image …**
```

图像处理。在 LXD 添加你自己的图片比在 LXC 容易得多；不需要在一个晦涩的目录中手动编辑复杂的 bash 脚本来从默认位置以外的地方获取图像。您可以创建自己的 tarball ( **教程即将推出！**)上传到 LXD，轻松创建新实例。您可以使用 **lxc 帮助图像**了解更多命令用法信息。

# 命名空间

在幕后，LXD 和 LXC 使用网络名称空间来隔离租户网络。有时候，出于某种原因，您可能需要直接与名称空间进行交互。

如果您熟悉命令 **ip netns exec** ，那么这一节将对您有用。默认情况下，网络名称空间在由 LXC/LXD 创建时没有给出名称。至少 iproute2 命令不能用 **ip netns 列表**识别这个名字。然而，通过运行 **ip netns list-id** ，您可以看到各种网络名称空间确实在使用中(如果您有实例在运行的话)。您可以用以下方式标记/命名这些现有的名称空间:

```
pid=$(lxc info instance-0000000d | grep Pid | awk ‘{print $2}’)
mkdir -p /var/run/netns
ln -sf /proc/$pid/ns/net /var/run/netns/$pid
```

上面的代码块改编自这里: [netns-lxc](https://gist.github.com/niedbalski/ea84a2dd7a7a5b50cda0)

# 请分享…

如果你有任何其他有用的命令/关于 LXD 用法的见解，我很乐意听到它们！