### 常量

##### 一、常量引入

当程序中需要引入一个在整个程序运行期，数据不发生改变时，使用常量

##### 二、常量概述（什么是常量）

就是在程序运行期不可以改变的变量

##### 三、Go 语言预定义常量

```go
true
false
iota
```

##### 四、常量的作用与应用场景

###### 1. 作用

全局唯一，编译期就已经确定的值，提高程序执行效率

即使存在未使用的常量，在编译的时候也不会报错（这个和变量不一样）

###### 2. 应用场景

当程序中需要引入一个在整个程序运行期，数据不发生改变时，使用常量。

iota 常量计数器

另一个就是枚举数据时使用，如下：

```go 
const (
   Monday = iota
   Tuesday
   Wednesday
   Thursday
   Friday
   Saturday
   Sunday
)
```

##### 五、申请常量的几个方法

###### 1. const constVariables 变量类型 = 变量值

```go
const constVariables1 float64 = 3.1415926 
```

###### 2. 一次申明多个值

```go 
const constVariables2,constVariables3 = 100,"小杨"
```

###### 3. const （…）

```go 
const (
   iotaVariables1 = iota  //0
   iotaVariables2 = iota  //1
   iotaVariables3 = iota  //2
)
```

###### 4. 单独赋值

```go 
const iotaVariables4 = iota //0
```

###### 5. const 指定第一个iota,其余自动递增

```go 
const (
   iotaVariables5 = iota //0 iota 执行完之后 +1 
   iotaVariables6      //1
   iotaVariables7      //2
)
```

###### 6. 枚举一周的日期

```go 
const (
   Monday = iota
   Tuesday
   Wednesday
   Thursday
   Friday
   Saturday
   Sunday
)
```

###### 7. 同一行定义

```go 
const(
   iotaVariables8,iotaVariables9,iotaVariables10 = iota,iota,iota
)
```

###### 8. const中iota与iota之间跳过

> 从第一行开始，iota从0逐行加一

```go 
const (
   iotaVariables11 = iota //0
   iotaVariables12 = "Bobo" // iota = 1 每一行iota 加一
   iotaVariables13 = iota //2
)
```

###### 9. 常量组如不指定类型和初始化值，该类型和值和上一行的类型一致

```go 
const (
	x int = 16
	y // 该常量的值等于上一个常量的表达式
	s = "abc"
	z
)
fmt.Println(x,y,s,z)

// 输出
// 16 16 abc abc
```

###### 10. 你真的懂 iota 了吗？

1. iota只能在常量组中使用

2. 不同的const定义块互相不干扰

```go 
const (
	Book = iota //计算器
	Cloth //行
	Phone
	DeskTop
)

const (
	Monday = iota
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturday
	Sunday
)

print(Book, Cloth, Monday, Tuesday)

// 输出
// 0101
```
3. 没有表达式的常量定义复用上一行的表达式

4. 从第一行开始，iota从0逐行加一

```go 
const (
	a = iota //iota  = 0
	b = 10 //iota = 1 每一行iota加一
	c // c=10 , iota = 2
	d,e = iota, iota //iota=3
	f = iota //iota=4
	
	//iota代表的是这里的行数
)
```

##### 六、注意事项

> 1.通过const关键字来申请
> 
> 2.常量是指编译期间就明确知道的值并且不可改变
> 
> 3.iota特殊，iota在每个const出现时被重置为0
