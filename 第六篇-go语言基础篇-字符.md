### 字符

##### 一、什么是字符

字符是电子计算机中字母、数字、符号的统称，是数据结构中最小的数据存取单位，通常由8个二进制位(一个字节)来表示一个字符。

##### 二、作用与应用场景

用于存储单个字符

##### 三、字符表示类型

> byte(uint8) byte 类型是 uint8 的别名
>
> rune 类型，代表一个 UTF-8 字符，当需要处理中文、日文或者其他复合字符时，则需要用到 rune 类型，rune 类型是 int32 类型的别名

##### 四、申请变量的方法

```go 

var charVariables1 byte = '0'

charVariables2 := '波'
fmt.Printf("charVariables1 = %d,charVariables2=%d\n",charVariables1,charVariables2)
fmt.Printf("charVariables2=%c，charVariables2=%T\n",charVariables2,charVariables2)

//charVariables1 = 48,charVariables2=27874
//charVariables2=波，charVariables2=int32

// 中文不能使用 byte 应该用 rune
//var charVariables3 byte = '波'
//fmt.Printf("charVariables3=%c，charVariables3=%T\n",charVariables3,charVariables3)


variables := 'a' //对应的编码值
fmt.Printf("加和=%d,a的编码值=%d\n",100+variables,variables)


a := 'a'
//这里注意一下 1. a+1可以和数字计算 2.a+1的类型是32 3. int类型可以直接变成字符
fmt.Printf("%T\n", a+1)
fmt.Printf("a+1=%c", a+1)
```

##### 五、注意事项

> 1.单引号括起来
> 
> 2.存储：字符-》ascii码值-》二进制
> 
> 3.读取：二进制-》ascii码值-》字符
> 
> 4.一个字符占一个字节，一个中文占3个字节
> 
> 5.字符可以与整数进行算术运算(转成ascii码值再计算)

