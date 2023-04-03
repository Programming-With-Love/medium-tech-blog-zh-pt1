# Golang 系列—空结构

> 原文：<https://medium.easyread.co/golang-series-empty-struct-ed317e6d8600?source=collection_archive---------2----------------------->

## 空结构？这是什么？当我们使用时，使用空结构有什么好处？

![](img/96afea53667928864f6950bcf268bb7e.png)

Photo by [pixabay](https://pixabay.com/photos/the-interior-of-the-boards-wall-254577/)

大家好！在这个时候，我想分享一些与软件工程领域的技术相关的经验、想法或观点。然而，我的文章可能是一个轻松的话题，只是简短。我想分享我在 Golang 中使用空结构的经验和看法。

在过去，我经常发现让我只使用没有值的键的情况，例如在一个片上有一个唯一的键。那时候，我总是使用带 bool 键的地图。如果我想得到唯一的 int，方法如下:

```
**func** UniqueInt(slice []int) []int {
   mapInt := make(**map**[int]bool)
   **for** _, v := **range** slice {
      mapInt[v] = **true**}

   output := []int{}

   **for** v := **range** mapInt{
      output= append(output, v)
   }

   **return output**
}
```

然而，有时我会看到一些使用空结构`**struct{}**`的实现。为什么要用这个，有什么好处？

从尺寸开始，`**empty struct**`相对于其他尺寸最小。下面是获取大小的示例代码:

```
package mainimport (
     "fmt"
     "unsafe"
 )

 func main() {
     var s struct{}
     var i interface{}
     var b bool
     fmt.Println(unsafe.Sizeof(s), unsafe.Sizeof(i), unsafe.Sizeof(b))
 }
```

输出:

```
0 16 1
```

我们可以看到，空结构有`**0**`。而我一直用的`**bool**`有`**1**`值。

接下来，关于内存使用。我找到了一些代码(references 链接可以在 References 部分找到),并尝试修改它们以获得来自`**empty struct**`和`**bool**`的结果。参见下面的代码:

```
**package** main

**import** (
   **"fmt"
   "runtime"** )

**func** main() {
   *// Below is an example of using our PrintMemUsage() function
   // Print our starting memory usage (should be around 0mb)* fmt.Println(**"Start"**)
   PrintMemUsage()
   fmt.Println(**""**)

   structContainer := make(**map**[int]**struct**{}, 1000000)
   **for** i := 0; i < 1000000; i++ {
      structContainer[i] = **struct**{}{}
   }

   fmt.Println(**"With 1kk empty struct{}"**)
   PrintMemUsage()
   fmt.Println(**""**)

   nilContainer := make(**map**[int]**interface**{}, 1000000)
   **for** i := 0; i < 1000000; i++ {
      nilContainer[i] = nil
   }

   fmt.Println(**"With 1kk nil interface{}"**)
   PrintMemUsage()
   fmt.Println(**""**)

   boolContainer := make(**map**[int]bool, 1000000)
   **for** i := 0; i < 1000000; i++ {
      boolContainer[i] = ***true*** }

   fmt.Println(**"With 1kk true bool"**)
   PrintMemUsage()
   fmt.Println(**""**)

}

*// PrintMemUsage outputs the current, total and OS memory being used. As well as the number
// of garage collection cycles completed.* **func** PrintMemUsage() {
   **var** m runtime.MemStats
   runtime.ReadMemStats(&m)
   *// For info on each, see: https://golang.org/pkg/runtime/#MemStats* fmt.Printf(**"Alloc = %v KiB"**, bToMb(m.Alloc))
   fmt.Printf(**"\tTotalAlloc = %v KiB"**, bToMb(m.TotalAlloc))
   fmt.Printf(**"\tSys = %v KiB"**, bToMb(m.Sys))
   fmt.Printf(**"\tNumGC = %v\n"**, m.NumGC)
}

**func** bToMb(b uint64) uint64 {
   **return** b / 1024
}
```

当我们运行上面的代码时，我们会得到这样的结果:

```
Start
Alloc = 84 KiB TotalAlloc = 84 KiB Sys = 69714 KiB NumGC = 0With 1kk struct{}
Alloc = 21986 KiB TotalAlloc = 21999 KiB Sys = 71440 KiB NumGC = 1With 1kk nil interface{}
Alloc = 56649 KiB TotalAlloc = 78576 KiB Sys = 139154 KiB NumGC = 2With 1kk bool
Alloc = 80738 KiB TotalAlloc = 102665 KiB Sys = 139154 KiB NumGC = 2
```

根据上面的数据，我们可以说`**empty struct**`由于 Sys 值而具有最低的内存使用率。从 doc 来看， *Sys 是从 OS 获得的内存总字节数。*

然后我们可以修改代码来查找唯一的 int，如下所示:

```
**func** UniqueInt(slice []int) []int {
   mapInt := make(**map**[int]struct{})
   **for** _, v := **range** slice {
      mapInt[v] = **struct{}{}**}

   output := []int{}

   **for** v := **range** mapInt{
      output= append(output, v)
   }

   **return output**
}
```

感谢你阅读我的文章，希望对你有用！下一个话题再见！

# 参考

1.  [内存使用:nil vs 空结构{}](https://stackoverflow.com/questions/59089869/memory-usage-nil-vs-empty-struct)