
### 函数

##### 一、函数的概述

函数是什么，在其他编程语言都有函数 为了完成某个具体功能封装起来的功能的集合

##### 二、函数的定义

```go 
//函数定义格式
func 函数名(参数) (返回值) {
    //函数体
    return xxx
}

func funcName(input1 type1, input2 type2) (output1 type1, output2 type2) {
    // 这里是处理逻辑代码
    // 返回多个值
    return value1, value2
 }
```

返回变量函数内部定义
```go  
func add(a, b int) int {
	var sum int
	sum = a + b
	return sum
}
```

返回变量在返回值部分定义 内部无需再定义
```go 
func add2(a, b int) (sum int) {
	sum = a + b
	return sum
}
```

当返回值部分定义了返回参数 函数体内部可以不用显式的 return
```go  
func add3(a, b int) (sum int) {
	sum = a + b
	return
}
```


支持多值返回
```go  
func div(a, b int) (int, error) {
	var result int
	var err error
	if b == 0 {
		err = errors.New("被除数不能为0")
	}else{
		result = a / b
	}
	return result, err
}
```

多值返回当在返回值部分定义了返回值 同样可以省略 return 

```go  
func div2(a, b int) (result int,err error) {
	if b == 0 {
		err = errors.New("被除数不能为0")
	}else{
		result = a / b
	}

	return
}
```


> 1.函数的定义及左大括号放在同一行
> 
> 2.多返回值
> 
> 3.忽略函数返回值 `_`


##### 三、多返回实例演示

> 实例: 定义一个函数[actionVariables]，返回2个返回值，加和，相减，测试多返回值

```go
func actionVariables(var1 int,var2 int)(int,int) {

    return var1 + var2,var1-var2
}
```

函数的参数如果一致，则可在最后一个参数中书写类型

函数返回值名称可指定，可忽略

指定函数返回值名称时，可直接return


> 如果2个参数或多个参数的类型一致，可把类型写在最后

```go 
func actionVariables(var1,var2 int)(int,int) {
    return var1 + var2,var1-var2
}
```

> 函数调用

```go  
var sum int
var minus int

sum,minus = actionVariables(1,2)
fmt.Println(sum,minus)
```

> 实例: 定义一个函数[actionVariables2]，测试函数多返回值时，设置有名与无名

```go 
func actionVariables2(var1,var2 int) (sum int,minus int) {
    sum = var1 + var2
    minus = var1 - var2
    //return sum,minus
    return
}
```

> 函数调用

```go  
sum,minus = actionVariables2(1,2)
fmt.Println(sum,minus)
```

##### 三、不定参数

> 可变参数…interface{}做为函数参数的使用,通过分析go系统自带的fmt.println讲解可变参数

```go 
// 1. 定义函数[actionVariables3]，可变参数，测试

func actionVariables3(args ...interface{}) {
    for _,value := range args {
        fmt.Println(key,value)
    }
}

// 2. 调用

actionVariables3(1,"我是小杨",3.14,true)
```

```go
// 通过省略号 来指定不定长参数
func add(params ...int) (sum int){
	for _, v := range params {
		sum += v
	}
	params[0] = 9
	return
}

slice := []int{1,2,3,4,5}
fmt.Println(add(slice...)) // 通过省略号 将slice打散 这样就是值拷贝 不再影响原来的切片
fmt.Println(slice)
```

> 可变参数，类似于切片，go语言里有很多，可变参数是go语言的特性
> 
> Golang 中函数的可变参数，必须是函数的最后一个参数，使用的形式是 ...。


```go 
//可变参数...interface{}做为函数参数的类型判断
//1.在函数[actionVariables3],增加功能，判断可变参数的类型

func actionVariables3(args ...interface{}) {
    for _,value := range args {
        switch value.(type) {
        case int:
            fmt.Println(value,"int")
        case string:
            fmt.Println(value,"string")
        case float64:
            fmt.Println(value,"float64")
        case bool:
            fmt.Println(value,"bool")
        default:
            fmt.Println(value,"unknow")
        }
    }
}

//2.调用
actionVariables3(1,"我是小杨",3.14,true)
```

##### 四、函数的作用域

函数体内部定义的变量称为局部变量，作用域只在函数体当中有效，函数调用完成之后，为这些变量定义的内存空间就会被释放

```go  
//1.定义函数[testScope],分析函数的作用域

func testScope(args int) {
    args = 100
    fmt.Println(args)
}

//2.调用

var args int
testScope(args)
fmt.Println(args)
```

> `_` 在函数中的使用，用于忽略返回值

```go  
sum, _ = actionVariables(3,5)
```

##### 五、递归函数

什么是递归函数 在一个循环体当中执行，必须有一个条件可以在满足某个条件时终止当前的循环，否则为死循环

> 递归函数举例: 
> 一般以斐波那契数列举例

斐波那契数列 特点 1 2 3 ...

```go 
/*
    位置        值
    1        1
    2        1
    3        2
    4        3
    ..        ..
    n        func(n-1) + func(n-2)
    */
```

传递一个位置，返回当前位置的值

```go
- 1.定义函数[fibonaci]实现

func fibonaci(index int64) int64 {
    if index == 1 || index == 2 {
        return 1
    } else {
        return fibonaci(index-1) + fibonaci(index-2)
    }
}

- 2.调用

//第20位置对应的值
fmt.Println(fibonaci(2))
```

##### 六、匿名函数与闭包

什么是匿名函数 没有名字的函数称为匿名函数

`应用场景`

当一个函数仅使用一次的时候，可定义为匿名函数

###### 匿名函数的使用方式

> 使用方式一:  定义一个匿名函数实现两个数的加和，定义的时候并调用

```go 
sumVariables := func(var1,var2 int) int {
        return var1 + var2
    }(1,2)
    
fmt.Println(sumVariables)
```

> 使用方式二: 定义一个变量，将匿名函数赋值给该变量，则变量具体匿名函数的功能

```go  
- //将一个匿名函数赋值给一个变量，这个变量存储的是这个匿名的地址

func1 := func(var1,var2 int) int{
    return var1 - var2
}
fmt.Printf("func1调用的值=%d,func1=%p\n",func1(1,2),&func1)

// func1存储的是函数的地址
// 输出 func1调用的值=-1,func1=0xc000130018
```

`匿名函数是不能独立存在的`

```go
//报错
func(var1,var2 int)int{return 1} 

//或者我们直接调用 
func(var1,var2 int)int{return 1}(1,2)
```


###### 闭包

首先他是一个函数，是函数与函数外部数据的引用（在函数体内部引用到了函数体外部的数据）

> 实例演示 以一个例子来定义一个闭包，该闭包函数每调用一次，让值加1

```go  
func clousure() func(int64) int64 {
     var step int64 = 0
     return func(_step int64) int64 {
          //函数内部引用函数外部的数据
       step +=  _step
       return step
     }
}

func2 := clousure()

fmt.Println(func2(1))
fmt.Println(func2(2))
fmt.Println(func2(3))
```

`分析执行结果`

闭包函数每次调用，均保留上次的执行结果，并参与下一次调用时候的运算

> 同一个包中，函数不支持重载即不能有相同的函数名

##### 七、defer

在我们实际的开发过程中，我们经常去连接一个远程的数据库，或者说连接本地的数据库，在我们连接数据库之后呢，就会返回连接的句柄，这种情况我们就占用了这个资源，假如我们程序执行的时候还没来得及关键关闭这些资源的时候，我们的程序就崩溃了，但是对这个资源的占用还一种存在着，我们如何解决，以此我们引出defer关键字
 
`defer` 关键字允许我们将业务逻辑延迟到函数返回之前才执行某个语句或函数

关键字` defer` 的用法类似于面向对象编程语言` Java` 和` C# `的` finally `语句块，它一般用于释放某些已分配的资源。

`使用场景`

常用于资源的回收

> 定义一个函数来演示defer的使用，该功能用于模拟打开一个文件

```go 
func deferAction() {
    handle,err := os.Open("hello.txt")
    // 这里是一大堆的文件操作程序...
    // 也有可能出现异常
    // fmt.Println(5/0)
    err = err
    handle.close()
}
```

由于程序的错误，导致后面的代码无法执行，导致打开的资源一直被占用着，则其他程序无法使用

> 对代码优化

```go 
func deferAction() {
    handle,err := os.Open("hello.txt")
    defer handle.Close()

    //这里是一大堆的文件操作程序...
    //fmt.Println(5/0)
    err = err 
}
```

> 先进后出原则

```go
func deferAction2(var1,var2 int) int {
     defer fmt.Println(var1)     // 1
     defer fmt.Println(var2)    // 2

     sum := var1 + var2
     defer fmt.Println(sum)    // 3

     return sum
} 

//defer
deferAction2(1,2)
```

> defer之后只能是函数调用 不能是表达式 比如： a++


```go
test := func() {
	fmt.Println("test1")
}

defer test()

test = func() {
	fmt.Println("test2")
}

fmt.Println("test3")

// 输出
test3
test1

// 在执行defer之后 将整个函数内容压入到待执行栈中
```

```go
x := 10
defer func(a int) {
	fmt.Println(a)
}(x)

x++

// 输出 
10

// 在将函数压入到栈的时候 参数的值也会进行拷贝 在之后执行的时候 参数值是当初拷贝的值
```


```go
x := 10
defer func(a *int) {
	fmt.Println(*a)
}(&x)

x++
//输出
11

// 当参数是指针的时候 压入到栈的仅仅是指针 在执行之前 指针指向数据发生变化 则等到执行的时候 数据也会变化
```

```go
x := 10
defer func() {
	fmt.Println(x)
}()

x++

// 输出
11

// 函数闭包特性  defer后面的函数并没有参数 函数内部使用的变量是全局的变量（即执行的是外部变量）
```

```go
func f1() int {
	x := 10

	defer func() {
		x++
	}()

	return x
}

func main() {
	fmt.Println(f1())
}

// 结果
10

// 实际原因是 在返回return之前 会将返回语句进行赋值返回

// return x
// temp := x   return temp
// 因为 x 是整型 直接信息拷贝赋值
// 先执行拷贝赋值 再执行defer 语句 因此无法影响到 temp 
```


```go
func f1() *int {
	x := 10
	b := &x

	defer func() {
		*b++
	}()

	return b
}

func main() {
	fmt.Println(*f1())
}

// 结果
11

// return b
// temp := b  return temp
// 在执行return 语句之前 执行的是 赋值语句 因为是地址指针数据 因此在执行完defer之后 指向的共同数据发生变化可以感知到
```

> defer 本质上注册了一个延迟函数  没有嵌套（多个defer平级） 用来替代 try except finally


**如何做异常捕捉**

使用`panic`抛出异常 

```go
func div(a, b int) (int, error) {

	if b == 0 {
		panic("被除数不能为0")
	}

	return a / b, nil

}

func main() {
	a := 12
	b := 0

	defer func() {
		// 将 捕捉异常的逻辑放到函数当中
		err := recover()

		if err != nil {
			fmt.Println("异常被捕捉到")
		}
		fmt.Println("div")

	}()

	fmt.Println(div(a, b))
}
```

panic会引起主线程的挂掉， 同时会导致其他的协程都挂掉

在协程本身的作用空间呢使用panic 可以捕捉到异常 但是 在父协程中无法捕获子协程中出现的异常

```go
func f1(){
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println("捕获到了")
		}
	}()

	go func() {
		defer func() {
			err := recover()
			if err != nil {
			    fmt.Println("捕获到了2")
			}
		}()

		panic("出错了")
	}()

	// 必须延迟 否则会直接退出父协程
	time.Sleep(10*time.Second)
}
```

> 上面函数是无法捕获到子协程产生的错误的




##### 八、函数的高级用法

> 函数做为一种类型，在函数参数中使用

```go 
func actionVariables4(var1,var2 int) (sum int,minus int) {
    sum = var1 + var2
    minus = var1 - var2
    //return sum,minus
    return
}

//第一个参数为函数类型
func funcAsArgs(funcName func(int,int)(int,int),var1,var2 int)(int,int) {
    return funcName(var1,var2)
}


//函数可以做为一种类型
sum,minus = funcAsArgs(actionVariables4,1,2)
fmt.Println(sum,minus)
```

> 函数是一等公民可以作为参数、返回值、赋值给另一个变量

go语言官方推荐函数返回值命名

> 函数做为值在函数参数中使用

定义一个切片，判断哪个是偶数，哪个是奇数
以此，我们引出 函数做为值在函数参数中使用的例子
定义2个函数判断是否为奇数，偶数，来演示如何将函数做为值来使用

```go 
type Boolean func(int)bool // 定义好一种函数格式

// 是否为偶数
func isEven(integer int) bool {
    if integer % 2 == 0 {
        return true
    }

    return false
}

// 是否为奇数
func isOdd(integer int)bool {
    if integer % 2 == 0 {
        return false
    }

    return true
}

func judgeSlice(slice []int,boolean Boolean) (result []int) {

    for _,value := range slice {
        if boolean(value) {
            result = append(result,value)
        }
    }

    return result
}


slice := []int{1,2,3,4,5,6,7}

evenSlice := judgeSlice(slice,isEven)
oddSlice := judgeSlice(slice,isOdd)
fmt.Println("偶数：",evenSlice)
fmt.Println("奇数：",oddSlice)
```

```go 
func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```

##### 九、init()/main()，go语言保留函数

> go语言编译器会自动调用
> 
> 在定义的时候不能有任何的参数与返回值
> 
> init()一般存在于所有的包中

> 在一个包中可以有多个init()函数
> 
> 但是我们建议只写一个init()函数
> 
> 一般用于初始化工具，比如连接数据库
> 
> main()只能在main包中使用

`执行顺序`

> -当前包引入其他包，优先调用其他包的全局变量，init()函数（如果其他包也调用 了init()函数，则继续向上调用），再调用本包的init()函数
> 
> -全局变量
> 
> -init()先执行
> 
> -main()后执行
> 
> -执行顺序:全局变量-> init() -> main()

##### 十、值类型、引用类型做为参数调用

> 1.数组，值类型
> 
> 2.切片/map，引用类型
> 
> 3.以值类型传递参数，是对应值的拷贝，不会改变实参的值
> 
> 4.以引用类型传递参数，传递的是对应的地址，这个地址占用的空间是轻量级的，对内存占用是极少的，可以通过指针传递的方式，传递一个何种较大的数据，会改变实参的值
