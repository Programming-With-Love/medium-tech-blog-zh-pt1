# GO:时间和日期 API

> 原文：<https://medium.com/globant/go-time-and-date-api-73280566d037?source=collection_archive---------4----------------------->

![](img/f2869fe772af8be4a522bafd3330ac3c.png)

包装时间提供测量和显示时间的功能。日历计算总是假设公历，没有闰秒。

操作系统提供了“**挂钟**”和“**单调时钟**”，前者会因时钟同步而改变，后者则不会。

总的规则是，挂钟是用来报时的，单调钟是用来计时的。

让我们来看看时间 API 的一些基本特性。为此，我们需要导入“时间”包。

**睡眠**

Sleep 至少在持续时间 d 内暂停当前的 goroutine。持续时间为负或为零会导致 Sleep 立即返回。

```
package mainimport (
 "time"
)func main() {
 time.Sleep(100 * time.Millisecond)
}
```

**持续时间**

让我们了解一下如何计算两个时间条目之间的持续时间。一般来说，我们用这个来衡量任何昂贵的电话所花费的时间。

```
package mainimport (
 "fmt"
 "time"
)func main() {
 t0 := time.Now() //Now returns the current local time.
 //Do some time consuming operation
 time.Sleep(100 * time.Millisecond)
 t1 := time.Now()
 fmt.Printf("The call took %v to run.\n", t1.Sub(t0))
}**O/P-**
The call took 100ms to run.
```

**解析时长**

ParseDuration 分析持续时间字符串。有效的时间单位是“ns”、“us”(或“s”)、“ms”、“s”、“m”、“h”。很少有实用函数可以计算特定时间内的纳秒、微秒、秒、分钟或小时。例如时间。ParseDuration("-1.5h ")。秒数()

```
package mainimport (
 "fmt"
 "time"
)func main() {
 hours1, _ := time.ParseDuration("-1.5h") **//.5h hrs is 30 secs**
 hours2, _ := time.ParseDuration("10h")
 hours3, _ := time.ParseDuration("1h10m10s")
 micro, _ := time.ParseDuration("1µs")
                                     **//prefix u for micro is also ok**
 fmt.Println(hours1)
 fmt.Println(hours2)
 fmt.Println(hours3)
 fmt.Printf("There are %.0f seconds in %v.\n",
               hours3.Seconds(),hours3)
 fmt.Printf("There are %d nanoseconds in %v.\n",     micro.Nanoseconds(),micro) h, _ := time.ParseDuration("4h30m")
 fmt.Printf("I've got %.1f hours of work left.", h.Hours())
}**O/P -** -1h30m0s
10h0m0s
1h10m10s
There are 4210 seconds in 1h10m10s.
There are 1000 nanoseconds in 1µs.
I've got 4.5 hours of work left.
```

**跑马灯**

ticker**在你想要定期重复做某件事的时候使用。**

```
package mainimport (
 "fmt"
 "time"
)func main() {
 c := time.Tick(time.Second * 2)
 for next := range c {
  fmt.Printf("%v %s\n", next, statusUpdate())
 }
}func statusUpdate() string {
 **//update status continuously**
 return "Go is Awesome"
}**O/P-**
2009-11-10 23:00:02 +0000 UTC m=+2.000000001 Go is Awesome
2009-11-10 23:00:04 +0000 UTC m=+4.000000001 Go is Awesome
2009-11-10 23:00:06 +0000 UTC m=+6.000000001 Go is Awesome
2009-11-10 23:00:08 +0000 UTC m=+8.000000001 Go is Awesome
```

****日期****

**在 Go 中，可以使用`time.Now()`确定当前时间。这将打印系统本地时间。如果你想要 UTC 格式的时间，那么使用`t := time.Now().UTC()`。如果想要时间戳，那么使用`t := time.Now().Unix()`。您也可以指定自己的自定义格式。如果你想知道今天是星期几，你可以通过 **time 得到。现在()。Weekday()，**查找当前月份使用，**时间。现在()。月份()。**同样，对于当前年份，使用**时间。现在()。年份()。有一些函数可以计算从某个特定时间过去了多长时间，你可以使用。自(t)，**类似地，要找出特定时间还剩多少时间，请使用 **time。直到(t)****

```
package mainimport (
 "fmt"
 "time"
)func main() {year, month, day := time.Now().Date()fmt.Println("Year : ", year, " Month: ", month, " Day: ", day)if month == time.May || day == 14 {
    fmt.Println("Its a may!")
}t := time.Date(2020, time.May, 15, 23, 0, 0, 0, time.UTC)fmt.Println("Given date is  :", t)fmt.Println("Time passed since given time   :", time.Since(t)) 

fmt.Println("Time remains until given time :", time.Until(t)) 

fmt.Println(time.May.String())     **//string representation**fmt.Println(time.Now().Weekday()) **//day of week**fmt.Println(time.Now().Month()) ** //current month**fmt.Println(time.Now().Year())  **//current year** }**O/P-**
Year :  2020  Month:  June  Day:  15
Given date is  : 2020-05-15 23:00:00 +0000 UTC
Time passed since given time   : 738h21m6.346267s
Time remains until given time : -738h21m6.346272s
May
Monday
June
2020
```

****位置****

**位置将时间瞬间映射到当时使用的区域。通常，位置代表在地理区域中使用的时间偏移的集合。对于许多地方来说，时间偏移取决于在该时刻是否使用夏令时。让我们比较一下当位置考虑日光节约或考虑固定时区时会有什么不同。**

```
package mainimport (
 "fmt"
 "time"
)func main() {**// China doesn't have daylight saving. It uses a fixed 8 hour offset from UTC.** secondsEastOfUTC := int((8 * time.Hour).Seconds())
beijing := time.FixedZone("Beijing Time", secondsEastOfUTC)**// Creating a time requires a location. Common locations are time.Local and time.UTC**.
timeInUTC := time.Date(2009, 1, 1, 12, 0, 0, 0, time.UTC)
sameTimeInBeijing := time.Date(2009, 1, 1, 20, 0, 0, 0, beijing) **// Although the UTC clock time is 1200 and the Beijing clock time is 2000, Beijing is 8 hours ahead so the two dates actually represent the same instant.** timesAreEqual := timeInUTC.Equal(sameTimeInBeijing)fmt.Println(timesAreEqual)
}**O/P-**
true
```

**在本例中，我们发现尽管为北京设置的时间比为 timeInUTC 设置的时间早 8 个小时，但仍然显示两个时间相等。背后的原因是，中国没有夏令时，它总是使用固定的 8 小时时差。**