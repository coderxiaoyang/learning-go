
### 方法

##### 一、方法的概述

方法是什么 实际上也是函数，声明时，在关键字func 和函数名之间增加了一个参数,这个参数被称作接收者,将函数与接收者的类型绑在一起。如果一个函数有接收者，这个函数就被称为方法。

> Go 语言里有两种类型的接收者：值接收者，指针接收者

##### 二、方法作用

方法有哪些作用呢?

给用户定义的类型添加新的行为

##### 三、方法定义与使用

我们一起来看一看方法的声明定义方式

定义格式

```go 
func (var vartype)funcname(参数列表) (返回值列表) {
       函数体
   }
```

##### 方法演示

为我们自定义的类型增加方法
```go 
- 1.定义结构体
type miniaction struct {
    name string //接口名称 热销商品，某一个分类下的商品列表
    router string //路由地址 https://api.imooc.com/hotproduct
    action string //路由对应的方法名 func hotproduct() {}
}
- 2.1.为结构体增加方法
func (mini miniaction)getMiniInfo() {
    fmt.Printf("mini.name=%s,mini.router=%s,mini.action=%s\n",mini.name,mini.router,mini.action)
}

- 2.2.为结构体增加方法
func (this *miniaction)miniInfo()  {
    fmt.Printf("mini.name=%s,mini.router=%s,mini.action=%s\n",this.name,this.router,this.action)
}
- 3.1.实例化结构体

mini1 := miniaction{
        name:   "获取商品列表",
        router: "productList",
        action: "productList()",
    }
    //调用
//接收者有两种，一种是值类型，一种是指针类型
    //接收者是值类型的时候，调用者可以是值类型，也可以是引用类型
    mini1.getMiniInfo()
- 3.2.实例化结构体

mini2 := &miniaction{
        name:   "获取分类下的商品列表",
        router: "productListByCategory",
        action: "productListByCategory()",
    }
mini2.getMiniInfo()
- //接收者是一个指针类型,调用者可以是值类型，也可以指针类型
    mini2.miniInfo()
    (*mini2).miniInfo()
    mini1.miniInfo()
```

##### 四、方法注意事项

> 1.接收者是值类型的时候，调用者可以是值类型，也可以是引用类型
> 
> 2.接收者是一个指针类型,调用者可以是值类型，也可以指针类型
> 
> 3.定义的方法只能通过指定的类型来调用，不能像函数一样来调用
> 
> 4.方法的接收者对应的变量名，习惯使用this,self

```go 
//比如传统编程语言当中的Person类
class Person{
    public name
    function info(){
        this.name
    }
}

p = new Person()
p.info()
```

> 自定义类型也可以做为方法的接收者

```go 
- 1.自定义类型
type float float32

- 2.为自定义类型定义方法

func (radius float)getCircleAround() float {
    return radius*3.14*2
}

- 3.定义一个自定义的类型并调用方法
var radius float = 2
fmt.Println(radius.getCircleAround())
```

`接收者类型限定`
> 接收者不能是接口,因为接口是一类事物的抽象，而方法是某个具体事物的实现，所以不能把一个接口类型的变量做为接收者

##### 五、方法和函数的区别

> 值类型，引用类型都可做为参数使用
> 
> 做为参数时遵循函数当中的值传递与引用传递规则
>
> 如果想要方法改变接收者的数据，就在接收者的指针类型上定义该方法。否则，就在普通的值类型上定义方法。
> 
> 方法调用:调用者.方法名（参数）
> 
> 函数调用：函数名（参数列表）
> 
> 首字母大小写，遵循包的规则
