### 数组

##### 一、数组概述

数组是具有相同类型的一组长度固定的数据项序列。这种类型可以是整型、字符串或者自定义类型。数组长度必须是一个常量表达式，并且必须是一个非负整数。数组长度也是数组类型的一部分，所以`[5]int`和`[10]int`是属于不同类型的。

如果我们想让数组元素类型为任意类型的话可以使用空接口作为类型，当使用值时我们必须先做一个类型判断 。

数组元素可以通过 索引（位置）来读取（或者修改），索引从 0 开始，第一个元素索引为 0，第二个索引为 1，以此类推。（数组以 0 开始在所有类 C 语言中是相似的）。元素的数目，也称为长度或者数组大小必须是固定的并且在声明该数组时就给出（编译时需要知道数组长度以便分配内存）。

> 1.数组是长度固定的数据类型
> 
> 2.数据元素的类型相同

##### 二、作用及应用场景

`作用`
数组在内存当中是连续的存储空间，可以有效的提升cpu的执行效率。

`应用场景`
存储多个相同类型的数据时，可以使用数组

##### 三、数组的定义

###### 一维数组的定义方式(1)

```go
// 数组的定义方式1
var arrayVariables [10]int
arrayVariables[0] = 100
arrayVariables[3] = 200
//arrayVariables[10] = 100
fmt.Println(arrayVariables)
```

###### 一维数组的定义方式(2)定义并初始化

```go 
var arrayVariables2 [5]int = [5]int{1,2,3,4,5}
// 在这种情况左边类型可简写
var arrayVariables3 = [5]int{1,2,3,4,5}
```

###### 数组内存分析

```go 
//遍历数组
var arrayVariables2 [5]int = [5]int{1,2,3,4,5}
var length = len(arrayVariables2)
for i :=0;i<length;i++ {
   fmt.Printf("arrayVariables2[%d]=%d,地址=%p\n",i,arrayVariables2[i],&arrayVariables2[i])
}

/*
数组在内存当中是连续的存储空间
数组的元素(下标)      元素值    元素的地址
0              1        0xc00006a060
1              2        0xc00006a068
2              3         0xc00006a070
3              4          0xc00006a078
4              5          0xc00006a080

*/
```

###### 一维数组定义方式(3) arrayVariables := […]int{1,2,3,4}

```go  
arrayVariables3 := [...]int{1,2,3,4,5}
fmt.Println(arrayVariables3,len(arrayVariables3))
``` 

不显示的指定数组长度

###### 一维数组定义方式(4)，指定索引下标

```go  
arrayVariables4 := [...]int{100:200,300:500}
```

显示指定某个索引位置的值

###### 二维数组,两个维度

```go 
var arrayVariables8 [4][2]int
fmt.Println(arrayVariables8)

arrayVariables9 := [4][2]int{{10,11},{3,5},{2,3},{100,88}}
fmt.Println(arrayVariables9)

arrayVariables10 := [4][2]int{1:{100,90},2:{8,9}}
fmt.Println(arrayVariables10)
```

###### 多维数组，多个维度

```go 
var arrayVariables11 [4][3][2]int = [4][3][2]int{}
fmt.Println(arrayVariables11)
```

##### 四、数组的遍历

###### 数组遍历方式一，通过for i :=0;i<length;i++ {} 形式

```go 
arrayVariables6 := [3]string{"小杨","coderyang","golang"}
arrayVariables7 := arrayVariables6
//for .. rage
for key,value := range arrayVariables6 {
   fmt.Printf("arrayVariables6[%d]=%v,地址=%p\n",key,value,&arrayVariables6[key])
}
for key,value := range arrayVariables7 {
   fmt.Printf("arrayVariables7[%d]=%v,地址=%p\n",key,value,&arrayVariables6[key])
}
```

###### 数组遍历方式二，使用 for .. range形式

```go 
arrayVariables6 := [3]string{"小杨","coderyang","golang"}
arrayVariables7 := arrayVariables6
//for .. rage
for key,value := range arrayVariables6 {
   fmt.Printf("arrayVariables6[%d]=%v,地址=%p\n",key,value,&arrayVariables6[key])
}
```

##### 五、数组注意事项

> 数组是值类型，这一点对于有c语言编程经验的同学来说需要特别的注意。
> 
> len()获取长度
> 
> 数组的长度是数组类型的组成部分,比如以下下数组为两种不同的类型

```go 
arrayVariables[10]int arrayVariables[11]int 
```

> 数组在内存当中是连续的存储空间

```go 
数组的元素(下标)          元素值            元素的地址
    0                    1            0xc00006a060
    1                    2            0xc00006a068
    2                    3            0xc00006a070
    3                    4            0xc00006a078
    4                    5            0xc00006a080
```

> 数组做为参数以引用形式传递函数，意义重大，当传递较大的数组时可以有效的提升cpu执行效率

```go 
func changeArrByPointer(arr *[10]int)  {
    (*arr)[0] = 100
}
```

> 数组做为值类型来传递函数,调用时是值拷贝，如果较大的数组传递对cpu的效能消耗是极其大的

```go 
func changeArr(arr [10]int) {
    arr[0] = 100
} 
```
