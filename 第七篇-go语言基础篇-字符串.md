
### 字符串

##### 一、字符串概述

字符串是的类型标识为string, 由数字、字母、下划线组成的一串字符。 在编程语言中用于表示文本的数据类型。

##### 二、申请变量的方法

###### 1. 申明并赋值

```go 
var stringVariables1 string
stringVariables1 = "hello 小杨\n"
```

###### 2. 通过反引号定义，原样输出

```go 
var stringVariables2 = `
package main 
import (
   "fmt"
   "unsafe"
)

func main() {
   /*
   float32 4个字节
   float64 8个字节
    */
   。。。。。。。
   fmt.Println(real(complexVariables1),imag(complexVariables1))
}
   `
```

###### 3. 获取字符串长度

```go 
var stringVariables3 = "hello imooc,我是小杨"
stringVariables3Len := len(stringVariables3)
```
> len 返回的是字节的长度 中文使用Unicode 字符集 utf-8 是一个动态的 编码  

```go 
var stringVariables3 = "hello imooc,我是小杨"

stringArr := []rune(stringVariables3)

fmt.Println(stringArr)

// [104 101 108 108 111 32 105 109 111 111 99 44 25105 26159 23567 26472]

fmt.Println(len(stringArr))

// 16
```
> rune 实际是 int32 将单个英文字符变成rune 增加了三个字节  将中文变成rune 增加了一个字节 这么求取长度比较浪费空间


###### 4. 字符串中有特殊的转义符

go语言的转义字符和其他语言保持一致 一个特殊注意点如下：

```go
date = `2021\08\19`
fmt.Println(date) 
```

###### 5. 字符串常用方法

```go 
var name string = "hello imooc,我是小杨"

fmt.Println(strings.Contains(name, "小杨")) // 是否包含某个子串

fmt.Println(strings.Index(name, "小杨"))  // 子串所在的位置

fmt.Println(strings.Count(name, "o")) // 某个字符出现的次数

fmt.Println(strings.HasPrefix(name, "h")) // 是否以某个字符开头

fmt.Println(strings.HasSuffix(name, "杨")) // 是否以某个字符结尾

fmt.Println(strings.ToUpper("name")) // 将小写字符串转为大写

fmt.Println(strings.ToLower("NAME")) // 将大写字符串转为小写

fmt.Println(strings.Compare("a", "b")) // 比较两个字符串 字符的比较 其实比较的是字符对用的ASCII码 小于返回 -1 大于返回 1 等于返回 0

fmt.Println(strings.TrimSpace(" name ")) // 去除左右两边空格

fmt.Println(strings.Trim("namen", "n")) // 去除左右两边指定字符

fmt.Println(strings.TrimLeft("name", "n")) // 去除左边的字符

fmt.Println(strings.Split("hello world", " ")) // 指定分隔符分割字符串 

arr := strings.Split("hello world", " ")

fmt.Println(strings.Join(arr, ",")) // 指定拼接符 拼接数组

fmt.Println(strings.Replace("age: 18 phone: 18358580984", "18", "19", 1)) // 替换字符串 可设置替换次数


desc := fmt.Sprintf("name: %s, age: %d\n", name, age) // 使用 Sprintf 将格式化后的字符串 赋值 供后续使用

fmt.Printf("desc: %s", desc)


var name string

var age int

fmt.Scanln(&name, &age) // 使用 Scanln 获取键盘输入

fmt.Println(name, age)


fmt.Scanf("%s %d", &name, &age) // 使用 Scanf 对输入进行格式化

fmt.Println(name, age)
```


> 我们可以使用 ` 包裹字符串 里面包含特殊转义字符 或者任意字符

##### 三、字符串遍历

###### 1. 通过 for index := 0; index < stringVariables3Len;index++ {} 形式来遍历

```go 
for index := 0; index < stringVariables3Len;index++ {
   //这种情况如果是中文，则会有编码问题
   fmt.Printf("%s-编码值=%d,值=%c,类型=%T\n",stringVariables3,stringVariables3[index],stringVariables3[index],stringVariables3[index])
}
```

###### 2. 通过for..range 形式来遍历

```go 
// 通过range，在这种情况下中文就不会有问题，按照rune类型来打印
for index, val := range stringVariables3 {
   fmt.Printf("通过for index ... %s--索引:%d--字符值:%c--字符值类型;%T\n", stringVariables5, index, val, val) //val 的类型为 rune，即int32
}
```

##### 四、参照字符类型

> 1.byte(uint8) byte 类型是 uint8 的别名
> 2.rune 类型，代表一个 UTF-8 字符，当需要处理中文、日文或者其他复合字符时，则需要用到 rune 类型，rune 类型是 int32 类型的别名

##### 五、注意事项

> 1.双引号括起来
> 
> 2.`` 反引号原样输出
> 
> 3.字符串初始化之后不允许重新赋值
> 
> 4.可以以字符数组下标的形式来读取，但不能赋值
> 
> 5.len获取字符串长度
> 
> 6.字符串相加即是拼接

##### 六、面试

> 1.go语言中如何遍历字符串中有中文的情况？
> 
> for循环遍历值的类型为uint8
> 
> 2.for循环的遍历与for….range的区别
> 
> for...range遍历值的类型为int32
