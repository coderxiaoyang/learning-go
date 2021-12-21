##### go 语言的 goroutine

针对多线程编程而言每个线程占用的内存比较多，而且系统开销很大。

go语言一开始就没有打算让我们去实例化一个线程，对于没有历史 包袱的go语言从一开始所有的库都是使用协程开发，因此go的协程才会那么火。而不是像Python，即使Python现在支持协程开发但是很多历史包还是使用多进程或者多线程开发的。

```go
func p() {
	fmt.Println("你好")
}

func main() {
	go p() // 一个方法前面加上go 就变成了一个 协程
	time.Sleep(time.Second * 2) // 不加上这句话 子协程是无法执行的
}
```

> 默认的go有一个主协程  当我们将一个函数变成协程去执行的时候  会丢到后台执行

```go
func main() {
	go func() {
		for {
			fmt.Println("循环")
		}
	}()
  // 我们可以将一个匿名函数设置为协程
	time.Sleep(time.Second * 2)
}
```

我们看下闭包可能带来的问题

```go
func main() {
	for i := 0; i < 10; i++ {
		go func() {
			for {
				fmt.Println(i) // 这个 i 为主协程传进来的 指向也是主协程的 i 可能会发生变化 打印出的值可能出现重复
				time.Sleep(time.Second)
			}
		}()

	}
}
```

我们以参数的形式将主协程的变量传入到子协程

```go
func main() {
	for i := 0; i < 10; i++ {
		go func(n int) {
			for {
				fmt.Println(n)
				time.Sleep(time.Second)
			}
		}(i)

	}

	time.Sleep(time.Second * 2)

}
```

> 协程调度先后顺序并不是先触发先执行

##### go的协程和Python的协程对比

1. 使用的简单性 - go语言的简单性 体现在启动一个协程
2. 使用Python和go都开启100w个协程

```go
func main() {
	for i := 0; i < 1000000; i++ {
		go func(n int) {
			for {
				fmt.Println(n)
				time.Sleep(time.Second)
			}
		}(i)

	}

	time.Sleep(time.Second * 30)

}
```

```python
import asyncio


async def p(n):
    while True:
        print(n)
        await asyncio.sleep(1)


async def main():
    for i in range(1000000):
        asyncio.create_task(p(i))

    await asyncio.sleep(30)


asyncio.run(main())

```

> 分别将上面100w个协程执行我们得出结论：Python单个协程占用内存少  go的协程写起来简单

##### 使用 waitgroup 控制协程退出

如何等待所有子协程结束之后主协程自动结束呢？

我们可以使用`sync`的`WaitGroup`进行控制

```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup  // 声明 WaitGroup 类型变量

func p(n int) {
	defer wg.Done() // Done 函数表示协程执行完毕 使用 defer 防止忘记
	fmt.Println(n)
}

func main() {

	for i := 0; i < 5; i++ {
		wg.Add(1)  // 协程开始前调用 表示有一个要执行
		go p(i)

	}

	wg.Wait() // 主协程一直阻塞 等待所有子协程执行完毕

}

```

`add`的数量必须和`done`的数量相等，否则会出现问题

**Add 数量大于 Done 数量**

```go
var wg sync.WaitGroup

func p(n int) {
	defer wg.Done()
	fmt.Println(n)
}

func main() {
	wg.Add(6)
	for i := 0; i < 5; i++ {
		go p(i)
	}
	wg.Wait()

}

fatal error: all goroutines are asleep - deadlock!

// 当 add 数大于 done 数的时候 将会出现死锁
```

**Add 数量小于 Done 数量**

```go
var wg sync.WaitGroup

func p(n int) {
	defer wg.Done()
	fmt.Println(n)
}

func main() {
	wg.Add(2)
	for i := 0; i < 5; i++ {
		go p(i)
	}
	wg.Wait()

}
4
1

// 如果 add 数量小于 实际产生的协程数 则可能出现协程漏掉未执行的可能
```

##### 使用互斥锁同步协程

一般使用到锁的地方就是存在资源竞争的场景

```go
var wg sync.WaitGroup
var total int

// 一个协程执行 100000 次加法
func add() {
	defer wg.Done()
	for i := 0; i < 100000; i++ {
		total = total + 1
    // 1. 从 total 取出值 
    // 2. 将 total + 1
    // 3. 将 total + 1 的计算结果放入到 total 中
    // 如果将上面整个过程加把锁 则可以保证执行期间不会被打断
    // 同一时刻只有一个执行过程拥有锁
	}
}

// 一个协程执行 100000 次减法
func sub() {
	defer wg.Done()
	for i := 0; i < 100000; i++ {
		total = total - 1
    // 1. 从 total 取出值 
    // 2. 将 total - 1
    // 3. 将 total - 1 的计算结果放入到 total 中
    // 等执行完毕之后再把锁释放掉
	}

}

// 理论上得到的最后结果 total 为 0 
func main() {
	wg.Add(2)
	go add()
	go sub()
	wg.Wait()
	fmt.Println(total)
}

// 实际上得到的 total 并不是 0 
// 原因是 加 减 赋值操作不是原子性的 可能会发生中断 导致数据混乱
```

我们针对需要共享的资源加上一把锁就可以解决资源竞争问题了

```go
var wg sync.WaitGroup
var total int
var lock sync.Mutex

func add() {
	defer wg.Done()
	for i := 0; i < 100000; i++ {
    // 需要完整执行的地方加上锁
		lock.Lock()
		total += 1
		lock.Unlock()
    // 使用完锁之后必须进行释放  
    // 释放完毕之后是否再次抢到这把锁不一定
	}
}

func sub() {
	defer wg.Done()
	for i := 0; i < 100000; i++ {
		lock.Lock()
		total -= 1
		lock.Unlock()
	}

}

func main() {
	wg.Add(2)
	go add()
	go sub()
	wg.Wait()
	fmt.Println(total)
}
```

加锁和释放锁是比较耗性能的 一般能不用锁就不用锁

##### 使用RWMutex锁同步协程

读写锁：针对不同的协程读是不互斥（不会相互竞争），写和读之间、写和写之间互斥（存在竞争锁）

```go
var wg sync.WaitGroup
var rwLock sync.RWMutex

func read() {
	defer wg.Done()
	rwLock.RLock() // 读锁
	fmt.Println("开始读数据")
	time.Sleep(time.Second * 2)
	fmt.Println("读取数据完毕")
	rwLock.RUnlock()// 释放读锁
}

func write() {
	defer wg.Done()
	rwLock.Lock() // 写锁
	fmt.Println("开始写数据")
	time.Sleep(time.Second * 10)
	fmt.Println("写数据完毕")
	rwLock.Unlock() // 释放写锁

}

func main() {
	wg.Add(5)
	for i := 0; i < 5; i++ {
		go read()
	}
	go write()
	wg.Wait()

}

开始读数据
开始读数据
读取数据完毕
读取数据完毕
// 一开始可能先调度了两个读协程 等2秒结束之后调度到了写协程
开始写数据
// 阻塞写操作 10 秒
写数据完毕
// 等写操作完毕之后 开启新的调度
开始读数据
开始读数据
开始读数据
读取数据完毕
读取数据完毕
读取数据完毕
// 我们看到 读操作无论多少个都是在2秒完成 这是因为读之间不是互斥的
```

##### 通过channel进行goroutine间的通信

不同的 goroutine 之间使用 channel 进行通信

```go
func main() {
	var msg chan int // go语言 channel 初始化的时候必须指明内部数据类型
	// 第一种初始化方式：无缓冲（消息队列没有空间）
	msg = make(chan int) // channel 定义之后必须初始化 否则是一个 nil
	// 在 go 语言中 使用 make 初始化的有三种： 1. slice 2. map 3. channel
	msg <- 1 // 将 1 放到 channel 中

	data := <-msg // 去 channel 中取值 赋值给 data

	fmt.Println(data)

}

fatal error: all goroutines are asleep - deadlock!
// 出现死锁的原因是 我们使用的初始化方式是无缓冲的
```

我们设置 1 个缓冲空间就能正常运行了

```go
func main() {
	var msg chan int 
	msg = make(chan int, 1) // 初始化的时候设置 1 个缓冲空间
	msg <- 1 
	data := <-msg 
	fmt.Println(data)
}
```

同样当我们只设置 1 个缓冲空间 存放数量大于 1 的时候也会出现死锁

```go
func main() {
	var msg chan int
	msg = make(chan int, 1)
	msg <- 1
	msg <- 2
	data := <-msg
	fmt.Println(data)
}

fatal error: all goroutines are asleep - deadlock!
```

这么看下来还是和Python的队列有区别的，当Python队列满的时候直接阻塞。go的队列当存储数据大于缓冲空间的时候直接产生了死锁。

我们开启一个协程 及时消费掉队列中的数据

```go
var wg sync.WaitGroup

func consumer(msg chan int) {
	defer wg.Done()
	data := <-msg
	fmt.Println(data)
}

func main() {
	var msg chan int
	msg = make(chan int, 1)
	msg <- 1
	wg.Add(1)
	go consumer(msg) // 调用消费协程 
	wg.Wait()
}
```

从 channel 中取值和往 channel 中存值必须保持一致，否则就会出现`deadlock`.

##### 通过 for range 遍历 channel 和 close 关闭 channel

我们可以使用 `for range`从channel中读取值

```go
var wg sync.WaitGroup

func consumer(msg chan int) {
  defer wg.Done()
	for data := range msg {
		fmt.Println(data)
	}
}

func main() {
	var msg chan int
	msg = make(chan int, 1)
	msg <- 1
	wg.Add(1)
	go consumer(msg)
	msg <- 2
	wg.Wait()
}

1
2
fatal error: all goroutines are asleep - deadlock!

// 但是 for range 会一直从 channel 中取值 当缓冲数据取完的时候 就会报错
```

为了防止`for range`遍历获取 channel 数据报错 我们可以在发送完数据之后关闭 channel。

```go
var wg sync.WaitGroup

func consumer(msg chan int) {
	defer wg.Done()
	for data := range msg {
		fmt.Println(data)
	}
}

func main() {
	var msg chan int
	msg = make(chan int, 1)
	msg <- 1
	wg.Add(1)
	go consumer(msg)
	msg <- 2
	close(msg) // 关闭 channel 之后 for range 遍历获取到所有数据 不再抛出异常
	wg.Wait()
}
```

已经关闭的 channel 不能再发送数据了

```go
close(msg)
msg <- 3
// 关闭之后 再发送会报以下错误
panic: send on closed channel
```

已经关闭的 channel 依旧能够取到数据 直到数据取完为止

```go
var wg sync.WaitGroup

func consumer(msg chan int) {
	defer wg.Done()
	for data := range msg {
		fmt.Println(data)
		time.Sleep(time.Second) // 我们阻塞一会 确保 2 能进入 channel
	}
}

func main() {
	var msg chan int
	msg = make(chan int, 1)
	msg <- 1
	wg.Add(1)
	go consumer(msg)
	msg <- 2
	close(msg)
	wg.Wait()
}

1
2
```

我们把取数据过程改为无限循环

```go
func consumer(msg chan int) {
	defer wg.Done()
	for {
		data := <-msg
		fmt.Println(data)
		time.Sleep(time.Second)
	}
}
// 输出
1
2
0
// 当我们关闭 channel 之后还是可以取值的 当取完之后 就是类型的默认值了
// 但是这并不是我们想要的结果 我们希望在关闭之后 获取完数据之后就停止获取
```

```go
func consumer(msg chan int) {
	defer wg.Done()
	for {
		data, ok := <- msg  // 可以设置两个接收值 第二个表示channel是否开启
    // 未开启则停止
		if !ok {
			break
		}
		fmt.Println(data, ok)
		time.Sleep(time.Second)

	}
}
```

上面我们一直在使用有缓冲空间的队列 对于无缓冲空间的队列 我们应该如何使用呢？

```go
var wg sync.WaitGroup

func consumer(msg chan int) {
	defer wg.Done()
	for {
		data, ok := <-msg
		if !ok {
			break
		}
		fmt.Println(data, ok)
		time.Sleep(time.Second)
	}
}

func main() {
	var msg chan int
	msg = make(chan int)
  // 我们在往 channel 中添加数据之前开启一个协程用于读取 channel
	wg.Add(1)
	go consumer(msg)
	msg <- 1
	close(msg) // close 必不可少 否则将会 死锁
	wg.Wait()
}
// 这样就是无缓冲的 channel 使用过程 如果不事先开启一个协程去读取 channel 里面的数据 则在往 channel 中添加数据的时候就会报错
```

##### 双向和单向的 channel

如果按照是否有缓冲可以将 channel 分为：有缓冲类型、无缓冲类型。

如果按照存取数据方向可以分为：双向 channel 、单向 channel

**双向 channel 的使用**

```go
var wg sync.WaitGroup

// 我们将双向 channel 传入到函数中
func consumer(msg chan int) {
	defer wg.Done()
	for {
		data, ok := <-msg // 从双向 channel 中取值
		if !ok {
			break
		}
		fmt.Println(data, ok)
		time.Sleep(time.Second * 2)
		fmt.Println("放进去值 2")
		msg <- 2 // 往双向 channel 中放值
	}
}

func main() {
	var msg chan int
	msg = make(chan int)
	wg.Add(1)
	go consumer(msg)
	msg <- 1 // 从双向 channel 中取值 
	data := <-msg // 往双向 channel 中放值
	fmt.Println("取出值", data)
	close(msg)
	wg.Wait()
}
```

**单向 channel 的使用**

go语言为了安全还提供了单向的 channel

```go
var msg chan <- int // 表示只能往里面存值的 channel
```

```go
var msg <- chan int // 表示只能从里面取值的 channel
```

一般单向 channel 的定义往往是存在函数传入的参数中

```go
// 我们需要传入一个 channel 但是不能往传入的这个 channel 放值
func consumer(msg <- chan int) {
	defer wg.Done()
	for {
		data, ok := <-msg // 只支持取值操作
		if !ok {
			break
		}
		fmt.Println(data, ok)
		time.Sleep(time.Second * 2)
	}
}

func main() {
	var msg chan int // 定义一个双向 channel
	msg = make(chan int)
	wg.Add(1)
	go consumer(msg) // 我们参数定义的是一个单向 channel 但是我们传入一个双向 channel 并没有报错
	msg <- 1 // 
	close(msg)
	wg.Wait()
}
```

> 双向 channel 是可以转换为单向 channel 的，但是 单向 channel 是不能转为双向 channel 的

单向 channel 能够保证数据的安全性 在某些时刻我们只想存数据或者取数据就可以使用单向 channel。

##### 使用 channel 容易出现 deadlock 的原因

我们在使用 channel 的时候一不小心就会出现`deadlock`，这是为什呢？

```go
func main() {
	var msg chan int
	msg = make(chan int) // 声明无缓冲的 channel
	msg <- 1 // 往无缓冲的 channel 中放值会引发 deadlock!
}
```

当我们声明一个无缓冲的 channel 在没有启动一个消费者之前 就往 channel 里面放数据就会报错。

> 在go语言中 channel是多个  goroutine 之间协程安全的  通过锁来实现线程安全

当我们执行往 channel 中放数据的时候(`msg <- 1`)会阻塞着，在阻塞之前会获取一把锁，这把锁只有等到数据被消费之后才会释放。（因为没有缓冲空间，当往 channel 中存值的时候，只有消费端取走数据 主协程才会释放锁继续执行。如果存在缓冲区，主协程会将数据放到缓冲区之后释放锁进行下一步操作）

完整的流程：第一步获取锁，第二步等到消费者消费，第三步释放锁

因为我们在开启消费者之前进行了放值操作所以永远等不到消费者，即不会释放锁，因此会产生死锁。

解决办法就是我们在放值之前就将消费者激活

```go
var wg sync.WaitGroup

func consumer(msg <-chan int) {
	defer wg.Done()
	for {
		data, ok := <-msg
		if !ok {
			break
		}
		fmt.Println(data, ok)
		time.Sleep(time.Second * 2)
	}
}

func main() {
	var msg chan int
	msg = make(chan int)
	wg.Add(1)
  // 在放值之前就开启一个协程去取值
	go consumer(msg)
	msg <- 1
	close(msg)
	wg.Wait()
}
```

##### go语言中select的应用场景

C 语言的 `select` 系统调用可以同时监听多个文件描述符的可读或者可写的状态，Go 语言中的 `select` 也能够让 Goroutine 同时等待多个 Channel 可读或者可写，在多个文件或者 Channel状态改变之前，`select` 会一直阻塞当前线程或者 Goroutine。

`select` 是与 `switch` 相似的控制结构，与 `switch` 不同的是，`select` 中虽然也有多个 `case`，但是这些 `case` 中的表达式必须都是 Channel 的收发操作

在 Go 语言中使用 `select` 控制结构时，会遇到两个有趣的现象：

1. `select` 能在 Channel 上进行非阻塞的收发操作；
2. `select` 在遇到多个 Channel 同时响应时，会随机执行一种情况；

```go
func main() {
	ch1 := make(chan int, 1)
	ch2 := make(chan int, 1)

	ch1 <- 1
	ch2 <- 2

	select {
	case data := <-ch1:
		fmt.Println(data)
	case data := <-ch2:
		fmt.Println(data)
	}
}
// 执行多次 每次执行结果都是随机的
```

**select的应用场景：timeout的超时机制**

```go
func main() {
	
  // 使用全局变量
	timeout := false
	
  // 该 goroutine 如果执行时间超过 1 秒就报告给主 goroutine
	go func() {
		time.Sleep(time.Second * 2)
		timeout = true
	}()
	
  // 使用 for 循环一直检测
	for {
		if timeout {
			fmt.Println("结束")
			break
		}

		time.Sleep(time.Millisecond * 10)
	}
}
```

我们可以使用 channel 实现这个功能

```go
func main() {
	// 初始化一个 channel 
	timeout := make(chan bool, 1)

	go func() {
		time.Sleep(time.Second * 2)
		timeout <- true
	}()
	
  // 一直阻塞等待从 channel 中取值
	fmt.Println(<-timeout)

}
```

通过上面方式我们可以检测到子goroutine的超时 不过我们可以使用 select 来实现

```go
func main() {

	timeout := make(chan bool, 1)

	go func() {
		time.Sleep(time.Second * 2)
		timeout <- true
	}()
	
  // 完成超时检测 
	select {
	case <-timeout: // 如果没有放值到 channel 这里会一直阻塞
		fmt.Println("超时了")
	}

}
```

select 监听只要有一个 case 准备就绪就不会阻塞了 会继续往下执行

```go
func main() {

	timeout := make(chan bool, 1)

	timeout2 := make(chan bool, 1)

	go func() {
		time.Sleep(time.Second * 5)
		timeout <- true
	}()

	go func() {
		time.Sleep(time.Second * 2)
		timeout2 <- true
	}()

	select {
	case <-timeout:
		fmt.Println("timeout 超时了")
	case <-timeout2:
		fmt.Println("timeout2 超时了")
	}
	fmt.Println("主协程结束了")

}
// 结果
timeout2 超时了
主协程结束了
```

在通常情况下，`select` 语句会阻塞当前 Goroutine 并等待多个 Channel 中的一个达到可以收发的状态。但是如果 `select` 控制结构中包含 `default` 语句，那么这个 `select` 语句在执行时会遇到以下两种情况：

1. 当存在可以收发的 Channel 时，直接处理该 Channel 对应的 `case`；
2. 当不存在可以收发的 Channel 时，执行 `default` 中的语句；

```go
func main() {

	timeout := make(chan bool, 1)

	timeout2 := make(chan bool, 1)

	go func() {
		time.Sleep(time.Second * 5)
		timeout <- true
	}()

	go func() {
		time.Sleep(time.Second * 2)
		timeout2 <- true
	}()

	select {
	case <-timeout:
		fmt.Println("timeout 超时了")
	case <-timeout2:
		fmt.Println("timeout2 超时了")
	default:
		fmt.Println("不阻塞 直接执行")
	}
	fmt.Println("主协程结束了")

}
// 结果
不阻塞 直接执行
主协程结束了
```

> 即可以通过判断是否执行了 default 来判断 channel 是否阻塞

##### context的应用场景

我们模拟一个监控CPU信息的场景来看下 `context`的使用

```go
var wg sync.WaitGroup

func cpuInfo() {
	defer wg.Done()
	for {
		time.Sleep(time.Second * 2)
		fmt.Println("CPU信息读取完毕")
	}

}

func main() {

	wg.Add(1)
	go cpuInfo()
	wg.Wait()

	fmt.Println("信息监控完成")
}

```

现在的监控协程一直执行 但是我们希望执行一段时间之后就停止掉 我们可以使用以下方式实现

**方式一：全局变量**

```go
var wg sync.WaitGroup

var stop bool

func cpuInfo() {
	defer wg.Done()
	for {
		if stop {
			break
		}
		time.Sleep(time.Second * 2)
		fmt.Println("CPU信息读取完毕")
	}

}

func main() {

	wg.Add(1)
	go cpuInfo()
	// 我们设置 6 秒钟之后将全局变量设置为 true
	time.Sleep(time.Second * 6)
	stop = true
	wg.Wait()
	fmt.Println("信息监控完成")
}
```

这种方式虽然可以实现中断子协程但是不够优雅

**方式二：通过channel**

```go
var wg sync.WaitGroup

var stop chan bool = make(chan bool)

func cpuInfo(stop chan bool) {
	defer wg.Done()
	for {
    // 我们使用 select 监控是否收到停止信号 
		select {
		case <-stop:
      // 如果收到停止信号则立即退出
			fmt.Println("退出CPU监控")
			return
		default:
      // 如果没有收到停止信号 则继续读取信息 进入下一轮循环
			time.Sleep(time.Second * 2)
			fmt.Println("CPU信息读取完成")
		}
	}

}

func main() {
	wg.Add(1)
	go cpuInfo(stop)
	time.Sleep(time.Second * 6)
	stop <- true
	wg.Wait()
	fmt.Println("信息监控完成")
}
```

**方式三：使用 context**

Go1.7给我们提供了 context 更加方便的控制子协程的退出

```go
var wg sync.WaitGroup

func cpuInfo(ctx context.Context) {
	defer wg.Done()
	for {
		select {
      // 同样通过判断 context 是否收到取消通知
		case <- ctx.Done():
			fmt.Println("取消监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("CPU信息读取完成")

		}

	}

}

func main() {
	// 初始化一个 context 
	ctx, cancle := context.WithCancel(context.Background())

	wg.Add(1)
	go cpuInfo(ctx)

	time.Sleep(time.Second * 6)
  // 通知 子协程 取消
	cancle()
	wg.Wait()
	fmt.Println("信息监控完成")
}
```

> context 内部使用的还是 channel

如果 context 仅仅按照上面方式使用和 channel 是没有区别的，使用 context 可以同时取消个多协程 只要传入的 context 是同一个

```go
var wg sync.WaitGroup

func cpuInfo(ctx context.Context) {
	defer wg.Done()
	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消CPU监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("CPU信息读取完成")

		}

	}

}

func memoryInfo(ctx context.Context) {
	defer wg.Done()
	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消内存监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("内存信息读取完成")

		}

	}

}

func main() {

	ctx, cancle := context.WithCancel(context.Background())

	wg.Add(2)
	go cpuInfo(ctx)
	go memoryInfo(ctx)

	time.Sleep(time.Second * 6)
	cancle()
	wg.Wait()
	fmt.Println("信息监控完成")
}
// 输出
CPU信息读取完成
内存信息读取完成
内存信息读取完成
CPU信息读取完成
内存信息读取完成
取消内存监控
CPU信息读取完成
取消CPU监控
信息监控完成
```

我们还可以控制嵌套的协程退出

```go
var wg sync.WaitGroup

func cpuInfo(ctx context.Context) {
	defer wg.Done()
  // 在协程中 调用另一个协程 传入 context 是同一个 
	go memoryInfo(ctx)
	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消CPU监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("CPU信息读取完成")

		}

	}

}

func memoryInfo(ctx context.Context) {
	defer wg.Done()
	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消内存监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("内存信息读取完成")

		}

	}

}

func main() {

	ctx, cancle := context.WithCancel(context.Background())

	wg.Add(2)
	go cpuInfo(ctx)

	time.Sleep(time.Second * 6)
	cancle()
	wg.Wait()
	fmt.Println("信息监控完成")
}
```

我们可以根据父 context 创建一个 子 context 如果父 context 取消的话 那么子 context 也会取消

```go
var wg sync.WaitGroup

func cpuInfo(ctx context.Context) {
	defer wg.Done()
  // 根据父 context 创建的子 context 会受到父 context 的影响
	childctx, _ := context.WithCancel(ctx)
	go memoryInfo(childctx)
	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消CPU监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("CPU信息读取完成")

		}

	}

}

func memoryInfo(ctx context.Context) {
	defer wg.Done()
	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消内存监控")
			return
		default:
			time.Sleep(time.Second * 2)
			fmt.Println("内存信息读取完成")

		}

	}

}

func main() {

	ctx, cancle := context.WithCancel(context.Background())

	wg.Add(2)
	go cpuInfo(ctx)

	time.Sleep(time.Second * 6)
	cancle()
	wg.Wait()
	fmt.Println("信息监控完成")
}
```

> 父 context 取消 那么这个父 context 生成的子 context 也会被取消

##### 通过 context 控制超时

除了 `WithCancle` context 还提供了其他机制

```go
context.WithDeadline() 在某个时间点取消
context.WithTimeout() 超过一定时间取消
context.WithValue()
```

```go
var wg sync.WaitGroup

func cpuInfo(ctx context.Context) {
	defer wg.Done()

	for {
		select {
		case <-ctx.Done():
			fmt.Println("取消CPU监控")
			return
		default:
			time.Sleep(time.Second)
			fmt.Println("CPU信息读取完成")

		}

	}

}

func main() {

	ctx, _ := context.WithTimeout(context.Background(), 5*time.Second)

	wg.Add(1)
	go cpuInfo(ctx)
	// 相比于 WithCancle WithTimeout 不需要手动调用 cancle 函数
  // 不过我们也可提前 在超时之前取消 
	wg.Wait()
	fmt.Println("信息监控完成")
}
```

