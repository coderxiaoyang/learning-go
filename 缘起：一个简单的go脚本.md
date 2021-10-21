我们看下一个简单的go文件长什么样子

```go 
package main

import "fmt"

func main() {

	fmt.Println("hello golang")

}
```

第一行代码指定当前文件属于哪个包 如果我们想直接运行 则需要设置为`main`（无需和文件名保持一致）

第二行代码为导入我们需要的包

第三行代码为定义程序入口函数 `main`

第四行代码为输出一串字符串


对于函数`main`无需像python那样显示调用，程序运行时则进入该函数执行
