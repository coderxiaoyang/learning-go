### 布尔

##### 一、布尔类型，要么为真，要么为假

> 真 true
> 
> 假 false


##### 二、作用与应用场景

`作用`

在计算机当中，为了表示某个条件是否成立，需要使用到布尔类型

`应用场景`

在条件判断当中，条件成立或条件不成立

##### 三、申请变量的方法

```go 
var boolVaraibles1 bool
boolVaraibles1 = true
boolVaraibles2 := (true == false)

if 3 == 4 {
   fmt.Println("false")
} else {
   fmt.Println("true")
}

fmt.Println(!true,!false,!!true)
```

##### 四、注意事项

> 1.只有true和false
> 
> 2.其他类型不可以转换成布尔
