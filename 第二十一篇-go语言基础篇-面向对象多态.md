###  面向对象多态

##### 什么是多态

> 多态同一个行为具有多个不同表现形式。也就是说一个类实例（对象）的相同方法在不同场景下有不同表现形式。多态机制使内部结构不同的对象可以共享相同的外部接口。
>
> 这意味着，虽然针对不同对象的具体操作不同，但通过一个公共的类，它们（那些操作）可以通过相同的方式予以调用。最大的优点就是说可以降低类型之间的耦合度，对吧
>

在go语言当中多态：用接口实现：某个类型的实例可以赋给它所实现的任意接口类型的变量。类型和接口是松耦合的，并且多重继承可以通过实现多个接口实现。

多态，是指一个类实例的相同方法在不同情形有不同表现形式。

多态机制使具有不同内部结构的对象可以共享相同的外部接口。这意味着，虽然针对不同对象的具体操作不同，但通过一个公共的类，它们（那些操作）可以通过相同的方式予以调用。

##### 多态的作用

张三的儿子娶媳妇，通知李五去他家`喝喜酒`（这里就是一个`方法`），但是李五有事不能来，李五就让他的三个儿子其中一个代表他老爹李五去`喝喜酒`（这里就是一个`方法`），使用的是他们老爹的`喝喜酒`方法,就像他老爹本人一样。

不必编写每一子类的功能调用，可以直接把不同子类当父类看，屏蔽子类间的差异，提高代码的通用率/复用率

父类引用可以调用不同子类的功能，提高了代码的扩充性和可维护性

##### 接口定义

接口本身是调用方和实现方均需要遵守的一种协议，大家按照统一的方法命名参数类型和数量来协调逻辑处理的过程。
Go 语言中使用组合实现对象特性的描述。对象的内部使用结构体内嵌组合对象应该具有的特性，对外通过接口暴露能使用的特性。

**其它编程语言中的接口**

接口是一种较为常见的特性，很多语言都有接口特性。C/C++、C# 语言中的接口都可以多重派生实现接口组合；在苹果的 Objective C 中与接口类似的功能被称为 `Protocol`，这种叫法比接口更形象、具体。
非侵入式设计是 Go 语言设计师经过多年的大项目经验总结出来的设计之道。只有让接口和实现者真正解耦，编译速度才能真正提高，项目之间的耦合度也会降低不少。



##### go语言的接口是一种类型

> go语言中 接口是一种类型 一种抽象类型

```go
type Programmer interface {
	Coding() string
	Debug() string
}
// 定义了一个接口 Programmer 

var pro Programmer // 接口是一种抽象类型 即可以根据接口生成一个变量

type Pythoner struct {
	name string
}
// 定义了一个结构体 Pythoner

func (p Pythoner) Coding() string {
	fmt.Println("Python 开发者")

	return "Python 开发者"
}
// 将 Coding  方法绑定到结构体上面

// 只有我们全部将接口的方法都实现绑定到结构体上面 该 Pythoner 类型的结构体 才能是一个 Programmer 类型

// Pythoner 本身就是一个类型 为什么还要在乎是否是一个 Programmer 类型呢？

// 这个就是我们要讲的多态
type AliPay struct {
}
type WeChatPay struct {
}

type BankPay struct {
}

// 我们定义了三种支付方式 如果根据场景不同 使用不同的支付方式 会随着支付方式的变化 修改代码逻辑
// 如果有一种通用的类型 可以屏蔽调不通支付渠道的差异 这样代码就很简约 即使新增 或者 删除某个支付渠道 也会大改代码

var g Compatibility

g = AliPay{}
g = WeChatPay{}
g = BankPay{}

// 选择不同的支付渠道 都赋值给兼容的类型变量 即使以后删除某个渠道 代码逻辑也不会大改  这就是 多态的场景

// 多态就是声明类型的时候声明一个兼容的类型 但是实际赋值的时候是另一种类型

// 通过接口即可将一种类型赋值给一个兼容类型（前提是类型实现了接口定义的所有方法）
```

```go
type Programmer interface {
	Coding() string
	Debug() string
}

type G struct {
}
type Pythoner struct {
	name string
}

func (p G) Coding() string {
	fmt.Println("go开发者")
	return "go开发者"
}

func (p G) Debug() string {
	fmt.Println("我会go的debug")
	return "我会go的debug"
}
func (p Pythoner) Coding() string {
	fmt.Println("Python 开发者")

	return "Python 开发者"
}

func (p Pythoner) Debug() string {
	fmt.Println("我会python的debug")
	return "我会python的debug"
}

// 我们实现了一个方法 参数为一个接口类型 凡是实现了该接口所有方法的类型 都可以当做参数 这样就可以简化处理相同类型的变量了
func handlerPro(pro Programmer) {

}

func main() {
	var pros []Programmer // 可以将实现接口所有方法的类型都放到一个切片中
	pros = append(pros, Pythoner{}, G{})
  
  // 接口虽然是一种类型 但是和其他类型不太一样 是一种抽象类型（内部只有方法名称 没有实体） 相反的 struct就是一种具象的类型 我们可以将一种具象的类型 赋值给一种抽象的类型
  
  p := Pythoner{}
	fmt.Printf("%T\n", p) 
  // 输出 main.Pythoner

  var pro Programmer = Pythoner{} // 我们可以将实现了接口所有方法的类型直接赋值给接口类型的变量
	fmt.Printf("%T", pro)
  // 输出 main.Pythoner
  // 我们看到 一个接口类型的变量 当我们赋值给它一个具象的类型时候 它的类是我们赋值的那个对象类型 而接口类型的变量就像一个占位符 仅仅占据一个位置

}
```



##### go的error是一个接口

```go
type error interface {
	Error() string
}

// 既然error是一个接口就意味这个不能进行实例化赋值给一个变量

// 我们可以自定义一个错误类型 进行错误赋值
type MyError struct {
}

func (m MyError) Error() string {
	return "发生错误"
}

var error = MyError{}

// 不过go语言已经有包实现了自定义的 error 不用我们自己再去写一个

func New(text string) error {
	return &errorString{text}
}

var error error = errors.New("发生错误") 

// 不过实际使用中 更多的是使用 fmt 包
errorReason := "文件不存在"
var error error = fmt.Errorf("发生错误: %s", errorReason) // 能够输入格式化的字符串
fmt.Println(error)
```



##### 空接口的应用场景

```go
var i interface{} // 这样子我们就定义了一个空接口

// 上面效果和下面的效果是一样的 都是定义一个空接口 接口里面没有任何方法
type EmptyInterface interface {
}
var i EmptyInterface

// 空接口类似于Python中的 object 

// 第一个作用：可以将任意类型赋值给空接口
type Course struct {
	name string
	age  int
	url  string
}
var i interface{}

i = Course{}
i = 10 
i = "linux"
fmt.Println(i)

// 第二个作用: 参数传递
func print(i interface{}) {
	fmt.Printf("%v\n", i)

}

func main() {
	print(1)
	print("golang")
}

// 第三个作用：空接口可以作为map的值
var teacherInfo = make(map[string]string)

teacherInfo["name"] = "Coderyang"
teacherInfo["age"] = "12"
teacherInfo["weight"] = "132.5"

fmt.Println(teacherInfo)
// 我们定义了一个 map value格式为字符串 那么我们所有的value必须为字符串格式 有些不是字符串的 也需要

// 当我们将map的值类型设置为 空接口的时候 则可以将任意类型赋值给 value
var teacherInfo = make(map[string]interface{})

teacherInfo["name"] = "Coderyang"
teacherInfo["age"] = 12
teacherInfo["weight"] = 132.5

fmt.Println(teacherInfo)
```



##### 接口的类型断言

我们先看一个接口的坑

```go
type Course struct {
	name  string
	price int
	url   string
}

func (c Course) printInfo() string {
	return "测试函数"
}

func main() {
	//c := Course{} // 声明结构体对象
	//c.printInfo()

	c := &Course{} // 声明对象指针
	c.printInfo()
}

// 当直接给结构体绑定某个方法时 无论是结构体对象 还是对象指针均能成功调用函数

type Printer interface {
	printInfo() string
}

// 这里绑定参数 必须按照 c Course 填写  不能填成指针方式 c *Course
func (c Course) printInfo() string {
	return "测试函数"
}

func main() {

	var printer Printer

	printer = Course{}
	printer.printInfo()
}
```

> 接口一般有一个默认的规范：使用 `er`结尾

```go
func print(i interface{}) {

	v, ok := i.(int) // 使用括号 加 类型判断 变量是否为某个类型 是的话 v 变量值 ok 为 True 否则 v 为 判断类型的默认值 ok 为 False

	fmt.Println(v, ok)

}

func main() {
  print(12) // 12 true

	print("12") // 0 false
}
```

```go
func print(i interface{}) {
	switch v := i.(type) { 
	case string:
		fmt.Printf("%s 是字符串\n", v)
	case int:
		fmt.Printf("%d 是整型\n", v)

	}
}
// i.(type) 进行类型断言  直接打印为变量的值
```

##### 通过接口协议去理解 sort的本质

```go
// Sort sorts data.
// It makes one call to data.Len to determine n, and O(n*log(n)) calls to
// data.Less and data.Swap. The sort is not guaranteed to be stable.
func Sort(data Interface) {
	n := data.Len()
	quickSort(data, 0, n, maxDepth(n))
}

// 需要排序的数据类型为 Interface  该接口 定义了一些方法 即 如果你想实现排序 则需要实现这些方法


// A type, typically a collection, that satisfies sort.Interface can be
// sorted by the routines in this package. The methods require that the
// elements of the collection be enumerated by an integer index.
type Interface interface {
	// Len is the number of elements in the collection.
	Len() int
	// Less reports whether the element with
	// index i should sort before the element with index j.
	Less(i, j int) bool
	// Swap swaps the elements with indexes i and j.
	Swap(i, j int)
}

```

```go
data := []int{1,2,34,5}
sort.Sort(data)
// 默认的切片是咩有实现上面三个方法 因此无法直接排序

type Course struct {
	name  string
	price int
	url   string
}

type Courses []Course // 使用type 给切片类型进行重命名

func (c Courses) Len() int {
	return len(c)

}

# 需要排序的内容进行比较
func (c Courses) Less(i, j int) bool {
	return c[i].price < c[j].price
}

func (c Courses) Swap(i, j int) {

	c[i], c[j] = c[j], c[i]

}

func main() {

	data := Courses{
		Course{"linux", 100, ""},
		Course{"go", 200, ""},
		Course{"python", 250, ""},
	}
	sort.Sort(data) // 不仅仅需要某个类型 而是说完成某些协议的类型 即完成某些方法

	for _, v := range data {
		fmt.Println(v)
	}
}

// 输出 
{linux 100 }
{go 200 }
{python 250 }
```



##### 多态的使用

###### 1. 定义接口与类型，让类型去实现接口中的方法

> 1.定义接口时，方法不能实现
>
> 2.一个类型如果实现了接口中的所有方法，我们就说这种类型实现接口，不仅仅结构体，也可以是内置类型
>
> 3.自定义类型可以实现多个接口

`定义格式`

```go  
type 接口名 interface {
    方法名1（参数列表）（返回值）
    方法名2（参数列表）（返回值）
    方法名3（参数列表）（返回值）
    。。。
}
通过自定义类型来实现接口中的所有方法
//定义类型
type T struct {
}
type TT string

//方法接收者
func (this *T)方法名1()（返回值）{}
func (this *T)方法名2()（返回值）{}
func (this *T)方法名3()（返回值）{} 
```

在`main.go`中定义一个结构体一个接口`pay`，增加两个方法`topay()`，`info()`

```go 
type pay interface {
    topay()
    info()
}
```

同时我们来定义一个结构体`payment`

```go 
type payment struct {
    paymentmethod string
}
```

让这个结构体去实现接口`pay`中的所有方法

```go 
func (this *payment)topay()  {
    fmt.Println("topay:",this.paymentmethod)
}

func (this *payment)info()  {
    fmt.Println("info:",this.paymentmethod)
}
```

我们来定义`payment`类型的变量`_payment`

```go  
_payment := &payment{paymentmethod:"alipay"}
```

这要我们就可以通过这个变量去调用接口中的方法了，因为我们的结构体`payment`实现了接口中所有的方法

```go 
_payment.info()
_payment.topay()
```

> 注意点
>
> 一个变量实现了接口当中所有方法，接口就可以指向这个变量
>
> 接口内的方法不能实现，体现了程序的多态与低偶合高内聚

```go 
var _pay pay
//一个变量实现了接口当中所有方法，接口就可以指向这个变量
_pay = _payment
_pay.info()
_pay.topay()
```

不过一般不这样使用

###### 2. 自定义类型也可以实现接口

在`main.go`文件中,定义两个接口

```go 
type write interface {
    echo()
    out()
}

type read interface {
    scan()
    input()
}
```

我们来基于内置类型创建自定义类型

```go 
//自定义类型，基于内置类型
type readwrite string
```

让自定义类型来实现上面定义的两个接口中的所有该当

```go 
//自定义类型`readwrite`实现`write`接口中的`echo()`方法
func (this *readwrite)echo()  {
    fmt.Println("readwrite:echo()")
}

//自定义类型`readwrite`实现`write`接口中的`out()`方法
func (this *readwrite)out()  {
    fmt.Println("readwrite:out()")
}

//自定义类型`readwrite`实现`read`接口中的`scan()`方法
func (this *readwrite)scan()  {
    fmt.Println("readwrite:scan()")
}

//自定义类型`readwrite`实现`read`接口中的`input()`方法
func (this *readwrite)input()  {
    fmt.Println("readwrite:input()")
}
```

这样我们自定义的类型`readwrite`就实现了两个接口中的所有方法，在`main.go`文件中调用

```go 
var _readwrite readwrite
_readwrite.echo()
```

> 多重继承接口，所有的方法都要实现

```go 
//多重继承格式
type InterfaceAA interface {
    InterfaceA
    InterfaceB
}
```

##### 综合案例

为了巩固面向对象知识点，通过结合面向对象三大特征，综合的对面向对象的知识进行进阶

> 企业项目中经常涉及到与日志存储的业务，比如针对web项目的日志存储

> 比如磁盘IO操作的日志:磁盘进行数据拷贝读取一般会有读写日志,记录读取到的位置，即当前数据在磁盘的哪个碰头哪个扇区上

> 比如网络请求的日志:去访问某个网站一般会有请求日志的记录，用于分析来访客户，刻画出用户的区域，访问兴趣爱好（网购时的一个典型例子,用户浏览记录写cookie）

这里我们模拟一个`网络`读写日志与`磁盘`读写日志的例子

定义写操作接口`write`

```go 
type write interface {
    echo()
    out()
}
```

定义公共日志结构体`Log`，做为父类,并为其增加方法`writeLog()`，传递一个`write`接口类型的变量

```go 
//日志结构体
type Log struct {
    name string
    content string
    addtime int64
}

func (this *Log)writeLog(_write write)  {
    fmt.Println(this.name+"---"+this.content)
    _write.echo()
    _write.out()
}
```

定义`网络`读写结构体与`磁盘`读写结构体，继承自父类`Log`

```go 
type NetLog struct {
    Log
}

type IOLog struct {
    Log
}
```

定义自定义类型，`iowrite`与`netwrite`，分别实现`write`接口中的所有方法

```go 
type iowrite string
func (this *iowrite)echo()  {
    fmt.Println("iowrite:echo()")
}
func (this *iowrite)out()  {
    fmt.Println("iowrite:out()")
}

type netwrite string
func (this *netwrite)echo()  {
    fmt.Println("netwrite:echo()")
}
func (this *netwrite)out()  {
    fmt.Println("netwrite:out()")
}
```

`main.go`文件中

```go 
//父类引用指向子类对象
//一个变量实现了接口当中所有方法，接口就可以指向这个变量
log := &Log{
    name:    "微信小程序支付日志",
    content: "微信小程序支付日志内容",
    addtime: 0,
}

var _iowrite *iowrite
var _netwrite *netwrite 
//writeLog的参数是一个write类型，我们这里的iowrite实现了write接口中的所有方法，所以可以传递iowrite
//相当于write （指向）-> iowrite
log.writeLog(_iowrite)

//writeLog的参数是一个write类型，我们这里的netwrite实现了write接口中的所有方法，所以可以传递netwrite
//相当于write （指向）-> netwrite
log.writeLog(_netwrite)
```

> 调用者多态的一面

```go 

netlog := &NetLog{Log{
    name:    "微信小程序网络支付日志",
    content: "微信小程序网络支付日志内容",
    addtime: 0,
}}
//writeLog的调用者是Log,我们的NetLog继承自Log，所以可直接调用
netlog.writeLog(_netwrite)

filelog := &IOLog{Log{
    name:    "微信小程序文件支付日志",
    content: "微信小程序文件支付日志内容",
    addtime: 0,
}}
//writeLog的调用者是Log,我们的IOLog继承自Log，所以可直接调用
filelog.writeLog(_iowrite)
```
