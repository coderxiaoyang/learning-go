### go语言最基本的数据类型转换

首先注意一点：go语言不支持变量间的隐式类型转换但是支持常量到变量的隐式类型转换

```go 
var b int = 5.0 //5.0 为常量   常量到变量是会进行隐式转换
fmt.Println(b)
```

但是并不是所有的常量都能进行隐式类型转换 比如上面的`5.0`改为 `5.1`则会报错


#### 简单的转换操作

``` 
valueOfTypeB = typeB(valueOfTypeA)
```

Go允许在底层结构相同的两个类型之间互转


#### strconv

###### Itoa 和 Atoi

**int转string**

`strconv` 语法

```go
str := strconv.Itoa(intvar)
```

`fmt` 语法

```go 
str := fmt.Sprintf("%d", intvar)
```

**string转int**


`Atoi`语法

```go 
i, err := strconv.Atoi(str)
```
`strconv.Atoi` 函数，返回两个参数，第一个参数是转换后的字符串。第二个参数是，如果转换失败，返回的失败信息。


`ParseInt`语法

```go 
i, err := strconv.ParseInt(str, 10, 64)
```

返回值 : `strconv.ParseInt` 函数，返回两个参数，第一个参数是转换后的字符串。第二个参数是，如果转换失败，返回的失败信息。

说明 `strconv.ParseInt` 函数的第二个参数，表示要转换的字符串的进制，第三个参数，接受转换成数字型的位数。

当第三个参数为0的时候，表示转为换`int` 当第二个参数为0的时候 根据字符串的前缀来判断以什么类型去解析


###### Parse类函数

Parse类函数用于转换字符串为给定类型的值: `ParseBool`、`ParseFloat`、`ParseInt`、`ParseUint`

```go  
b, err := strconv.ParseBool("true")
fmt.Println("b: ", b, "err: ", err)
b:  true err:  <nil>

f, err := strconv.ParseFloat("3.1415", 64)
fmt.Println("f: ", f, "err: ", err)
f:  3.1415 err:  <nil>

i, err := strconv.ParseInt("-42", 10, 64)
fmt.Println("i: ", i, "err: ", err)
i:  -42 err:  <nil>

u, err := strconv.ParseUint("42", 10, 64)
fmt.Println("u: ", u, "err: ", err)
u:  42 err:  <nil>
```

###### Format类函数

将给定类型格式化为 string类型： `FormatBool`、`FormatFloat`、`FormatInt`、`FormatUint`

```go 
boolS := strconv.FormatBool(true)
fmt.Println("boolS: ", boolS)
boolS:  true

floatS := strconv.FormatFloat(3.1415, 'E', -1, 64)
fmt.Println("floatS: ", floatS)
floatS:  3.1415E+00

intS := strconv.FormatInt(-42, 16)
fmt.Println("intS: ", intS)
intS:  -2a

uintS := strconv.FormatUint(42, 16)
fmt.Println("uintS: ", uintS)
uintS:  2a

```

``` 
func FormatFloat(f float64, fmt byte, prec, bitSize int) string
```

`FormatFloat`根据格式`fmt`和`precision prec`将浮点数f转换为字符串。假设原始结果是从`bitSize`位的浮点值(float32为32，float64为64)获得的，则对结果进行四舍五入。

格式fmt是'b'（-ddddp±ddd，二进制指数），'e'（-d.dddde±dd，十进制指数），'E'（-d.ddddE±dd，十进制指数）之一 ），'f'（-ddd.dddd，无指数），'g'（大指数为'e'，否则为'f'），'G'（大指数为'E'，否则为'f'），' x'（-0xd.ddddp±ddd，十六进制分数和二进制指数）或“ X”（-0Xd.ddddP±ddd，十六进制分数和二进制指数）。

精度精度控制'e'，'E'，'f'，'g'，'G'，'x'和'X'格式打印的位数(不包括 index )。对于'e'，'E'，'f'，'x'和'X'，它是小数点后的位数。对于'g'和'G'，它是有效数字的最大数量(删除零位)。特殊精度-1使用必要的最小位数，以使ParseFloat准确返回f。

注意：strconv.FormatFloat 函数的第一个参数，只能接受 float64 类型的变量。因此，如果需要将 float32 类型转成 string，需要先将 float32 转成 float64。

