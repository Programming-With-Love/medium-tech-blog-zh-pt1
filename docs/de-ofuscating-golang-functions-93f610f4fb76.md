# 去混淆 GoLang 函数

> 原文：<https://medium.com/walmartglobaltech/de-ofuscating-golang-functions-93f610f4fb76?source=collection_archive---------0----------------------->

![](img/3d3748e35f52ec4504b72aa69dcaf0e0.png)

GoLang[8]为恶意软件开发人员提供了一个绝佳的机会来创建工具，这些工具向恶意软件研究人员和逆向工程师提出了挑战，同时又能够跨各种架构进行交叉编译。

此后，许多人开发了各种工具来帮助对这些二进制文件进行静态逆向工程，并且开发了其他工具来演示如何通过各种混淆方法攻击这些技术。目前大多数静态反转 GoLang 二进制文件的工具依赖于[的存在。gopclntab 结构](http://github.com/strazzere/golang_loader_assist/blob/master/Bsides-GO-Forth-And-Reverse.pdf)即使在剥离的二进制文件中也有一个函数名和它们的偏移量的列表，因此你可以枚举这个结构并用它们的标准库名重命名所有剥离的函数。这种结构不是必需的，可以被另一个进程清零或枚举，以破坏函数名，造成额外的混乱。事实上，有两种现有的方法可以混淆 golang 编译的二进制文件，一种是在通过[操作源代码](http://github.com/unixpickle/gobfuscate)编译之前，另一种是枚举已经编译好的[二进制文件的 gopclntab 结构来操作它](http://github.com/sysopfb/GoMang)。

对于如何消除 GoLang 二进制文件的模糊性，我最初的想法是创建一个系统，在这个系统中，您可以根据字节码序列匹配标准库函数，这将允许您识别函数的真实名称，然后如果需要的话，最终可以重建 gopclntab 结构。要做到这一点，我们需要一种基于所用 GoLang 版本和架构的标准库函数编目方法，为了证明我们的想法，我们可以从一个单一架构开始，在这种情况下是“windows_386”和“windows_amd64”。

# 编目标准功能

我们需要做的是将所有的标准 GoLang 库编译成目标文件，解析目标文件并存储我们需要的相关数据。您可以强制 GoLang 为给定的架构生成这些数据:

```
env GOOS=<os> GOARCH=<arch> go install -v std
```

为了解析目标文件，我们只需要知道它们的结构，幸运的是这是有据可查的，GoLang 目标文件基本上是 SymID 结构的列表，所涉及的结构在标准 GoLang 文档中有记录[10，11]。我们也可以通过构建一些简单的 GoLang 程序，使用标准库中的 goobj 包[5]打印基于代码的数据，来自己检查它们。

```
f,_ := os.Open(*file);obj, _ := goobj.Parse(f, *pkgpath)fmt.Printf("Funct: %#v\n", obj)
```

输出片段:

```
goobj.SymID{Name:"type..importpath.math.", Version: 0}, goobj.SymID{Name:"type..importpath.os.", Version: 0}, goobj.SymID{Name:"type..importpath.reflect.", Version: 0}, goobj.SymID{Name:"type..importpath.strconv.", Version: 0}, goobj.SymID{Name:"type..importpath.sync.", Version: 0}, goobj.SymID{Name:"type..importpath.unicode/utf8.", Version: 0}}, Syms:[]*goobj.Sym{(*goobj.Sym)( 0xc420112000), (*goobj.Sym)( 0xc420112070), (*goobj.Sym)( 0xc420112150), (*goobj.Sym)( 0xc4201121c0), (*goobj.Sym)( 0xc420112230), (*goobj.Sym)( 0xc4201122a0), (*goobj.Sym)( 0xc420112310), (*goobj.Sym)( 0xc420112380), (*goobj.Sym)
```

在返回的结构中是一个 Sym 对象列表，这些对象是我们可以访问的结构:

```
type Sym struct { SymID                // symbol identifier (name and version) Kind  objabi.SymKind // kind of symbol DupOK bool           // are duplicate definitions okay? Size  int64          // size of corresponding data Type  SymID          // symbol for Go type information Data  Data           // memory image of symbol Reloc []Reloc        // relocations to apply to Data Func  *Func          // additional data for functions}
```

为了检索函数，我们需要遍历这个列表，并存储子结构 SymID 中的名字。

```
type SymID struct { Name string Version int64}
```

以及可以使用子结构数据找到的函数本身，因为它包含函数的偏移量和大小。

```
type Data struct { Offset int64 Size   int64}
```

现在，我们可以枚举这些目标文件，并存储我们需要的相关数据，如整个函数及其名称，我们还有一个优势，即每个函数在字节码中都有空内存地址和偏移量。接下来我们需要能够解析 GoLang 编译的二进制文件来枚举它们的功能。

# 解析编译的二进制文件

解析编译后的 GoLang 二进制文件非常简单，这个过程也已经与示例代码一起记录下来，这些示例代码来自用于混淆表的工具[4]以及用于静态分析的解析工具[3]。这里的主要缺点是函数长度不包含在我们将要处理的结构中，所以我们必须在我们的过程中考虑到这一点。

```
lookup = binascii.unhexlify("FBFFFFFF0000")def check_is_gopclntab(data, off): (first_entry, first_entry_off) = struct.unpack_from('<II', data[off+12:]) func_loc = struct.unpack_from('<I', data[first_entry_off+off:])[0] if func_loc == first_entry: return True return Falsedef findGoPcLn(data): possible_loc = data.find(lookup) while possible_loc != -1: if check_is_gopclntab(data, possible_loc): return possible_loc else: #keep searching till we reach end of binary possible_loc = data[possible_loc+1:].find(lookup) return None
```

使用我们从现有脚本[3]修改的代码，我们可以找到 gopclntab 结构。找到它之后，我们需要解析结构[4]:

```
struct gopclntab { char header[8]; uint function_count; struct function { char address[4]; char offsetToFuncName[4]; }[function_count]}
```

在解析每个函数后，我们存储函数地址后的前 200 个字节，以便进行适当的采样，因为我们从编译的标准库对象中获得了精确的函数长度，我们可以使用该大小作为在哪里停止扫描匹配的决定因素。这一数量的字节和对几个库的关注应该给我们足够的证据来表明所提出的方法是否有效。

# 比较功能

为了比较字节码，我们将基于两个字节码序列的最小长度遍历函数的长度，同时计算二进制代码中的函数与数据库中的函数相比有多接近。该方法很简单，只需计算“miss_bytes ”,但我们在最初的测试中覆盖了这一检查，以查看是否可以找到精确的匹配，即我们在跳过空字节的同时检查每个字节。跳过空字节的原因是，来自标准库编译对象的字节码序列中的空字节可能是要填充的内存偏移量的位置。

```
def compare_bytecode(s1, s2):
    good_bytes = 0
    checked_bytes = 0
    for i in range(min(len(s1),len(s2))):
        if s1[i] != '\x00':
            checked_bytes += 1
            if s1[i] == s2[i]:
                good_bytes += 1
    if good_bytes == 0 or checked_bytes <10:
        p = 0
        miss_bytes = 100
    else:
        miss_bytes = checked_bytes-good_bytes
        p = float(checked_bytes)/good_bytes
        p *= 100
        p = int(p)
    return((miss_bytes, p))
```

使用这个函数，我们将遍历 GoLang 二进制文件中的所有函数，将每个函数与数据库中的每个函数进行比较，建立一个这些比较的列表，然后对它们进行排序。这将允许找到最接近的匹配，但是出于本博客的目的，我们将简单地通过检查返回的 miss_bytes 计数是否为 0 来寻找精确的匹配。

# 初步结果

出于概念验证测试的目的，我们重点关注标准库包“fmt”[12]中的功能。然后，我们将执行 3 类检查，一类是针对在用于获取库对象字节码序列的同一系统上编译的非模糊样本的检查，一类是针对模糊样本的检查，另一类是针对从其他系统编译的样本的检查，但使用的是相同版本的 GoLang。

第一个测试工作得非常完美，这是个好消息，但也是意料之中的，因为样本和编译后的库操作码来自同一个系统。

```
Found match for :fmt.newPrinter with library function: fmt.newPrinterFound match for :fmt.(*pp).free with library function: fmt.(*pp).freeFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.(*pp).Write with library function: fmt.(*pp).WriteFound match for :fmt.Fprintf with library function: fmt.FprintfFound match for :fmt.Printf with library function: fmt.Printf
```

下一个测试纯粹是演示性的，因为这个示例现在只是通过篡改函数名来混淆。然而，这是有益的，因为这是消除函数名混淆的最终目标。

```
Found match for :wSvJxDLJ9eeZ17 with library function: fmt.(*pp).freeFound match for :EczqAzxqYVA0llA with library function: fmt.(*pp).WidthFound match for :tpIOMKrRu4hTwKrk0fH with library function: fmt.(*pp).PrecisionFound match for :J0kQbEASN1xgVU with library function: fmt.(*pp).FlagFound match for :sXYfrfNWvZrQQka with library function: fmt.(*pp).WriteFound match for :RXr0KzfvmmN with library function: fmt.FprintfFound match for :tHCyE9xFow with library function: fmt.Printf
```

下一个测试我使用了从 [VirusTotal](https://www.virustotal.com) 下载的一个样本，它说它是我在我的系统上使用的 GoLang 的相同版本，但是在其他地方被编译。

```
Found match for :fmt.(*fmt).fmtS with library function: fmt.(*fmt).fmt_sFound match for :fmt.(*fmt).fmtSx with library function: fmt.(*fmt).fmt_sxFound match for :fmt.(*fmt).fmtBx with library function: fmt.(*fmt).fmt_bxFound match for :fmt.(*fmt).fmtQ with library function: fmt.(*fmt).fmt_qFound match for :fmt.(*fmt).fmtQc with library function: fmt.(*fmt).fmt_qcFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.Printf with library function: fmt.Printf
```

这个例子表明，函数匹配是可能的，但我们需要测试更多的样本来验证。我们的样本集由从 VirusTotal 获得的另外四个 32 位 GoLang 1.10 编译二进制文件组成。

仅关注 32 位“fmt”包，我们的测试成功地完全匹配了样本集中的主要函数，而无需使用部分匹配逻辑。

样品 sha 256:970 cc F5 abed 19 b 5499 AFD 57864709817 b8 f8d 44350 a7d 38220776121313 b8 CBC

```
Found match for :fmt.(*fmt).truncate with library function: fmt.(*fmt).truncateFound match for :fmt.(*fmt).fmt_s with library function: fmt.(*fmt).fmt_sFound match for :fmt.(*fmt).fmt_sbx with library function: fmt.(*fmt).fmt_sbxFound match for :fmt.(*fmt).fmt_sx with library function: fmt.(*fmt).fmt_sxFound match for :fmt.(*fmt).fmt_bx with library function: fmt.(*fmt).fmt_bxFound match for :fmt.(*fmt).fmt_q with library function: fmt.(*fmt).fmt_qFound match for :fmt.(*fmt).fmt_c with library function: fmt.(*fmt).fmt_cFound match for :fmt.(*fmt).fmt_qc with library function: fmt.(*fmt).fmt_qcFound match for :fmt.(*buffer).WriteRune with library function: fmt.(*buffer).WriteRuneFound match for :fmt.newPrinter with library function: fmt.newPrinterFound match for :fmt.(*pp).free with library function: fmt.(*pp).freeFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.(*pp).Write with library function: fmt.(*pp).WriteFound match for :fmt.(*pp).WriteString with library function: fmt.(*pp).WriteStringFound match for :fmt.Fprintf with library function: fmt.FprintfFound match for :fmt.Printf with library function: fmt.Printf
```

样品 sha 256:DC 3 b 0 de 17 AE 3d 83763025174 FD 152 aeea 0 b 614 BD 4316 af 30510 e 842 DCA 806 ebe

```
Found match for :fmt.(*buffer).Write with library function: fmt.(*buffer).WriteFound match for :fmt.(*buffer).WriteString with library function: fmt.(*buffer).WriteFound match for :fmt.(*buffer).WriteByte with library function: fmt.(*buffer).WriteByteFound match for :fmt.(*buffer).WriteRune with library function: fmt.(*buffer).WriteRuneFound match for :fmt.newPrinter with library function: fmt.newPrinterFound match for :fmt.(*pp).free with library function: fmt.(*pp).freeFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.(*pp).Write with library function: fmt.(*pp).WriteFound match for :fmt.(*pp).WriteString with library function: fmt.(*pp).WriteStringFound match for :fmt.Fprintf with library function: fmt.FprintfFound match for :fmt.Printf with library function: fmt.Printf
```

样品 sha 256:ed 3c E1 b 2022d 702 f 837 c 255283 df 13 a 7396 B2 ba 5 db 0 fecd 5a 81 da 83 E0 c 56 C5 e 9

```
Found match for :fmt.newPrinter with library function: fmt.newPrinterFound match for :fmt.(*pp).free with library function: fmt.(*pp).freeFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.(*pp).Write with library function: fmt.(*pp).WriteFound match for :fmt.(*pp).WriteString with library function: fmt.(*pp).WriteStringFound match for :fmt.Fprintf with library function: fmt.FprintfFound match for :fmt.Printf with library function: fmt.Printf
```

样品 sha 256:FEC 50384 f 57656 e 47844403 a 38 b 77 f 9 AC 27 e 99 c 5662757 e 96 f 500 E1 de 6315 c5a

```
Found match for :fmt.(*fmt).fmtBoolean with library function: fmt.(*fmt).fmt_booleanFound match for :fmt.(*fmt).fmtS with library function: fmt.(*fmt).fmt_sFound match for :fmt.(*fmt).fmtSx with library function: fmt.(*fmt).fmt_sxFound match for :fmt.(*fmt).fmtBx with library function: fmt.(*fmt).fmt_bxFound match for :fmt.(*fmt).fmtQ with library function: fmt.(*fmt).fmt_qFound match for :fmt.(*fmt).fmtQc with library function: fmt.(*fmt).fmt_qcFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.Printf with library function: fmt.Printf
```

对 64 位 windows 示例的测试同样成功，但是需要对标准库中的 64 位函数进行编目。

样品 sha 256:58 f 40 EB 3 f 9 c 7 b 8 ccbb 6482 b 58 ab 9 EB 4c 84 a3 f 50 e 19 c 50 e 22 AAC 6593 e 56144779

```
Found match for :fmt.(*fmt).fmt_q with library function: fmt.(*fmt).fmt_qFound match for :fmt.(*fmt).fmt_c with library function: fmt.(*fmt).fmt_cFound match for :fmt.(*fmt).fmt_qc with library function: fmt.(*fmt).fmt_qcFound match for :fmt.(*fmt).fmt_float with library function: fmt.(*fmt).fmt_floatFound match for :fmt.(*buffer).WriteRune with library function: fmt.(*buffer).WriteRuneFound match for :fmt.newPrinter with library function: fmt.newPrinterFound match for :fmt.(*pp).free with library function: fmt.(*pp).freeFound match for :fmt.(*pp).Width with library function: fmt.(*pp).WidthFound match for :fmt.(*pp).Precision with library function: fmt.(*pp).PrecisionFound match for :fmt.(*pp).Flag with library function: fmt.(*pp).FlagFound match for :fmt.(*pp).Write with library function: fmt.(*pp).WriteFound match for :fmt.(*pp).WriteString with library function: fmt.(*pp).WriteStringFound match for :fmt.Fprintf with library function: fmt.FprintfFound match for :fmt.Printf with library function: fmt.Printf
```

样品 sha 256:E1 EB 18 AE 92 Fe 10d 4c B1 df 2d b 65 B2 Fe 0 e 08 e 282746 F2 b 5365522 CFA 52460328

```
Found match for :fmt.(*pp).catchPanic with library function: fmt.(*pp).catchPanicFound match for :fmt.(*pp).handleMethods with library function: fmt.(*pp).handleMethodsFound match for :fmt.(*pp).printArg with library function: fmt.(*pp).printArgFound match for :fmt.(*pp).printValue with library function: fmt.(*pp).printValueFound match for :fmt.intFromArg with library function: fmt.intFromArgFound match for :fmt.parseArgNumber with library function: fmt.parseArgNumberFound match for :fmt.(*pp).argNumber with library function: fmt.(*pp).argNumberFound match for :fmt.(*pp).badArgNum with library function: fmt.(*pp).badArgNumFound match for :fmt.(*pp).missingArg with library function: fmt.(*pp).missingArgFound match for :fmt.(*pp).doPrintf with library function: fmt.(*pp).doPrintfFound match for :fmt.(*pp).doPrint with library function: fmt.(*pp).doPrintFound match for :fmt.(*pp).doPrintln with library function: fmt.(*pp).doPrintln
```

初步结果表明，去除 GoLang 双星中的气泡是可能的，但也有一些问题需要解决。到目前为止遇到的最大问题是你需要为你感兴趣的每个架构和 GoLang 版本分类的函数的数量，这将导致速度问题，克服这个问题的一个可能的途径是将函数分类到 YARA[13]规则中，这样它们可以用来加速这个过程。这种方法还可以简化 gopclntab 结构的重建，而不是依赖它来枚举二进制文件中的函数。

# 参考

1:斯特拉泽雷，蒂姆。 *Bsides 前进后退*，2017，github . com/strazzere/golang _ loader _ assist/blob/master/b sides-GO-forward-And-Reverse . pdf

2:姐妹。"姐妹/IDAGolangHelper。"github.com/sibears/IDAGolangHelper.*GitHub*

3: Sysopfb。“Sysopfb/GoMang。”github.com/sysopfb/GoMang.的 GitHub

4:“Golang 内部，第 3 部分:链接器、目标文件和重定位。” *Altoros* ，2019 年 11 月 27 日，[www . alto ROS . com/blog/golang-internals-part-3-the-linker-object-files-and-relocations/](http://www.altoros.com/blog/golang-internals-part-3-the-linker-object-files-and-relocations/)。

5:S-马图科维奇。" s-Matyukevich/goobj_explorer "github.com/s-matyukevich/goobj_explorer. GitHub

6: Unixpickle。" UNIX pickle/gofbuscate。"github.com/unixpickle/gobfuscate.*GitHub*

7:“管理 Go 安装。”golang.org/doc/manage-install.*走*

8:“Go 是一种开源编程语言，可以轻松构建简单、可靠、高效的软件。”*走*，golang.org/.

9:“智能装配 6。”*用名称混淆代码 Mangling — SmartAssembly 6 —产品文档*，Documentation . red-gate . com/sa6/用 SmartAssembly 混淆代码/用名称混淆代码。

10:“包 Goobj。”*走*，golang.org/pkg/cmd/internal/goobj/#Sym.

11:“包 Goobj。”去 golang.org/pkg/cmd/internal/goobj/#SymID.吧

12:“封装 Fmt。”去 golang.org/pkg/fmt/.吧

13:*virustotal.github.io/yara/. YARA——恶意软件研究者的模式匹配瑞士刀*