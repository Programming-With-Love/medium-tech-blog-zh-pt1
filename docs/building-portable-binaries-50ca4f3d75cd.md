# 构建可移植的二进制文件

> 原文：<https://medium.com/square-corner-blog/building-portable-binaries-50ca4f3d75cd?source=collection_archive---------1----------------------->

## 尝试自包含应用程序依赖项

*由* [*亚历克斯·库曼斯*](https://twitter.com/alexcoomans) *撰写。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

部署您的代码是向用户展示全新功能或重要缺陷修复的最后一个主要障碍。然而，确保您的应用程序拥有它所需要的一切可能是一件苦差事。准备应用程序进行部署的三种典型方法是:

1.  在运行时系统范围内安装依赖项
2.  通过应用程序交付依赖项，并依赖系统运行时
3.  应用程序附带运行时和依赖项

这篇文章将讨论第三种方法。但是首先，我将指出我们在方法一和方法二中遇到的一些问题，以及这两个选项是如何将我们推向第三个选项的。

对于选项一和选项二，你的应用程序依赖系统来提供一些东西。通常情况下，这是很好的，但是想象一下，如果你碰巧升级了你的操作系统，它包括了对你的运行时的更新。例如，Python 出现在许多 Linux 机器上。您可能刚刚损坏了您的应用程序并导致停机。使用选项一，您可能还会丢失所有系统依赖项。即使您尝试重新安装它们，它们也可能无法与较新的运行时一起工作。

我们以前见过这种情况，当操作系统升级破坏了一些依赖于系统二进制文件的服务时。这些服务使用的是旧的 MySQL 共享对象，在操作系统升级后消失了。因此，我们寻找一个更好的解决方案:选项 3。

# 为什么不用 X？

在寻找解决方案时，我们没有找到任何能够以我们需要的方式完全隔离运行时和依赖项的东西。相反，我们转向了两个众所周知的——尽管有些晦涩 Linux 栈的部分: [shebangs](http://en.wikipedia.org/wiki/Shebang_(Unix)) 和 [rpaths](http://wikipedia.org/wiki/Rpath) 。

Shebangs 是以#开头的第一行脚本，比如/bin/bash 或/usr/bin/env ruby！。内核读取#！并用这个解释器执行脚本。

rpath 是共享库的运行时搜索路径，被硬编码到编译后的二进制文件的头中。这允许二进制代码搜索自身的其他部分或核心内容，如提供核心 C 标准库的 [libc](http://en.wikipedia.org/wiki/C_standard_library) 。

所有这些工作都始于一个简单的需求，即修复一个应用程序，该应用程序依赖于系统来提供它的所有依赖项，并在部署到新机器时发生故障。虽然 Virtualenv 作为一种解决方案出现，但它对 Python 安装问题没有帮助。结果，一旦我有了一个可重定位的 Python，我就像普通的那样安装了依赖项——因此 Virtualenv 不会提供任何额外的东西。

# 舍邦

shebangs 最大的缺点是它们不支持相对于二进制位置的相对路径。您需要硬编码一个系统路径，或者在执行时使用一个相对于当前工作目录的路径。第一个选项在重定位二进制文件时不起作用，第二个选项也不起作用，因为它要求您将 cd 放入正确的目录。

下面是我编译的一个 Python 版本中的 pip shim 示例:

```
#!/home/vagrant/python/bin/python
# EASY-INSTALL-ENTRY-SCRIPT: 'pip==1.3.1','console_scripts','pip'
__requires__ = 'pip==1.3.1'
import sys
from pkg_resources import load_entry_pointif __name__ == '__main__':
    sys.exit(
        load_entry_point('pip==1.3.1', 'console_scripts', 'pip')()
    )
```

你会注意到#！/home/vagger/python/bin/Python she bang，如果我将 Python 二进制文件移到别处，它会失败。

某些语言支持使这个问题更容易解决的技巧。例如，很久以前内核不支持 shebangs 所以，你必须确保你在运行你的解释器。受这些工作的启发(经过反复试验)，我想到了以下方法:

```
#!/bin/bash -e
"eval" '$(cd `dirname $0`; pwd)/python $(cd `dirname $0`; pwd)/$(basename $0) "$@" && exit 0'
# python code
```

在 Python 中执行时，相邻的字符串被简单地连接在一起，充当一个空操作。但是在 Bash 中，它是作为代码执行的，整个引用部分被视为 eval 的一个参数——这正是我们需要的。

由于它期望参数的方式，我不得不使用 eval 和 exit 0 命令与-e 标志配对，而不是 exec，因为否则您会得到如下命令:

```
"/home/vagrant/python/bin/python /home/vagrant/python/bin/pip"
```

棘手的是，在参数名称中，空格被视为文字空格，但在打印时，它与正确的参数集没有什么不同:

```
"/home/vagrant/python/bin/python" "/home/vagrant/python/bin/pip"
```

单引号是最后一部分，我花了一段时间来纠正，以便$@(即脚本的参数)被正确传递。

# RPATH

可执行文件的 rpath 是程序中的一个头，它帮助链接器在运行时找到所需的共享对象。共享对象是一组编译后的代码，旨在在一组不同的二进制文件之间共享。Libc 是共享对象的最好例子，因为几乎所有东西都需要它。以下是/bin/bash 的一些头文件示例:

```
$ readelf -d /bin/bash
Dynamic section at offset 0xd3738 contains 26 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libtinfo.so.5]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
(other header information)
```

您会注意到 bash 需要 libc、libdl 和 libtinfo。在那个二进制文件上运行 ldd 会给出链接器完成的依赖关系解析的准确位置:

```
$ ldd /bin/bash
    linux-vdso.so.1 =>  (0x00007fff463ff000)
    libtinfo.so.5 => /lib64/libtinfo.so.5 (0x0000003415400000)
    libdl.so.2 => /lib64/libdl.so.2 (0x0000003412c00000)
    libc.so.6 => /lib64/libc.so.6 (0x0000003413000000)
    /lib64/ld-linux-x86-64.so.2 (0x0000003412800000)
```

ldd 将报告它找到的共享对象的位置，但是它忽略 rpath(如果存在的话)。bash 不使用 rpath，但是这里有一个 Python 的例子，我们将很快编译它来帮助实现第三个选项(即，将运行时和依赖项与应用一起发布):

```
$ readelf -d /example/python/bin/python
Dynamic section at offset 0x8f0 contains 26 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libpython2.7.so.1.0]
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libutil.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib]
```

注意 RPATH 行吗？这就是允许我们重新定位二进制文件的魔力。这对 Python 尤其重要，因为二进制文件需要一个共享对象。$ORIGIN 变量也非常重要，因为它允许您创建相对于二进制文件的路径。在这种情况下，Python 应该在/example/lib 中查找共享对象，以及默认的链接器位置。 [ld.so](http://man7.org/linux/man-pages/man8/ld.so.8.html) 的手册页有更多详细信息。

虽然这很神奇，但实际上从源代码到实际的二进制代码有点困难。由于 shell 中变量的形式是$var，所以对 shell 的嵌套调用可能会导致变量被插值或格式错误。(注意:要使这个 rpath 工作，需要存在文字字符串$ORIGIN。)

我知道，这是一个悬念。我为什么选择第三个选项？抓紧了。你首先需要了解我为什么使用 rpaths 的背景。我正在使用 Python，并计划将 mod_wsgi 编译为我的应用服务器，但是它要求 Python 是一个共享对象。因此，在编译 Python 时，可以给它-enable-shared 标志，让它构建共享对象。唯一的缺点是 Python 解释器现在需要能够找到那个共享对象。在我第一次尝试编译一个默认的 Python 版本后，我结束了这个错误:

```
$ /example/python/bin/python
/home/vagrant/python/bin/python: error while loading shared libraries: libpython2.7.so.1.0: cannot open shared object file: No such file or directory
```

嗯，那不是我想要的。我需要做以下事情之一:

1.  在运行时设置 LD_LIBRARY_PATH。
2.  在编译时设置 RPATH，以便它在执行时引用正确的 libpython.sowhen。

LD_LIBRARY_PATH 最大的缺点是它需要被设置为一个环境变量，每当你想使用 Python 时，记住这一点有点痛苦。相反，我决定选择第二个选项(rpath ),这使我第一次尝试使用 RPATH 进行编译:

```
LDFLAGS='-Wl,-rpath=$ORIGIN/../lib' ./configure --prefix=/example/python --enable-shared
```

配置运行良好，所以制作应该是小菜一碟:

```
$ make
...
gcc -pthread -shared -Wl,-rpath=RIGIN/../lib -Wl,-hlibpython2.7.so.1.0 -o libpython2.7.so.1.0 Modules/getbuildinfo.o Parser/bitset.o Parser/metagrammar.o Parser/firstsets.o ...  Modules/pwdmodule.o  Modules/_sre.o  Modules/_codecsmodule.o -lpthread -ldl  -lutil  -lm ; \
        ln -f libpython2.7.so.1.0 libpython2.7.so;
...
```

注意 RIGIN/../lib 缺少$O 部分？变量需要作为$ORIGIN/一直传递给链接器../lib 以使该技巧起作用。经过大量的反复试验，将变量正确传递给链接器的最后一个命令是:

```
LDFLAGS='-Wl,-rpath=\$$ORIGIN/../lib' ./configure --prefix=/example/python --enable-shared
```

我能够使用我在上面发布的 readelf 进行验证，最后的确认来自于运行:

```
$ /example/python/bin/python
Python 2.7.8 (default, Aug 26 2014, 06:15:54)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-4)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

如果您想要三重检查它是否加载了正确的库(即您的编译库，而不是系统库，如果它碰巧存在的话)，您可以使用 strace:

```
$ strace -e open,stat python/bin/python
...
open("/example/python/bin/../lib/tls/x86_64/libpython2.7.so.1.0", O_RDONLY) = -1 ENOENT (No such file or directory)
stat("/example/python/bin/../lib/tls/x86_64", 0x7fffa5a4d520) = -1 ENOENT (No such file or directory)
open("/example/python/bin/../lib/tls/libpython2.7.so.1.0", O_RDONLY) = -1 ENOENT (No such file or directory)
stat("/example/python/bin/../lib/tls", 0x7fffa5a4d520) = -1 ENOENT (No such file or directory)
open("/example/python/bin/../lib/x86_64/libpython2.7.so.1.0", O_RDONLY) = -1 ENOENT (No such file or directory)
stat("/example/python/bin/../lib/x86_64", 0x7fffa5a4d520) = -1 ENOENT (No such file or directory)
open("/example/python/bin/../lib/libpython2.7.so.1.0", O_RDONLY) = 3
...
```

您可以看到它最终会在/example/python/lib 目录中查找。

# 结论

显然，选择第三种方法不是最简单或最明显的方法——编译可重定位的二进制文件可能很难(有时甚至不可能)。然而，使用相对的 shebangs 和 rpaths 有助于简化应用程序的部署，并允许更大的灵活性，同时仍然保持可靠性。

[](https://twitter.com/alexcoomans) [## 亚历克斯·库曼斯(@亚历克斯·库曼斯)|推特

### 亚历克斯·库曼斯的最新推文(@alexcoomans)。“幻想新事物”方，骄傲的“乌达斯汀校友”。德克萨斯州奥斯汀…

twitter.com](https://twitter.com/alexcoomans)