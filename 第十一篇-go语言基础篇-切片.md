### 切片

##### 一、切片概述

> 1.一种数据结构，便于使用与管理我们的数据集合。
> 
> 2.按需自动增长，动态数组（通过append来完成）
> 
> 3.底层指向的是数组
> 
> 4.内存当中是连续的存储空间，可以有效的提升cpu的执行效率。
> 
> 5.引用类型

##### 二、切片组成

> 指向底层数组的指针
> 
> 切片元素的长度，通过len()获取
> 
> 容量,通过cap()获取

##### 三、切片的作用与应用场景

`作用`
在函数当中传递切片时，当数据类型数据较大时，使用切片可以有效减少内存占用，提高程序执行效率

`应用场景`
从数据库表中读取商品信息时，用到map切片，每一个map看成一个整体

##### 四、切片的定义

> 定义一个数组,让切片去引用,这种方式，数组可见

```go  
arrayVariables := [...]int{12, 21, 23, 55, 98, 2}

// 定义一个切片去引用数组，这种情况默认长度和容量一致
var sliceVariables []int
sliceVariables = arrayVariables[:]
```

通过以上代码，分析切片的底层情况

分别打印数组的地址与切片的地址来分析

```go  
// 切片的定义
var sliceVariables []int


// 定义一个数组
arrayVariables := [...]int{12,21,23,55,98,2}
for i :=0;i<len(arrayVariables);i++ {
   fmt.Printf("arrayVariables[%d]=%d,地址=%p\n",i,arrayVariables[i],&arrayVariables[i])
}

// 切片去引用数组
sliceVariables = arrayVariables[:]
for i:=0;i<len(sliceVariables);i++ {
   fmt.Printf("sliceVariables[%d]=%d,地址=%p\n",i, sliceVariables[i],&sliceVariables[i])
}

> 使用数组创建切片 区间是左闭右开  还有在go语言中切片是一种数据结构 而在python中 切片是一种操作

var sliceVariables2 []int
sliceVariables2 = arrayVariables[1:3]
// 12,21,23,55,98,2
// [21 23]
fmt.Println(sliceVariables2)
// 切片指向的是底层的数组
for i :=0;i<len(sliceVariables2);i++ {
   fmt.Printf("sliceVariables2[%d]=%d,地址=%p\n",i, sliceVariables2[i],&sliceVariables2[i])
}
sliceVariables2[0] = 100

fmt.Println(arrayVariables)
fmt.Println(sliceVariables2)
```
![sRVSjx.jpg](https://s3.ax1x.com/2021/01/19/sRVSjx.jpg)


> 通过make来创建切片,这种方式可以指定切片的大小和容量，未赋值，有默认值 ，这种方式切片对应的数组不可见，由make来维护

```go  
// 创建一个初始元素个数为 5 的数组切片，元素初始值为 0，并预留 6 个元素的存储空间
var sliceVariables2 []int = make([]int, 5, 6)

分析通过make方式定义切片的内存情况

var sliceVariables3 []int = make([]int,5,6)
fmt.Printf("sliceVariables3的长度=%d,容量=%d,\n切片指向的底层数组的地址=%p,切片自己的地址=%p\n",len(sliceVariables3),cap(sliceVariables3),sliceVariables3,&sliceVariables3)
```

> 通过make来创建切片的内存情况

![sRVkUe.jpg](https://s3.ax1x.com/2021/01/19/sRVkUe.jpg)

##### 五、切片的遍历

###### 1. 通过for i:=0;i<len(sliceVariables);i++ {}形式遍历

```go 
sliceVariables = arrayVariables[:]
for i:=0;i<len(sliceVariables);i++ {
   fmt.Printf("sliceVariables[%d]=%d,地址=%p\n",i, sliceVariables[i],&sliceVariables[i])
}
```

###### 2. 通过for..range形式遍历

```go  
for key,value := range sliceVariables {
   fmt.Println(key,value)
}
```

##### 六、切片的追加append

> 1.创建一个初始元素个数为 5 的数组切片，元素初始值为 0，并`预留 6 个元素`的存储空间

```go 
var sliceVariables3 []int = make([]int,5,6)

fmt.Printf("sliceVariables3的长度=%d,容量=%d,\n切片指向的底层数组的地址=%p,切片自己的地址=%p\n",len(sliceVariables3),cap(sliceVariables3),sliceVariables3,&sliceVariables3)

```

> 2.第一次追加切片的容量`达到预设的最大容量`

```go 
sliceVariables3 = append(sliceVariables3,7)

fmt.Printf("第一次追加sliceVariables3的长度=%d,容量=%d,\n切片指向的底层数组的地址=%p,切片自己的地址=%p\n",len(sliceVariables3),cap(sliceVariables3),sliceVariables3,&sliceVariables3)

```

> 3.第一次追加切片的容量超出了预设的最大值，go语言会新申请一块内存将原始数据拷贝到新的内存当中，切片指向新的内存空间

```go 
sliceVariables3 = append(sliceVariables3,8)
fmt.Printf("第二次追加sliceVariables3的长度=%d,容量=%d,\n切片指向的底层数组的地址=%p,切片自己的地址=%p\n",len(sliceVariables3),cap(sliceVariables3),sliceVariables3,&sliceVariables3)
```

> append 追加可以同时追加多个元素 并且函数函数返回值 必须有一个变量接收

> 4.合并两个切片
 
```go 
sliceVariables3 = append(sliceVariables3, sliceVariables2...)
```
> 第二个切片后面加上`...`即可完成合并

##### 七、切片的拷贝

> 1.定义源切片

```go 
sliceVariables4 := []int{1,2,3,4,5}
```

> 2.定义目标切片

```go 
sliceVariables5 := make([]int,10)
```

> 3.目标切片，源切片，拷贝的数量以两个切片中最小切片的长度为准，copy(目标切片，源切片)

```go
copy(sliceVariables5,sliceVariables4)

fmt.Println(sliceVariables4)

fmt.Println(sliceVariables5)
```

> 注意一个坑：如果目标切片的长度为0 则拷贝得到的长度也为0


##### 八、切片做为函数参数

###### 切片是引用类型

切片做为参数传递给函数的意义重大，同数组，当传递较大的数组切片时可以有效的提升cpu执行效率

```go 
func changeSlice(slice []int) {
   slice[0] = 100
}

// 切片是引用类型
fmt.Println(sliceVariables5)
changeSlice(sliceVariables5)
fmt.Println(sliceVariables5)
```

##### 九、切片的原理

###### 现象一：copy的时候目的切片长度为0的时候无法拷贝成功

```go
a := make([]int, 0)
b := []int{1, 2, 3}
fmt.Println(copy(a, b))
fmt.Println(a)
```
这是因为拷贝不会扩容目的切片 如果目的切片长度为0 则拷贝失败


###### 现象二：切片操作之后获得的新的切片的修改会影响到老的切片

```go  
b := []int{1, 2, 3}
c := b[:]
c[0] = 8
```

这是因为底层指向的是同一块内存 

###### 现象三：append操作未能影响到之前的切片

```go  
b := []int{1, 2, 3}
c := b[:]
b = append(b, 9)

fmt.Println(b)
fmt.Println(c)

c[0] = 8

fmt.Println(b)
fmt.Println(c)

// [1 2 3]
// [1 2 3 9]
// [1 2 3]
// [8 2 3 9]
```

这是因为产生了扩容机制 扩容机制一旦产生 切片就会指向新的内存地址（数据发生拷贝）

###### 现象四：切片的长度和容量 的意义

```go  
fmt.Println(len(c))
fmt.Println(cap(c))

// 4
// 6

```

这是因为实际申请内存和当前使用内存的场景进行区分


##### 九、切片注意事项

> 1.切片是引用类型，切片操作之后获得新的切片修改会影响到之前的切片（更像是一个指针，本身不存值）
> 
> 2.切片做为参数传递给函数的意义重大，同数组，当传递较大的数组切片时可以有效的提升cpu执行效率
> 
> 3.`new` 用于各种类型的内存分配 返回的是指针,`var variables = new(T)` 返回的是`T`，指针类型，即返回的是一个地址，默认是当前类型的零值，它返回了一个指针，指向新分配的类型` T `的零值。也就是说`new `返回指针。
> 
> 4.`make` 用来分配内存主要用来分配引用类型，比如`channel` ,`map` ,`slice`，返回一个有初始值 (非零) 的 `T `类型，不是`T`类型
