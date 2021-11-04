
### 结构体

##### 一、结构体概述

结构体是值类型，用于自定义数据类型与实现面向对象

##### 二、作用与应用场景

`作用`

用于定义自定义类型

`应用场景`

自定义数据类型
面向对象

##### 三、定义自定义类型与使用

###### 定义自定义类型的方式1

```go 
type structVariables struct{}


type integer int
var intVariables int
var integerVariables integer
intVariables = integerVariables

//基于一种类型去创建另一种类型，被认为是两种不同的类型
fmt.Println(intVariables,integerVariables)

//无法相互赋值,如何赋值？类型转换
intVariables = int(integerVariables)
```

###### 定义自定义类型的方式2 结构体

> 结构体字段可以是任何类型，以及结构体本身，函数，接口等等
> 
> 结构体类型是某一类具体事物的抽象
> 
> 以中秋节的月饼举例，月饼的模板，做出月饼
> 
> 通过组合一定数据的字段来完成

```go  
type 结构体名 struct {
    field1 type1
    field2 type2
    ....
} 
```

**结构体的使用方式1**

```go 
type userinfo struct {
    name string
    age int
    height float32
    eduschool string
    hobby []string
    moreinfo map[string]interface{}
}

//使用
var bobo userinfo
bobo.name = "小杨"
bobo.age = 18
bobo.height = 181
bobo.eduschool = "庞各庄大学"
bobo.hobby = []string{"coding","运动","旅行"}
bobo.moreinfo = map[string]interface{}{
    "work":"福报厂",
    "duty":"产品狗",
}
```

**结构体的使用方式2 :=简短声明来实现一个结构变量**

第二种形式没有字段名，只声明对应的值，每个值也可以分别占一行，不过习惯上这种形式会写在一行里，结尾不需要逗号。这种形式下，值的顺序很重要，必须要和结构声明中字段的顺序一致，同时在我们不指定字段名称的时候，结构体当中的所有字段必须全部赋值

```go
// 1.声明变量
// 2.初始化
var liuge = userinfo{
    name:      "",
    age:       0,
    height:    0,
    eduschool: "",
    hobby:     nil,
    moreinfo:  nil,
}

huge := userinfo{
        eduschool: "庞各庄大学",
        hobby:     []string{"拍电影","唱歌","旅行"},
        moreinfo: map[string]interface{}{
            "role":"演员",
            "earnmoney":300000,
        },
        name:      "小杨",
        age:       28,
        height:    188,
    }
```

> 不指定字段名的时候，需要严格的按照定义结构体时候的顺序赋值
> 
> 指定字段名的时候，可以不按定义结构体时候的顺序赋值
> 
> 对结构体赋值时，如果各个字段不在一行，最后一个字段必须添加逗号
> 
> 对结构体赋值时，如果各个字段在同一行，则最后的一个字段可以不添加逗 号

```go  
xiaoge := userinfo{"小哥",12,120,"小学",[]string{"学习","玩","打游戏"}, map[string]interface{}{"年级":"六年级"}}
fmt.Printf("xiaoge=%v\n",xiaoge)
```

对结构体赋值时，如果各个字段在同一行，则最后的一个字段可以不添加逗号

**结构体的使用方式3 new**

使用`new` `new(int)`,`new(string)`,`new(T)` 返回结构体指针

```go 
//var t *T
//t = new(T)
var xiaoming *userinfo
xiaoming = new(userinfo)
(*xiaoming).name = "小明"
(*xiaoming).age = 12
(*xiaoming).eduschool = "北京小学"
//xiaoming->(*xiaoming) go语言编译器自动转换
xiaoming.hobby = []string{"学习","玩","打游戏"}
fmt.Println(xiaoming)
```

**结构体的使用方式4 &地址符，同样是返回的结构体指针**

```go 
var xiaohong *userinfo = &userinfo{
    "小红",12,120,"小学",[]string{"学习","玩","打游戏"}, map[string]interface{}{"年级":"五年级"},
}
```

##### 四、结构体注意事项

> 不指定字段名的时候，需要严格的按照定义结构体时候的顺序赋值
> 
> 1.结构体是值类型
> 
> 2.结构体之间是否可以相互转换？可以转换，前提条件：具有相同的字段（个数，类型，名称)
> 
> 3.结构体可以做为另一个结构体字段的类型
> 
> 4.结构体变量赋值，各字段不在同一行时，最后一个字段必须加逗号
> 
> 5.结构体变量赋值，各字段在同一行时，最后一个字段的逗号可加，可不加

```go 
//1.结构体是值类型
    user1 := userinfo{
        name:      "user1",
        age:       0,
        height:    0,
        eduschool: "",
        hobby:     nil,
        moreinfo:  nil,
    }
    user2 := user1
    fmt.Printf("user1 = %p,user2 = %p\n",&user1,&user2)


//2.结构体之间是否可以相互转换？可以转换，前提条件：具有相同的字段（个数，类型，名称)
user3 := userinfo{
    name:      "user3",
    age:       0,
    height:    0,
    eduschool: "",
    hobby:     nil,
    moreinfo:  nil,
}

user4 := peopleinfo{
    name:      "user4",
    age:       0,
    height:    0,
    eduschool: "",
    hobby:     nil,
    moreinfo:  nil,
    //pmoreinfo:nil,
}

user3 = userinfo(user4)
//user3 = user4
fmt.Println(user3)
```

##### 五、自定义类型做为结构体字段的类型与最佳实践

###### 结构体可以做为另一个结构体字段的类型

> 后台管理系统中，不同权限分配的问题

```go 
//后台管理系统中，权限问题，这里涉及了角色，超级管理员，管理员，普通用户
type role struct {
    user userinfo 
    authorization Integer  //1=超级管理员，2=管理员，3=普通用户
}


superadmin := role{
    user:          userinfo{
        name:      "超级管理员",
        age:       0,
        height:    0,
        eduschool: "",
        hobby:     nil,
        moreinfo:  nil,
    },
    authorization: 1,
}
admin := role{
    user:          userinfo{
        name:      "管理员",
        age:       0,
        height:    0,
        eduschool: "",
        hobby:     nil,
        moreinfo:  nil,
    },
    authorization: 2,
}
fmt.Println(superadmin,admin)
```

##### 六、结构体在内存中的布局

结构体在内存中的布局是连续的存储空间

```go 
type Rectange struct {
	x int
	y int
	z int
}

r := Rectange{
	x: 1,
	y: 11,
	z: 11,
}

fmt.Printf("x=%p,y=%p,z=%p\n",&r.x,&r.y,&r.z)

// 输出
x=0xc000138000,y=0xc000138008,z=0xc000138010
```
