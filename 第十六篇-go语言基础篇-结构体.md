### 结构体

##### 一、结构体概述

结构体是值类型，用于自定义数据类型与实现面向对象

面向对象的一些特征：1. 封装 2. 继承 3. 多态 4. 方法重载 5. 抽象基类

go是没有面向对象的 不过也实现了上面的三点功能（后面两点咩有）

对比与Python类的封装（类内部封装了变量以及基于这些变量的方法）go如果想达到统样的封装效果那么一定要解决：变量分封装、方法的封装

```python
from collections import namedtuple

Course = namedtuple('Course', 'name price url')
course = Course(name='Linux', price=100, url='lanqiao.cn')
course.name
# 'Linux'
```

`namedtuple` 很像是一个只能封装数据的类，但是`namedtuple`的性能比Class高，内存会比Class小

`go`语言的`struct`更像是`namedtuple`

>一个包中的变量或者结构体（包括结构体内部的属性）如果首字母是小写 那么对于另外一个包不可见



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
    moreinfo:  nil, // 这里最后一定要加一个逗号
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

```go
bige := unserinfo{"庞各庄大学", []string{"拍电影","唱歌","旅行"}} // 顺序传值 不可混用 
```

```go
type Course struct {
	name  string
	price int
	url   string
}
// 通过指向结构体的指针获取结构体的值
course := &Course{"linux", 100, "baidu"}
fmt.Println((*course).name, (*course).price, (*course).url)
fmt.Println(course.name, course.price, course.url)
// 我们可以通过*获取到指针指向结构体的值 也可以通过语法糖获取到 
// 这个语法糖是指当我们通过指向结构体的指针获取值的时候 可以直接通过 结构对象(course)获取 go语言内部会将其转为(*course) 
// 另一个根本原因是 go语言的指针是受限的

// 结构体的零值 如果不给结构体复制 go语言默认给每个字段采用默认值(字段类型的默认值) 
course := Course{}
fmt.Println(course.name, course.price, course.url)
// 即 即使没有赋值 结构体也不是一个 nil 也会开辟一个空间 将每个值设置成对应类型的默认值
```

```go
// 多种方式零值初始结构体
var course1 Course = Course{}
var course2 Course // 相当于 var a int  会初始化的时候 生成一个内存块 设置对应类型的默认值
var course3 *Course = new(Course)
var course4 *Course // 指针只声明不赋值 默认值是 nil 因此下面将会报错

fmt.Println(course1.price)
fmt.Println(course2.price)
fmt.Println(course3.price)
fmt.Println(course4.price)
```

```go
// 结构体是值类型(拷贝是值拷贝)
course := Course{"linux", 100, "baidu"}
course2 := course
fmt.Println(course)
fmt.Println(course2)
course.price = 200
fmt.Println(course)
fmt.Println(course2)
```

```go
// 结构体的大小 占用内存的大小 可以使用 sizeof 查看对象占用的类型
fmt.Println(unsafe.Sizeof(1)) // 1 是int类型 占用8个字节
fmt.Println(unsafe.Sizeof("Linux")) // 占用16个字节
```

为什么字符串的大小是16个字节呢？这要看字符串的底层数据结构了

```go
type StringHeader struct {
    Data uintptr // 指针 占8个长度 指向数据域
    Len  int // 字符串的实际长度 64位操作系统占8个长度
}
```

当我们计算字符串的大小的时候 实际上计算的只是对应的数据结构的大小  因此不管字符串有多长 实际占大小都是16个字节



```go
// slice的大小 slice的底层数据结构也是结构体
type slice struct {
	array unsafe.Pointer // 指针 指向底层数组的地址
	len   int // 长度
	cap   int // 容量
}
```

```go
s1 := []string{"linux", "os", "tag"}
fmt.Println(unsafe.Sizeof(s1))
```

无论切片内容实际多少 切片对象本身占用的字节数是固定的（64位操作系统是24个字节）



```go
// map 的大小 
m1 := map[string]string{
	"name": "yang",
	"sex":  "male",
}

fmt.Println(unsafe.Sizeof(m1))
```





> 不指定字段名的时候，需要严格的按照定义结构体时候的顺序赋值

> 指定字段名的时候，可以不按定义结构体时候的顺序赋值

> 对结构体赋值时，如果各个字段不在一行，最后一个字段必须添加逗号

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



##### 七、Type的五种应用场景

###### 1. 给一个类型定义别名

```go
var a byte

// byte is an alias for uint8 and is equivalent to uint8 in all ways. It is
// used, by convention, to distinguish byte values from 8-bit unsigned
// integer values.
type byte = uint8
```

> 我们将 `uint8`设置为别名`byte` 这样强调我们正在处理的是一个字节类型

> 这种别名增加可代码的可读性 不过在实际编译过程还是按照`uint8`进行编译的

```go
type myByte = byte
var b myByte
fmt.Printf("%T", b)
// 我们可以将别名设置为任意值
```

###### 2. 基于一个已有类型定义一个新的类型

```go
type myInt int
var i myInt
fmt.Printf("%T", i)
// main.myInt
```

注意和第一种情况对比是左右两边是没有等号`=`的  类型也不是在之前的类型 而是一个全新的类型 这个类型拥有之前类型的所有功能属性，相当于把之前的类型代码拷贝一份生成一个新的类型

###### 3. 定义结构体

```go
type Course struct {
	name string 
	price int
}
```

###### 4. 定义一个接口

```go
type Callable interface {
	
}
```

###### 5. 定义函数别名

```go
type handle func(str string)
```



  ##### 八、结构体绑定方法

对于封装而言 需要同时封装属性和方法 我们看下如何给结构体绑定方法

```go
type Course struct {
	name  string
	price int
	url   string
}

// 函数前面这部分 (c Course) 我们称为函数接收者 即将函数绑定到 结构体上
func (c Course) printCourseInfo() {
	fmt.Printf("课程名：%s, 课程价格：%d, 课程地址: %s\n", c.name, c.price, c.url)
}
func main() {

	course := Course{"linux", 100, "baidu"}
	course.printCourseInfo() // 调用的时候 和面向对象一样
	Course.printCourseInfo(course) // 实际上会转换成这个语句执行
}

// 这样我们就完成可 属性 方法的封装
```

我们看下在绑定的方法内部修改结构体数据会是什么样子

```go
func (c Course) setCourseInfo(price int) {
	c.price = price
}
func main() {

	course := Course{"linux", 100, "baidu"}
	course.setCourseInfo(200)
	fmt.Println(course.price) // 输出 100 
	Course.setCourseInfo(course, 200) // 实际执行语句  回到了函数的值 传递 因为 结构体是值类型 因此在作为函数参数的时候 会进行数据拷贝复制一份
}
```

如果想真的修改结构体的值 则将参数改为指针类型即可

```go
func (c *Course) setCourseInfo(price int) {
	c.price = price
}
func main() {
	course := Course{"linux", 100, "baidu"}
	course.setCourseInfo(200) // 如果参数是指针类型的结构体 我们可以直接这么调用 不用像下面那样
	(&course).setCourseInfo(200) // 原始调用
	fmt.Println(course.price)

}
```

> 结构体的接收者有两种形式：1. 值传递 2. 指针传递
>
> 如果想修改结构体的值 或者 结构体的数据很大 可以使用指针传递

> 注意：结构体的方法和结构体需要在一个包中

> 内置的int类型不能加方法（可以将内置int变成新的类型加方法）



##### 九、通过内嵌结构体实现继承的效果

go语言不支持继承 通过组合来实现相同的效果

```go

type Teacher struct {
	name  string
	age   int
	title string
}

func (t Teacher) teacherInfo() {
	fmt.Printf("姓名: %s, 年龄: %d, 职位: %s", t.name, t.age, t.title)
}

type Course struct {
	teacher Teacher // 将另一个结构体当做变量
	name    string
	price   int
	url     string
}

func (c Course) printCourseInfo() {
	fmt.Printf("课程名：%s, 价格：%d, 教师信息: %s %d %s  ", c.name, c.price, c.teacher.name, c.teacher.age, c.teacher.title)
}

func main() {

	teacher := Teacher{"Coderyang", 28, "程序员"}

	course := Course{teacher, "linux", 100, "baidu"}

	course.printCourseInfo()

}
```

> 上面方式当取出嵌套数据的时候 需要逐层取出

```go

type Teacher struct {
	name  string
	age   int
	title string
}

type Course struct {
	Teacher // 匿名嵌套 不提供参数名称 将继承该结构体的所有字段
	name    string
	price   int
	url     string
}

func (c Course) printCourseInfo() {
	fmt.Printf("课程名：%s, 价格：%d, 教师信息: %s %d %s  ", c.name, c.price, c.name, c.age, c.title)
}
// 当使用匿名嵌套的方式 可以直接访问嵌套获得的属性  但是 当出现重名的时候需要显式的指出需要调用的嵌套结构体数据

func (c Course) printCourseInfo() {
	fmt.Printf("课程名：%s, 价格：%d, 教师信息: %s %d %s  ", c.name, c.price, c.Teacher.name, c.age, c.title)
}
```

> 匿名嵌套可以直接获取嵌套的属性 不过这种方式也是一种语法糖 当我们使用嵌套方式的时候 查找属性会先找当前结构体 找不到 再找嵌套的结构体

到这里我们大致可以说 go 实现了基本的继承 封装



##### 十、结构体的标签

结构体的字段除了名字和类型外，还可以有一个可选的标签（tag）：

它是一个附属于字段的字符串，可以是文档或者其他的重要标记

比如在我们解析json或生成json文件时，常用到`encoding/json`包，它提供了一些默认标签，例如：`omitempty`标签可以在序列化的时候忽略0值或者空值。而`-`标签的作用是不进行序列化，其效果和和直接将结构体中的字段写成小写的效果一样。

```go
type Info struct {
	Name   string `json:"username"` // 指定json解析的时候里面的字段名字
	Age    int    `json:"userage,omitempty"` // 我们加了标签 omitempty 如果是0值或者空值将不参与序列化
	Gender string // 没有设置json序列化的名称时候 保留结构体设置的名称
}

func main() {
	info := Info{"Coderyang", 0, "男"}
	res, _ := json.Marshal(info)

	fmt.Println(string(res))
}
// 输出
{"username":"Coderyang","Gender":"男"}
```

我们可以自己定义标签 然后使用反射获取到标签的具体值

反射通俗来讲就是获取已知变量的类型或者获取已知变量的值，使用realect包的两个方法获取变量的类型和值`reflect.ValueOf()`和`reflect.TypeOf()`，我们下面用代码可以展示一下

```go
type Info struct {
	Name   string `orm:"name, max_length=17, min_length=5"`
	Age    int    `orm:"age, min=18, max=70"`
	Gender string `orm:"gender, required"`
}

func main() {

	info := Info{"Coderyang", 28, "男"}
	info_value := reflect.ValueOf(info) // 值
	info_type := reflect.TypeOf(info)  // 类型
	fmt.Println(info_value, info_type) // {Coderyang 28 男} main.Info
	
	for i := 0; i < info_type.NumField(); i++ {
		field := info_type.Field(i)
		tag := field.Tag.Get("orm")
		fmt.Printf("%d. %v (%v), tag: '%v'\n", i+1, field.Name, field.Type.Name(), tag)
	}

}

// 输出
1. Name (string), tag: 'name, max_length=17, min_length=5'
2. Age (int), tag: 'age, min=18, max=70'
3. Gender (string), tag: 'gender, required'
```

以上代码实现了通过Go语言的反射获取结构标签的值，通过这个例子是不是想到了前面文章实现的ORM模型方法和数据表结构迁移。我们再简单扩展一下关于反射知识的内容，反射本质是在代码运行的时候尝试获取获取对象的类型信息和内存结构，在Go语言中一般定义变量的时候有一个静态类型，比如，`int,string,float,`但是这并不是底层的数据类型，`string`的底层类型是一个`struct`,Go语言的数据类型分为静态类型和底层类型，例如：

```go
type MyInt int

var i int
var j MyInt
```

`i`和`j`的底层类型都是`int`，`i`的静态类型为`int`,`j`的静态类型为`MyInt`,所以他们不能相等，他们的静态类型不同。

所以一个Go语言变量的有值（value）还有它的静态类型（static type）或者底层类型（concrete type），底层类型也可以叫具体类型，静态类型在编译阶段就已经确定了。反射的具体实现是这个变量在赋值和相应类型的时候会向接口（interface）保存自己的类型信息,`type`和`value`用接口（interface）进行转换，上面的代码使用了`Info`的结构体实现了这个转换的接口。

