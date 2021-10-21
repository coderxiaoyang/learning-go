### map

##### 一、map概述

`Map` 是一种无序的`key-value`键值对组成的集合。
通过` key `可以快速检索到我们需要的数据，这里的`key` 类似于索引，指向对应的数据值。

`Map` 是一种集合，所以我们可以像迭代数组和切片那样迭代它。不过，`Map` 是无序的，我们无法决定它的返回顺序，这是因为 `Map` 是使用 `hash` 表来实现的。

`map`无序是有原因的：主要是通过散列函数对`key`计算一个唯一值与值进行映射

通过散列函数对我们的map

0xc0001

0xc0002

0xc0003


> 1.无序的key-value键值对,每次循环出来的数据的顺序是不一致的，又称为集合
> 
> 2.引用类型
> 
> 3.类似perl当中的hash，python当中的字典，go当中被称为map
> 
> 4.通过key可快速找到value
> 
> 5.key需要支持 == 或者 != 操作

##### 二、map的定义

> 1.使用方式 一般定义 map 的方法是：var map[key的类型]value的类型

```go
var mapVariables1 map[string]string
    //在使用map前，需要先make , make的作用就是给map分配数据空间,之后才可以进行引用
    mapVariables1 = make(map[string]string, 2)
    //需要特别说明的是，map中的键是唯一的，不可以重复，值可以重复
    mapVariables1["Monday"] = "周一"
    mapVariables1["Tuesday"] = "周二"
    mapVariables1["Wednesday"] = "周三"
    mapVariables1["Thursday"] = "周四"
```
 
> 2.使用方式 在申明同同时赋值

```go 
// 更常用的方法是使用map字面量。map的初始长度会根据初始化时指定的键值对的数量来确定。
    var mapVariables3 = map[string]int{
        "Monday": 1, "Tuesday": 2, "Wednesday": 3,
        "Thursday": 4, "Friday": 5, "Saturday": 6, "sunday": 7,
    }
```

> 3.通过短冒号（简短声明）直接make，当只需要声明一个 map 的时候，使用 make 的形式

```go 
mapVariables2 := make(map[string]string)

mapVariables2["Monday"] = "周一"
mapVariables2["Tuesday"] = "周二"
mapVariables2["Wednesday"] = "周三"
```

> 4.结构体和c类似 定义一个结构体，做为map的值

```go 
type course struct {
    courseName string //课程名称
    courseTime float32 //课程时长 单位分钟
    courseTeacher string //课程讲师
}
//定义一个结构体 变量并赋值
couser1 := course {
    //不指定结构体字段名的方式，严格按照定义结构体时的顺序
    "go语言体系课",300.3,"波哥",
}

courser2 := course {
    courseTeacher:"胡哥",
    courseTime:100.2,
    courseName:"如何变帅",
}

//定义map，key为string,value为结构体
courser := make(map[string]course)
courser["go"] = couser1
courser["美容"] = courser2
fmt.Println(courser)
```

> 5.map切片 map+切片

```go 
//interface{} 可以把它当作万能类型

var mapVariables6 []map[string]interface{}
mapVariables6 = make([]map[string]interface{},2)
mapVariables6[0] = make(map[string]interface{},2)
mapVariables6[0]["name"] = "小杨"
mapVariables6[0]["age"] = 18

mapVariables6[1] = make(map[string]interface{},2)
mapVariables6[1]["name"] = "coderyang"
mapVariables6[1]["age"] = 16
```

##### 三、map的遍历

```go  
for i :=0;i<len(sortKeys);i++ {
    fmt.Printf("mapVariables3[%s]=%d\n",sortKeys[i],mapVariables3[sortKeys[i]])
}

for key,value := range mapVariables3 {
    fmt.Println(key,value)
}
```

##### 四、map注意事项

> map如何判断一个key是否存在

```go 
value,ok := courser["go"]

if ok {
    fmt.Println(value)
} else {
    fmt.Println("no value")
}
    
//来语言特有的语法格式 
if value,ok := courser["go"];ok {
    fmt.Println(value)
}
```

> 只申明没有make,报错panic: assignment to entry in nil map

```go 
//var mapVariables6 map[string]string

//mapVariables6["hello"] = "小杨"
//nil null 空
```

> map是一个引用,函数传参，遵循引用类型规则


> 删除一个key

```go  
delete(mapVariables6, "key")
```
即使`key`不存在 `delete`方法也不会报错


```go
map是无序的键值对类型，如何排序?

1.定义切片存储所有的key
2.通过排序算法（排序算法使用内置或者冒泡，选择等排序算法均可）对key进行排序
3.按照排序后的key打印map
```
