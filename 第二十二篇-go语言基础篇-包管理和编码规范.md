##### 新老版本创建项目

1. 新建项目的时候选择`go modules`
2. 新建一个包的时候 新建一个文件夹 一般小写 这个文件夹下面的所有源码中的`package`都一样
3. `import 包的路径` 我们导入的并不是包名 而是路径`ProjectName/packageName`但是我们实际在使用的时候还是使用`package`的名字，因此我们一般将`package`的名字设置为路径名字 
4. 同一个文件夹下面的`package`名称可以不和文件夹名称保持一致 但是这些源码中的`package`名称必须一样
5. 同一个包内可以直接调用在其他文件中的函数和变量，即使是以小写开头。（可以理解为将一个大文件 打散到各个子文件中）

##### go import的各种姿势

```go
// 逐个导入
import "fmt"

import "io/ioutil"

```

```go
// 使用import 组
import (
	"fmt"
	"io/ioutil"
)
```

```go
import (
	. "fmt" // 这种格式相当于把 fmt 包直接合并到当前程序中 在使用 fmt 包内的方法是可以不用加前缀 fmt. 直接使用
)

func main() {
	Println("你好")
}

```

```go
import (
	"fmt"
	int_calc "ProjectName/calc"
	float_calc "ProjectName/calc_float"
)
func main() {
	int_calc.Add(1,1)
}
// 我们可以给一个包设置别名 这样即使不同包有重复的package名字 我们也可以在一个文件使用
```

```go
import (
	_ "io/ioutil"  // 如果我们仅仅是引入 而不使用 为了防止报错 则可以使用 匿名引用
)

// 我们没有使用 仅仅引用 在初始化包函数的时候有特殊使用
// 因为当我们 import 一个包的时候 这个包里面的 init 函数会自动执行
```

> 在引用某个包时 如果只是希望执行包初始化的 `init` 函数 而不是包内部的数据时 可以使用匿名引用格式

> 匿名导入的包也会被编译到可执行文件中

几个知识点：

1. 一个包可以有多个`init`函数 包加载时会执行全部的 `init`函数，但并不能保证执行顺序，所以并不建议一个包中有多个`init`函数。可以将需要初始化的逻辑放到一个`init`函数里面。
1. 包不能出现循环引用的情况，比如包`a`引用了包`b`，包`b`引用了包`c`，如果包`c`又引用了包`a`，则编译不能通过。
1. 包的重复引用是允许的，比如包`a`引用了包`b`和包`c`，包`b`和包`c`都引用了包`d`，这种场景相当于重复引用了包`d`，这种情况是允许的，并且Go编译器保证包`d`的`init`函数只会执行一次。



##### gopath开发模式和go modules开发模式对比

`gopath`模式

1. 一定要将代码新建到`gopath`目录之下的`src`
2. 要记得设置`go env -w GO111MODULE="off"`（不然导入自己写的包报错）
3. 包的查找顺序：先查找`gopath/src`这个目录下的包是否有 没有再去`goroot/src`目录下找

`go modules`开发模式

1. 要记得设置`go env -w GO111MODULE="on"`
2. 使用`go modules`可以抛弃之前的方式
3. 即使使用之前方式，也可以自动设置为现在的`modules`模式
4. `go modules`是包管理的一个统一做法



##### go编码规范

[Go语言编码规范](https://blog.csdn.net/weixin_43865299/article/details/118979202)

