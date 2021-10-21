### 指针

##### 一、指针概述

程序在内存中存储它的值，每个内存块都 有一个地址，而存储这个地址的变量被称为指针变量，指针。
通常用十六进制数表示，如：`0x6b0820` 或 `0xf84001d7f0`

一个指针变量可以指向任何一个值的内存地址 它指向那个值的内存地址，在 32 位机器上占用 4 个字节，在 64 位机器上占用 8 个字节，并且与它所指向的值的大小无关。
指针可以指向任何类型的值，但是使用时指定指针的类型在实际编码中具有重要意义；
在指针类型前面加上` * `号来获取指针所指向的内容。
使用一个指针引用一个值被称为间接引用。

> 1.主要用于管理内存
> 
> 2.指针是一个特殊的变量
> 
> 3.存储的是另一个变量的内存地址

[![s2lhBn.png](https://s3.ax1x.com/2021/01/19/s2lhBn.png)](https://imgchr.com/i/s2lhBn)


```go 
a := 10
var ip *int // 定义一个指针变量 这个变量里面就只能保存地址类型为 int的值 （这个类型是必须设置的，如果不设置在通过指针去取值的时候不知道设置多大的连续内存空间）

ip = &a  // 获取变量a的地址

*ip = 30  // 修改指针指向的变量的值
fmt.Println(a)

fmt.Printf("ip所指向的内存空间地址是：%p, 内存中的值是: %d\n", ip, *ip)
```

通过指针交换两个变量的值

```go  
func swap(a *int, b *int) {
	c := *a
	*a = *b
	*b = c
}

a := 10
b := 20

swap(&a, &b)
fmt.Println(a, b)
```

指针还可以指向数组 指向数组的指针 数组是值类型

```go 
arr := [3]int{1, 2, 3}
var arrPointer *[3]int = &arr
fmt.Println(arrPointer)
```

指针数组

```go 
var ptrs [3]*int // 创建能够存放三个指针变量的数组
```

> 很多时候都是函数参数的时候指明的类型


指针的默认值是nil

```go 
arr := [3]int{1, 2, 3}
var ip *[3]int = &arr
if ip != nil {
	fmt.Println("指针不为空")
}
```

> go语言的 nil 更多的时候用于判断指针是否为空


###### go语言的指针和C语言的指针有什么区别？

像python和java这种语言都是极力的屏蔽指针， c/c++ 都提供了指针 指针本身是很强大
c和c++中指针的功能很强大 指针的转换 指针的偏移 指针的运算
go语言没有屏蔽指针，但是go语言在指针上做了大量的限制，安全性高很多，相比较 c和c++灵活性就降低了

###### make 和 new 函数

如果我们在声明一个指针之后没有初始化指针而是直接进行更改指针指向的值 则会报错
```go
var p *int // 申明了一个变量p 但是变量没有初始值 没有内存
*p = 10

// panic: runtime error: invalid memory address or nil pointer dereference
```

但是如果我们对 `int` 类型的进行赋值不会报错

```go  
var a int
a = 10
fmt.Println(a)
```
这是因为默认值的原因

> 默认值 int byte rune float bool string 这些类型都有默认值 
> 我们在定义上述类型的时候 会分配内存空间 并将对应的默认值放到内存空间中 
> 
> 指针， 切片 map， 接口这些默认值是nil 理解为none

那么对于指针来说或者说其他的默认值是0的情况来说 如何一开始申明的时候就分配内存？
可以通过`new`函数进行初始化申请内存空间
```go  
var p *int = new(int) // go的编译器先申请一个内存空间，这里的内存中的值全部设置为0
*p = 10

```

除了new函数可以申请内存以外 还有一个函数就是make,更加常用的是make函数， make函数一般用于切片 map
```go 
var info map[string]string = make(map[string]string)
info["c"] = "Coder"
```

> new函数返回的是这个值的地址 make函数返回的是指定类型的实例


###### nil的一些细节

```go  
var info2 map[string]string
if info2 == nil {
	fmt.Println("map的默认值是 nil")
}

var slice []string
if slice == nil {
	fmt.Println("slice的默认值是 nil")
}

var err error
if err == nil {
	fmt.Println("error的默认值是 nil")
}

// python中的None和go语言中的nil类型不一样，None是全局唯一的
// go语言中 nil是唯一可以用来表示部分类型的零值的标识符（并不是实际对象）， 它可以代表许多不同内存布局的值
fmt.Println(unsafe.Sizeof(slice), unsafe.Sizeof(info2))
```

##### 二、指针的作用及应用场景

`作用`

节省内存空间，提高执行效率（当操作的数据量较大规模时）
访问变量的值

`应用场景`

修改变量的值
访问变量的值

##### 三、指针变量操作

先定义变量，再定义指针去取变量的地址，那么这个变量存储的就是地址，实际的值是这个地址指向的空间

```go 
//先定义变量，再定义指针去取变量的地址
var intVariables int = 100
fmt.Printf("intVariables的值=%d,地址=%v\n",intVariables,&intVariables)

//定义一个指针类型的变量，那么这个变量存储的就是地址，实际的值是这个地址指向的空间
//定义一个指针类型的变量去指向 intVariables
var pointerVariables *int = &intVariables
fmt.Printf("pointerVariables的值=%v,地址=%v\n",pointerVariables,&pointerVariables)
```

> 取地址操作符 `&`，一般在指针操作当中经常使用，`&` 取出地址
> 
> 取值操作符 `*`，一般在指针操作当中，`*` 根据地址找该地址指向空间的值。

###### 指针变量的内存分析

```go 
/*
   变量名             变量值           地址
   intVariables      100              0xc00006c008
   pointerVariables    0xc00006e008    0xc00007c020
*/
```

###### 取变量值的方式

> 1.直接通过变量名,variables
> 
> 2.通过指针间接访问(指针名),pointerVariables

##### 四、注意事项

> 1.值类型一般都有对应的指针类型，格式 数据的类型 比如int->int float64->*float64
> 
> 2.0x开头的十六进制的一组数据
> 
> 3.go语言中引用类型有哪些？指针，`slice`，`map`，`chan`,`interface`
> 
> 4.值类型：变量存储的值是值类型，通常在栈中分配
> 
> 5.引用类型：变量存储的是地址，这个地址对应的空间存储的才是实际的值，一般引用类型在堆中分配，一旦无任何变量来引用这块空间，那么会被操作系统进行垃圾回收。
> 
> 6.go语言的指针没有指针运算，因为指针的乱引用会导致的内存泄漏，以及引发一连串程序的崩溃
> 
> 7.修改指针指向变量的值,但是不会修改地址，通过指针改掉了指向变量对应的值
> 
> 8.一个指针变量可以指向任何一个值的内存地址
> 
> 9.指针也可以指向另一个指针，并且可以进行任意深度的嵌套，导致你可以有多级的间接引用，但在大多数情况这会使你的代码结构不清晰
> 
> 10.当一个指针被定义后没有分配到任何变量时，它的值为 nil。对一个空指针的反向引用是不合法的，并且会使程序崩溃
