### 数值类型

##### 一、数值引入

我们要存储一个人的年龄，就需要用到数值类型

##### 二、数值类型概述

用于定义整数类型变量的标识符

##### 三、作用与应用场景

在计算机当中存储数值类型

##### 四、申请整型变量的方法

```go
var intVariables1 = 100 //int
intVariables2 := 200 //int
var intVariables3 int32 //int32
intVariables := 126 //int

//类型转换
intVariables3 = int32(intVariables)

//指定类型
var intVariables4 int64 = 123456789
fmt.Printf("intVariables1=%T,intVariables2=%T,intVariables3=%T\n",intVariables1,intVariables2,intVariables3)

//引入unsafe包，打印占据的空间大小，即字节大小
fmt.Println(unsafe.Sizeof(intVariables4))
```

##### 五、不同数值类型与占用的空间

###### 1. 有符号位

```go  
int8 数据范围：-2^7到2^7-1
int16 数据范围：-2^15到2^15-1
int32 数据范围：-2^31到2^31-1
int64 数据范围：-2^63到2^63-1
```

###### 2. 无符号位

```go 
uint8 数据范围：0到2^8-1
uint16 数据范围：0到2^16-1
uint32 数据范围：0到2^32-1
uint64 数据范围：0到2^64-1
```
###### 3. 相比python而言，go语言为什么有这么多种整数类型

年龄，分数都是有上线 年龄不超过0-200 分数0-150

很多场景之下，数字有上限，我们可以选择合适的数据类型来降低内存的占用

对于Python来说`int`占用字节是动态的，我们使用的时候不用担心超过上限，而go存在超过上限的可能
```python
In [8]: import sys

In [9]: age = 18

In [10]: sys.getsizeof(age)
Out[10]: 28
```

```go 
func main() {

	var age int = 18
	fmt.Println("age", unsafe.Sizeof(age))

}

// intScore 8

```

##### 六、注意事项

> 1. 默认数据类型为int
> 2. int32,int64由于占用空间大小不一样，被认为是不同的类型，所有不能相互赋值
> 3. 不同类型的转换
> 4. 占用字节 unsafe.Sizeof(intVariables)
> 5. 注意使用时数据可能溢出与损失的问题
> 6. Go 语言的 int 类型的具体大小是跟机器的 CPU 位数相关的。如果 CPU 是 32 位的，那么 int 就占 4 字节，如果 CPU 是 64 位的，那么 int 就占 8 字节。
