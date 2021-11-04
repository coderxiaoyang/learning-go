
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
