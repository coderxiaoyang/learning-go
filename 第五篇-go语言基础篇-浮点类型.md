### 浮点类型

##### 一、浮点引入

当我们需要存储带有小数点的数据类型时需要使用浮点类型

##### 二、浮点概述

用于存储带有小数位的数据类型

##### 三、作用与应用场景

**作用**

当我们需要存储带有小数点的数据类型时需要使用浮点类型

**应用场景**

比如在微信小程序支付时，这里支付的金额，可能是1888.856元，这个时候就需要使用浮点类型

##### 四、主要类型

> 单精度 float32
> 双精度 float64

##### 五、申请变量的方法

###### 1. 十进制的形式来展示

```go 
var floatVariables1 float32 = 3.1415926
floatVariables2 := .1416926 //0.1416926
fmt.Printf("floatVariables1的类型=%T,占用的字节大小=%d\n",floatVariables1,unsafe.Sizeof(floatVariables1))
fmt.Printf("floatVariables2的类型=%T,占用的字节大小=%d\n",floatVariables2,unsafe.Sizeof(floatVariables2))
```

###### 2. 科学计数法来展示

```go 
floatVariables3 := 3.1415926e2 //3.1415926乘以10的2次方
floatVariables4 := 3.1415926e-2 //3.1415926除以10的2次方
fmt.Println(floatVariables3,floatVariables4)
```

###### 3. 不同精度的浮点类型的转换

```go 
var floatVariables5 float32 = 3.14
var floatVariables6 float64 = 3.14
floatVariables6 = float64(floatVariables5)
floatVariables6 = floatVariables6
```

###### 4. 最大值

```go
fmt.Println("float64max:", math.MaxFloat64)
fmt.Println("float32max:", math.MaxFloat32)
//float64max 1.7976931348623157e+308
//float32max 3.4028234663852886e+38
、
```

###### 5. 默认的类型 float64

```go 
weight := 71.2

fmt.Printf("%T", weight)

// float64
```
##### 六、复数

> 实数+虚数i
> complex64(32 位实数 + 32 位虚数+i虚数单位)
> complex128（默认）(64 位实数 + 64 位虚数+i虚数单位)


###### 申请方式

```go 
var complexVariables1 complex64
complexVariables1 = 3.14+12i
complexVariables2 := complex(3.14,12)

fmt.Printf("complexVariables1的类型=%T,值=%v\n",complexVariables1,complexVariables1)
fmt.Printf("complexVariables2的类型=%T,值=%v\n",complexVariables2,complexVariables2)

//打印复数的实数部分与虚数部分
fmt.Println(real(complexVariables1),imag(complexVariables1))
```

##### 七、注意事项

> 1.默认数据类型为float64
> 
> 2.float32,float64由于占用空间大小不一样，被认为是不同的类型
> 
> 3.不同类型的转换
> 
> 4.单精度，双精度类型转换的精度损失与溢出
