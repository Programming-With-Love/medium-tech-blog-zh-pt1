# 今天我学习了系列:在 Golang 中嵌入本地数据类型

> 原文：<https://medium.easyread.co/today-i-learned-series-embedding-native-data-type-in-golang-2ac3703fe73d?source=collection_archive---------2----------------------->

## 今天我在 Easyread 上学习了系列

![](img/20759d4d6177967a3472952abb281ea2.png)

Today I Learned from Google Images

今天我在为我们公司的新功能工作。在开发它的时候，我会遇到新的问题。

我很好奇，Golang 中的本地数据类型是否可以嵌入到 struct 中。

```
type  SomeStruct  struct {
 map[string]interface{}
 []int64
 FieldA string `json:"field_a"`
}
```

但是试过之后，发现这是非法选项。然后，我尝试了一种新的方法。也就是定义一种新的数据类型。

```
type CustomMap map[string]interface{}
type CustomArrayInt []int64
```

然后把它嵌入到我想要的结构中

```
type  SomeStruct  struct {
 CustomMap 
 CustomArrayInt 
 FieldA string `json:"field_a"`
}
```

不知何故，这是有效的。运行这段代码时没有编译错误。

## 转换成 JSON

然后，我很好奇，如果我用上面的 struct 把数据转换成 JSON 会发生什么。

```
func convertToJSON() string {
 mapOne := CustomMap{
  "age":    20,
  "gender": "male",
 }
 numbers := CustomArrayInt{
  2, 3, 4, 5,
 }
 sample := SomeStruct{
  CustomMap:      mapOne,
  CustomArrayInt: numbers,
  FieldA:         "sample",
 }
 jbyt, err := json.Marshal(sample)
 if err != nil {
  log.Fatal(err)
 }
 return string(jbyt)
}
```

当打印这个结果时，它打印:

```
{
"CustomMap":
   { "age":20,
     "gender":"male"
   },
"CustomArrayInt":[2,3,4,5],
 "field_a":"sample"
}
```

嗯，这不是我所期望的。我想会是这样的:

```
{
 "age":20,
 "gender" : "male", 
 "CustomArrayInt":[2,3,4,5],
 "field_a": "sample"
}
```

但是我错了。如果使用嵌入式结构，情况会有所不同。

稍后为了让 JSON 看起来更干净，我添加了 JSON 标签

```
type SomeStruct struct {
 CustomMap      `json:"custom_map"`
 CustomArrayInt `json:"custom_array"`
 FieldA         string `json:"field_a"`
}
```

所以它的 JSON 更像下面这个样子。更漂亮。

```
{
 "custom_map":
  { "age":20,
    "gender":"male"
  },
"custom_array":[2,3,4,5],
"field_a":"sample"
}
```

但是，我确定。我今天学到了一件新东西。Golang 游乐场的源代码:

 [## 围棋运动场

### 操场服务不仅仅被官方 Go 项目使用(Go by Example 是另一个例子),我们…

play.golang.org](https://play.golang.org/p/iJ-Vihgb_vx)