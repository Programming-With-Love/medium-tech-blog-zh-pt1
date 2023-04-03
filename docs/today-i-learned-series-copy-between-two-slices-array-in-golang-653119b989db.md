# 今天我学习了 Golang 系列:在两个切片/数组之间复制

> 原文：<https://medium.easyread.co/today-i-learned-series-copy-between-two-slices-array-in-golang-653119b989db?source=collection_archive---------1----------------------->

## 今天我在 Easyread 上学习系列的一部分

![](img/20759d4d6177967a3472952abb281ea2.png)

今天在 Golang 学到了一些诡异独特又很酷的东西。

当我想移动一个对象到 golang 中的第一个数组时，就会发生这种情况。

```
func sample(){
 number:=9
 arr:= []int{ 1,2,3,4,5}
}
```

我想实现的是，我想把`number : 9`附加到数组的第一个索引上

```
arr:=[]int{9,1,2,3,4,5}
```

然后，根据我的编程经验，我可以创建一个新的数组，然后通过循环复制值。

```
func sample(){
 number:=9
 arr:= []int{ 1,2,3,4,5}
 arrNew:= make([]int, len(arr) +1 )  for i:=0 ;i< (len(arr)+1) ; i++ {
  if i==0 {
    arrNew[i] = number
  }else{
   arrNew[i]=arr[i-1]
  }
 }
 fmt.Println(arrNew)
}// Print: [ 9,1,2,3,4,5]
```

经典就是这样运作的。但是，在我发现切片功能之前，我能做的会更简单:

```
func sample(){
 number:=9
 arr:= []int{ 1,2,3,4,5}
 arrNew:= make([]int,0)
 arrNew= append(arrNew,number)
 arrNew= append(arrNew,arr...) 
 fmt.Println(arrNew)
}
// Print: [ 9,1,2,3,4,5]
```

或者另一个样本

```
func sample(){
 number:=9
 arr:= []int{ 1,2,3,4,5}
 arrNew:= make([]int,len(arr)+1)
 arrNew[0]= number
 arrNew= append(arrNew[:1],arr...) 
 fmt.Println(arrNew)
}
// Print: [ 9,1,2,3,4,5]
```

这有助于减少代码行的长度。这个特性让我更喜欢使用 golang。