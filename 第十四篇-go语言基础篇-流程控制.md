### 流程控制

##### 一、流程控制是什么

程序的执行顺序

##### 二、条件控制：if eles else if

```go  
if true {
       fmt.Println("true")
   } else if false {
       fmt.Println("false")
   } else {
       fmt.Println("other")
   }
```

> 左大括号放在同一行

**if的另一种使用方法，判断类型**

```go 
if value,ok := courser["go"];ok {
   fmt.Println(value)
}
```

```go 
if num := 11; num % 2 ==0 {
	fmt.Println("偶数")
} else {
	fmt.Println(num)
}
	
fmt.Println(num) // 在 if 作用域定义的变量 是无法在 if作用域之外使用的 
```


> 打开某一个文件不存在或者由于权限的问题无法操作，这种使用方式，在go语言普遍存在，我们也推荐以这种方式来书写。

```go 
if fileHandle,err := os.Open("hello.txt");err != nil {
        //null
        fmt.Println(fileHandle)
        fmt.Println(err.Error())
    } else {
        fmt.Println("获取文件成功")
    }
```


##### 三、选择控制 switch case select  

```go 
switch switchVariables := 100; {
    case switchVariables >90:
        fmt.Println("成绩优秀")
        fallthrough // fallthrough 会强制执行后面的 case 语句，fallthrough 不会判断下一条 case 的表达式结果是否为 true。
    case switchVariables>80:
        fmt.Println("成绩良好")
        fallthrough
    case switchVariables >=60:
        fmt.Println("成绩合格")  // 默认自带 break 匹配执行完毕 退出 switch 语句
    default:
        fmt.Println("成绩不及格")
    }
```

```go
// if else 也能实现 类似的功能
var switchVariables int = 100
if switchVariables > 100 {

} else {

}
```

```go  
// 使用switch模拟if else
switch {
    case switchVariables > 90:
        fmt.Println("成绩优秀")
    case switchVariables > 80:
        fmt.Println("成绩良好")
    case switchVariables >=60:
        fmt.Println("成绩合格")
    default:
        fmt.Println("成绩不及格")
    }
```

```go 
// switch判断类型，类型断言(后面还会详细讲解) 
switch typeVariables := interfaceVariables.(type) {
case float32:
    fmt.Println("float32",typeVariables)
case float64:
    fmt.Println("float64",typeVariables)
case int8:
    fmt.Println("int8",typeVariables)
default:
    fmt.Println("未知的类型",typeVariables)
}

// 类型断言，这种方式在go语言中称为类型断言

var category = "笔记本"
switch category  {
// 一个case中判断多个满足条件
case "电脑", "笔记本", "手机":
    fmt.Println("数码产品")
case "T恤", "背心":
    fmt.Println("服饰产品")
case "饼干", "巧克力", "苏打":
    fmt.Println("零食产品")
default:
    fmt.Println("其他产品")
}
```

##### 四、循环控制

###### for
![for循环执行流程](https://www.tutorialspoint.com/go/images/go_for_loop.jpg)

```go 
- //循环控制 for e
  //for 初始化条件;循环的条件;变化的条件 {
  //
  //}
    
- for i :=0;i<10;i++ {
        fmt.Println(i)
    }
  
- initVariables := 1
  for initVariables < 10 {
      fmt.Print(initVariables)
      initVariables++
  }
  
- 条件语句是可以被省略的，如 i:=0; ; i++ 或 for { } 或 for ;; { }（;; 会在使用 gofmt 时被移除）：这些循环的本质就是无限循环。
  最后一个形式也可以被改写为 for true { }，但一般情况下都会直接写 for { }//死循环

  for {
      fmt.Println(1)
  }
```

###### for…range…
主要对`slice` `map` 数组 字符串 进行循环迭代
```go 
  var stringVariables = "hello 慕课，go语言体系课"
  for key,value := range stringVariables {
      //fmt.Println(key,value)
      fmt.Printf("key=%d,value=%c,类型=%T\n",key,value,value)
  }
  
  key=0,value=h,类型=int32
  key=1,value=e,类型=int32
  key=2,value=l,类型=int32
  key=3,value=l,类型=int32
  key=4,value=o,类型=int32
  key=5,value= ,类型=int32
  key=6,value=慕,类型=int32
  key=9,value=课,类型=int32
  key=12,value=，,类型=int32
  key=15,value=g,类型=int32
  key=16,value=o,类型=int32
  key=17,value=语,类型=int32
  key=20,value=言,类型=int32
  key=23,value=体,类型=int32
  key=26,value=系,类型=int32
  key=29,value=课,类型=int32

  我们看到 英文字符串是一个位置 一个索引 中文是三个索引  主要是因为中文是UTF-8 编码
  如果我们想获取具体某个索引的值 还是要将字符串转为 rune 进行遍历 
  
  var stringVariables = "hello 慕课，go语言体系课"

  stringArr := []rune(stringVariables)

  for i := 0; i < len(stringArr); i++ {
  	fmt.Printf("%c\n", stringArr[i])
  }
  
 //只  打印key,不打印value
  for key := range stringVariables {
      fmt.Printf("key=%d\n",key)
  }
// value遍历数据中对应索引的值拷贝，所以对它所做的任何修改都不会影响到数据中原有的值（但如果value 为指针，则会产生指针的拷贝，就会修改数据的原值）。一个字符串是 Unicode 编码的字符（或称之为 rune）集合，for...rage可以用来遍历字符串：每个 rune 字符和索引在 for-range 循环中是一一对应的。它能够自动根据 UTF-8 规则识别 Unicode 编码的字符。
```

##### 五、跳转控制

###### goto

跳转到某个标签

Go语言的goto语句可以无条件地转义到过程中指定的行

goto语句通常与条件语句配合使用。可以来实现条件转义，构成循环，跳出循环体等功能

![goto](https://www.tutorialspoint.com/cprogramming/images/cpp_goto_statement.jpg)


```go 
func gotoFunc() {
    variables := 0
GOTO:
    fmt.Println(variables)
    variables++
    goto GOTO
}
```

使用goto 可以简化退出多层循环的外层循环

```go 
package main

import "fmt"

func main() {

    for x := 0; x < 10; x++ {

        for y := 0; y < 10; y++ {

            if y == 2 {
                // 跳转到标签
                goto breakHere
            }

        }
    }

    // 手动返回, 避免执行进入标签
    return

// 标签
breakHere:
    fmt.Println("done")
}
```

使用 goto 集中处理错误

```go  
    err := firstCheckError()
    if err != nil {
        goto onExit
    }
    err = secondCheckError()
    if err != nil {
        goto onExit
    }
    fmt.Println("done")
    return
onExit:
    fmt.Println(err)
    exitProcess()
```

###### break
```go 
 //break退出当前循环
    for outerIndex :=0;outerIndex<10;outerIndex ++ {
        for innerIndex :=0 ;innerIndex<6;innerIndex ++ {
            fmt.Println(outerIndex,innerIndex)
            break
        }
    }
 //break+退出标签
FORBREAK:    for outerIndex :=0;outerIndex<10;outerIndex ++ {
                for innerIndex :=0 ;innerIndex<6;innerIndex ++ {
                    fmt.Println(outerIndex,innerIndex)
                    break FORBREAK
                }
            }
```

###### continue

```go 
//continue 忽略当前循环剩余的代码
        for outerIndex :=0;outerIndex<3;outerIndex ++ {
            for innerIndex :=0 ;innerIndex<2;innerIndex ++ {
                fmt.Println(outerIndex,innerIndex)
                continue
                fmt.Println("这里是内层循环剩余的代码")
            }
        }

 //continue+标签，忽略当前循环剩余的代码，跳转转到某个标签
FORCONTINUE:for outerIndex :=0;outerIndex<3;outerIndex ++ {
                for innerIndex :=0 ;innerIndex<2;innerIndex ++ {
                    fmt.Println(outerIndex,innerIndex)
                    continue FORCONTINUE
                    fmt.Println("这里是内层循环剩余的代码")
                }
            } 
```
