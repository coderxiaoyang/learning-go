
### 面向对象继承

##### 什么是继承

继承是面向对象的基本特征之一，继承就是子类继承父类的特征和行为，使得子类对象（实例）自动就拥有了父类的属性和方法，

当多个子类拥有父类相同的方法与属性时，就可以抽取共有特征和方法形成一个父级类，形成了父子类之间关系。

继承机制可以很好的提高了代码复用率，比如在Java中的Object类，就是所有类的超类。

继承，指可以让某个类型的对象获得另一个类型的对象的属性的方法。

它支持按级分类的概念。继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。 通过继承创建的新类称为“子类”或“派生类”，被继承的类称为“基类”、“父类”或“超类”。

继承的过程，就是从一般到特殊的过程。要实现继承，可以通过 “继承”（`Inheritance`）和“组合”（`Composition`）来实现。继承概念的实现方式有二类：实现继承与接口继承。实现继承是指直接使用 基类的属性和方法而无需额外编码的能力；接口继承是指仅使用属性和方法的名称、但是子类必须提供实现的能力。

在go语言当中继承：主要是通过类型组合的方式来实现：内嵌一个（或多个）包含想要的行为（字段和方法）的类型；多重继承可以通过内嵌多个类型实现

##### 继承的作用

支付是一个企业项目变现的重要手段，做为企业应用必不可少的功能，支付系统包括微信支付，支付宝，银联，京东等等，在开发中如何避免重复代码来实现类似的支付功能？由此引出继承

##### 继承的使用

###### 1. 结构体嵌套

> 继承在go语言中主要通过在一个结构体中嵌套另一个结构体，那么这个结构体自动拥有另一个结构体的字段与方法，实现了继承

引入前面包的知识，定义一个`mode`l包，新建`payment.go`文件，这里我们将结构体`PaymentArgs`

```go
package model

import "fmt"
//微信支付
//支付宝
//银联
//银行卡
type PaymentArgs struct {
    AppID string
    MchID string
    Key string
    CallbackUrl string
}
```

同时在`model`包，新建`Alipay.go`文件，这里我们让结构体`Alipay` 继承 `PaymentArgs`

```go 
package model

import "fmt"

type Alipay struct {
    PaymentArgs 
    AlipayOpenID string
    string
}
```

这样`Alipay`结构体就自动拥有`PaymentArgs`的信息

在我们的`main.go`文件中这样来实现结构体变量

```go 
alipay := &model.Alipay{
        PaymentArgs:  model.PaymentArgs{
            AppID:"alipay123",
            MchID:"alipaymchid",
            Key:"alipayfjkadsfjkasfjas",
            CallbackUrl:"https://api.imooc.com/alipay",
        },
        AlipayOpenID: "alipayopenid",
    }
```

当然如果想再为`微信支付`增加一个类似的结构体，也可以同时在`model`包，新建`payment_weixin.go`文件，这里我们让结构体`WeixinPay` 继承 `PaymentArgs`，访问方式同`Alipay`

我们在`main.go`中来打印他们的信息

```go 
fmt.Println(alipay.PaymentArgs.AppID)
fmt.Println(weixinpay.WeixinOpenID)
```

###### 2. 同一个结构体继承了多个不同的结构体

> 多个不同结构体有相同字段时，如何使用?

在`mode`l包，新建`payment_other.go`文件，这里我们将结构体`PaymentOther` ，结构体内容同`PaymentArgs`相同,`payment_other.go`的内容

```go 
package model

//微信支付
//支付宝
//银联
//银行卡
type PaymentOther struct {
    AppID string
    MchID string
    Key string
    CallbackUrl string
}
```

这里我们让`Alipay`这个结构体同时继承`PaymentArgs`和`PaymentOther`,`Alipay.go`的内容

```go
package model

import "fmt"

type Alipay struct {
    PaymentArgs //匿名结构体
    PaymentOther PaymentOther //有名结构体
    AlipayOpenID string
    string
}
```

这里`Alipay`结构体想访问`AppID`字段就必须指定结构体名，这里有两个概念`匿名结构体`和`有名结构体`

`匿名结构体` 在嵌套结构体时没有指定结构体变量名，例如上面`Alipay`结构体中的`PaymentArgs`
`有名结构体` 在嵌套结构体时没有指定结构体变量名，例如上面`Alipay`结构体中的`PaymentOther`
因为他们具有相同的字段，如果想访问`Alipay`结构体中的`AppID`就必须指定结构体变量名或者匿名结构体名

在`main.g`o中，如下的访问

```go 
fmt.Println(alipay.PaymentOther.AppID)
fmt.Println(alipay.PaymentArgs.AppID)
```

###### 3. 方法的继承与重载

我们为`payment.go`文件中`PaymentArgs`结构体增加方法

```go 
func (this *PaymentArgs)Info() {
    fmt.Printf("Info = %v\n",this)
}
```

这样我们定义的`Alipay`结构体与`WeixinPay`结构体自动就继承了`PaymentArgs`的方法

我们在`main.go`中调用

```go 
paymentArgs := model.PaymentArgs{
    AppID:       "superAppid",
    MchID:       "superMchid",
    Key:         "superKey",
    CallbackUrl: "https://api.imooc.com/super",
}
paymentArgs.Info()
```

同样的我们可以对继承的方法进行重定义

在我们的`Alipay.go`中为`Alipay`结构体增加方法

```go  
func (this *Alipay)Info()  {
    fmt.Printf("alipay = %v\n",this)
}
```

此时`Alipay`结构体重写了`Info()`方法，在`main.go`中调用

```go  
alipay := &model.Alipay{
    PaymentArgs:  model.PaymentArgs{
        AppID:"alipay123",
        MchID:"alipaymchid",
        Key:"alipayfjkadsfjkasfjas",
        CallbackUrl:"https://api.imooc.com/alipay",
    },
    AlipayOpenID: "alipayopenid",
}
alipay.Info()
```

##### 结构体继承访问流程

> 这里以上面`Alipay`结构体当中访问`AppID`为例
> 
> 1.先判断`AppID`是否属于`Alipay`,如果有就访问
> 
> 2.如果没有，继续去找他继承的结构体`PaymentArgs`，如果有就访问
> 
> 3.如果没有继续去找`PaymentArgs`这个结构的所继承的结构体，如果有就访问，没有就报错,因为`PaymentArgs`结构体没有再继承别的结构体
> 

##### 结构体注意事项

> 1.如果一个结构中继承了多个结构体，而这些多个结构体当中有相同的字段，那么我们就需要使用 结构体名来访问

如上面我们所演示的访问Alipay结构体中的AppID就必须指定结构体变量名或者匿名结构体名

> 2.一个内置类型可以做为结构体的匿名字段，这种方式只能在本包访问

```go  
//定义一个结构体，字段为一个内置string类型
type StringStruct struct {
    string
}

ss := StringStruct{"hello"}
//这种方式只能在本包中访问
fmt.Println(ss.string)
```

##### 继承有什么优点

> 1.提高代码的复用率
> 
> 2.提高代码的扩展性与维护性

