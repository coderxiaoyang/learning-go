
### 面向对象封装

##### 什么是封装

> 隐藏隐私数据，对外暴露公开的接口，增强安全，简化编程

封装就是隐藏对象的属性和实现细节，仅对外公开接口，控制对数据的读写操作，也就是将数据也就是对象属性与操作数据的方法进行有机的结合，形成“类”，封装的目的是为了增强安全性和简化编程，使用者不必了解具体的实现细节，而只是要通过外部接口，以特定的访问权限来使用类的成员

封装，就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。一个类就是一个封装了数据以及操作这些数据的代码的逻辑实体。在一个对象内部，某些代码或某些数据可以是私有的，不能被外界访问。通过这种方式，对象对内部数据提供了不同级别的保护，以防止程序中无关的部分意外的改变或错误的使用了对象的私有部分。

通过引入外部包小写字母开头的私有的结构体来实现封装,我们引入了工厂方法来实现

通过引入外部包结构体中小写字母开头的私有字段来实现封装，我们引入`gettter`和`setter`来实现

##### 封装的作用

比如某位漂亮小姐姐可能年龄已经不小了，但是这位漂亮小姐姐会保养，可能已经30多岁了，看起来像个18岁的小姑娘，别人问她年龄时，她不愿意说，这是她的隐私。

在软件工程当中，如果包中的结构体中某些变量，我们不希望外部实体直接访问，因为这些数据比较重要，也就是说对外部的包来说他是一个私有的，对数据起到保护的作用。

##### 封装的使用

###### 1. 外部包中的结构体首字母小写

引入前面包的知识，定义一个`model`包，新建`userinfo.go`文件，这里我们将结构体`userInfo`首字母小写

```go 
package model

//class UserInfo{
//
//}
type userInfo struct {
    Name string
    Age int
    Height float32
    Eduschool string
    Hobby []string
    MoreInfo map[string]interface{}
} 
```

在我们的`main.go`文件中执行如下代码即会报错

```go 
//这种情况下是无法调用的，首字母小写它是非导出的，即外部的包无法访问。
boge := model.userInfo{
    Name:      "波哥",
    Age:       18,
    Height:    181,
    Eduschool: "北京邮电大学",
    Hobby:     []string{"coding","运动"},
    MoreInfo:  nil,
}
```

那如果外部包想访问这个结构体，如何来操作，这里就需要引入软件编程设计模式中的工厂模式了，工厂就如同现实中的工厂用于生产产品，在软件工程当中，工厂模式就是用于生成对象。这里建立工厂函数`NewUserInfo`，习惯以`New`开头

```go 
//工厂模式：生成对象
func NewUserInfo(name string,age int,height float32,eduschool string,hobby []string,moreinfo map[string]interface{}) *userInfo {
    return &userInfo{
        Name:      name,
        Age:       age,
        Height:    height,
        Eduschool: eduschool,
        Hobby:     hobby,
        MoreInfo:  moreinfo,
    }
}
```

在我们的main.go文件中执行如下代码正确

```go 
//这种情况下即可以访问了。
boge := model.NewUserInfo("小杨",18, 181,"北京邮电大学",[]string{"coding","运动"},nil)
fmt.Printf("coderyang的信息=%v\n",boge)
```

###### 2. 外部包中的结构体字段首字母小写

引入前面包的知识，定义一个`model`包，新建`product.go`文件，这里我们将结构体`Product`中的两个字段首字母小写

```go 
package model

type Product struct {
    productName string
    productPrice float32
}
```

这种情况下同样是无法调用的，首字母小写它是非导出的，即外部的包无法访问。

如果包中的结构体中某些变量，我们不希望外部实体直接访问，也就是说对外部的包来说他是一个私有的， 可是我们又想获取或者设置对应的数据，如何操作？
前面我们讲解方法接收者时有讲解过，我们可以从面向对象语言特性中得到启发，也就是面向对象中提供的 `getter` 和 `setter` 方法。对于 `setter` 方法使用 `Set` 前缀，对于` getter `方法只使用成员名，这样就形成了对我们数据的保护，称为对数据的封装。

> 工作中的应用
> 
> 比如产品的价格，我们不希望外部数据直接进行修改，因为这些数据很重要，但是我们可以提供一个接口告知使用者，你想修改我商品的价格，可以，但是必须按我的规定来，比如商品的价格必须在某个范围之内，这些是在接口中事先封装好的，不在这个范围，则拒绝修改。
> 

我们增加如下方法：

```go 
//getter setter
func (this *Product)SetProductName(_productName string )  {
    this.productName = _productName
}

func (this *Product)GetProductName() string  {
    return this.productName
}

func (this *Product)SetProductPrice(_productPrice float32)  {
    this.productPrice = _productPrice
}

func (this *Product)GetProductPrice() float32  {
    return this.productPrice
}
```

在我们的main.go文件中执行如下代码正确

```go 
product := &model.Product{}
product.SetProductName("慕课网go语言体系课")
fmt.Println(product.GetProductName())
```
