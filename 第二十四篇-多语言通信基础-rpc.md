##### 什么是rpc?

1. RPC(Remote Procedure Call) 远程过程调用,简单理解就是一个节点请求另一个节点的数据
2. 对应RPC的就是本地过程调用，函数调用是最常见的本地过程调用
3. 将本地过程调用变成远程过程调用会面临各种问题

##### 本地过程调用

```python
def add(a,b):
     total = a + b
     return total

total = add(1,2)
print(total)
```

上述函数调用就是本地调用(执行过程):

1. 将1和2压入add函数栈（每个函数初始化一个栈）
2. 进入add函数,将1和2分别赋值给a和b
3. 执行a+b的结果并赋值给局部的total并压栈
4. 将栈中的值取出来赋值给全局的total

##### 远程过程面临的问题

原本的本地函数放到另一个服务器上去运行，引入了很多新的问题

1. Call的id映射
2. 序列化和反序列化
3. 网络传输

###### 1. call id 映射问题:

我们怎么告诉远程机器我们要调用的函数是add，而不是sub或其他函数。在本地调用中,函数体是直接通过函数指针来指定的，我们调用add函数，编译器会自动帮我们调用函数指针。

但是在远程调用中，函数指针是不行的，因为两个进程的地址空间是完全不一样的。

所以，在RPC中，所有的函数都必须有自己的一个ID。这个ID在所有进程中都是唯一确定的。

客户端在做远程过程调用时，必须附上这个ID。然后我们还需要在客户端和服务端分别维护一个 *{函数 <--> Call ID}* 的对应表。两者的表不一定需要完全相同，但相同的函数对应的Call ID必须相同。

当客户端需要进行远程调用时，它就查一下这个表，找出相应的Call ID，然后把它传给服务端，服务端也通过查表，来确定客户端需要调用的函数，然后执行相应函数的代码。

###### 2. 序列化和反序列化的问题

客户端怎么把参数值传给远程的函数呢？

在本地调用中，我们只需要把参数压到栈里，然后让函数自己去栈里读就行。

但是在远程过程调用时，客户端跟服务端是不同的进程，不能通过内存来传递参数。甚至有时候客户端和服务端使用的都不是同一种语言。

这时候就需要客户端把参数先转成一个字节流，传给服务端后，再把字节流转成自己能读取的格式。*这个过程叫序列化和反序列化*。

同理，从服务端返回的值也需要序列化反序列化的过程。

###### 3. 网络传输

远程调用往往用在网络上，客户端和服务端是通过网络连接的。所有的数据都需要通过网络传输，因此就需要有一个网络传输层。

网络传输层需要把*Call ID* 和 *序列化后的参数字节流*传给服务端，然后再把序列化后的调用结果传回客户端。

只要能完成这两者的，都可以作为传输层使用。因此，它所使用的协议其实是不限的，能完成传输就行。尽管大部分RPC框架都使用TCP协议，但其实UDP也可以，而gRPC干脆就用了HTTP2。Java的Netty也属于这层的东西。

解决了上面三个问题，就能实现RPC了，具体过程如下：

客户端流程：

```
1. 将这个调用映射为Call ID。这里假设用最简单的字符串当Call ID的方法
2. 将Call ID，a和b序列化。可以直接将它们的值以二进制形式打包
3. 把2中得到的数据包发送给ServerAddr，这需要使用网络传输层
4. 等待服务器返回结果
5. 如果服务器调用成功，那么就将结果反序列化，并赋给total
```

服务端流程：

```
1. 在本地维护一个Call ID到函数指针的映射call_id_map，可以用dict完成
2. 等待请求，包括多线程的并发处理能力
3. 得到一个请求后，将其数据包反序列化，得到Call ID
4. 通过在call_id_map中查找，得到相应的函数指针
5. 将a和rb反序列化后，在本地调用add函数，得到结果
6. 将结果序列化后通过网络返回给Client
```

在上面的整个流程中，看到了熟悉的计算机网络的流程和web服务器的定义。

所以要实现一个RPC框架，其实只需要按以上流程实现就基本完成了。

其中：

- Call ID映射可以直接使用函数字符串，也可以使用整数ID。映射表一般就是一个哈希表。
- 序列化反序列化可以自己写，也可以使用Protobuf或者FlatBuffers之类的。
- 网络传输库可以自己写socket，或者用asio，ZeroMQ，Netty之类。

实际上真正的开发过程中，除了上面的基本功能以外还需要更多的细节：网络错误、流量控制、超时和重试等。



##### rpc、http以及restful 之间的区别

###### RPC 和 REST 区别是什么？

REST，是Representational State Transfer 的简写，中文描述表述性状态传递（是指某个瞬间状态的资源数据的快照，包括资源数据的内容、表述格式(XML、JSON)等信息。）

REST 是一种软件架构风格。这种风格的典型应用，就是HTTP。其因为简单、扩展性强的特点而广受开发者的青睐。

而RPC 呢，是 Remote Procedure Call Protocol 的简写，中文描述是远程过程调用，它可以实现客户端像调用本地服务(方法)一样调用服务器的服务(方法)。

而 RPC 可以基于 TCP/UDP，也可以基于 HTTP 协议进行传输的，按理说它和REST不是一个层面意义上的东西，不应该放在一起讨论，但是谁让REST这么流行呢，它是目前最流行的一套互联网应用程序的API设计标准，某种意义下，我们说 REST 可以其实就是指代 HTTP 协议。

###### 使用方式不同

从使用上来看，HTTP 接口只关注服务提供方，对于客户端怎么调用并不关心。接口只要保证有客户端调用时，返回对应的数据就行了。而RPC则要求客户端接口保持和服务端的一致。

REST 是服务端把方法写好，客户端并不知道具体方法。客户端只想获取资源，所以发起HTTP请求，而服务端接收到请求后根据URI经过一系列的路由才定位到方法上面去RPC是服务端提供好方法给客户端调用，客户端需要知道服务端的具体类，具体方法，然后像调用本地方法一样直接调用它。

###### 面向对象不同

从设计上来看，RPC，所谓的远程过程调用 ，是面向方法的 ，REST：所谓的 Representational state transfer ，是面向资源的，除此之外，还有一种叫做 SOA，所谓的面向服务的架构，它是面向消息的。

###### 序列化协议不同

接口调用通常包含两个部分，序列化和通信协议。

通信协议，上面已经提及了，REST 是 基于 HTTP 协议，而 RPC 可以基于 TCP/UDP，也可以基于 HTTP 协议进行传输的。

常见的序列化协议，有：json、xml、hession、protobuf、thrift、text、bytes等，REST 通常使用的是 JSON或者XML，而 RPC 使用的是 JSON-RPC，或者 XML-RPC。

通过以上几点，我们知道了 REST 和 RPC 之间有很明显的差异。


###### 为什么要采用RPC呢？

RPC 和 REST 两者的定位不同，REST 面向资源，更注重接口的规范，因为要保证通用性更强，所以对外最好通过 REST。而 RPC 面向方法，主要用于函数方法的调用，可以适合更复杂通信需求的场景。RESTful API客户端与服务端之间采用的是同步机制，当发送HTTP请求时，客户端需要等待服务端的响应。当然对于这一点是可以通过一些技术来实现异步的机制的。采用RESTful API，客户端与服务端之间虽然可以独立开发，但还是存在耦合。比如，客户端在发送请求的时，必须知道服务器的地址，且必须保证服务器正常工作。而 rpc + ralbbimq中间件可以实现低耦合的分布式集群架构。

###### 我们该如何选择这两者呢？

REST 接口更加规范，通用适配性要求高，建议对外的接口都统一成 REST。而组件内部的各个模块，可以选择 RPC，一个是不用耗费太多精力去开发和维护多套的HTTP接口，一个RPC的调用性能更高从性能角度看，由于HTTP本身提供了丰富的状态功能与扩展功能，但也正由于HTTP提供的功能过多，导致在网络传输时，需要携带的信息更多，从性能角度上讲，较为低效。而RPC服务网络传输上仅传输与业务内容相关的数据，传输数据更小，性能更高。

###### 为什么一定要rpc，不能只学http协议和restful协议吗？

1. rpc可以基于tcp直接开发自己的协议，这个是可以保持长连接的，tcp的传输效率高，并且可以一直维持链接
2. 自定义协议可以优化数据的传输

如果我们只是开发web网站或者一些服务的使用者， 那么我们用restful看起来已经足够了，但是rpc的这种模式在大量的服务中都有，比如redis协议， rabbitmq的AMQP协议， 聊天软件的协议，也就是说我们想要开发一个redis的客户端，我们只需要用我们喜欢的语言实现redis定义的协议就行了，这对于开发服务来说非常有用，一般这种协议的价值在于我们自己开发的服务之间需要通信的时候 - 那你会问了，自己开发的组件之间协作，直接调用函数不就行了吗？ - 对了，有些人已经反映过来了 -- 分布式系统，分布式系统中非常常用， 比如openstack中。 还有就是微服务！

所以掌握rpc开发，对于进阶和分布式开发就变得非常重要。

http协议1.x一般情况下一个来回就关闭连接，虽然提供了keep-alive可以保持长连接，但是依然不方便，所以就出现了http2.0， http2.0基本上可以当做tcp协议使用了。所以后面讲解到的grpc就会使用http2.0开发

##### 通过httpserver实现rpc

首先一点需要明确：一定会发起一个网络请求，一定会有个网络连接(tcp/udp)

把远程的函数变成一个http请求

```python
from http.server import HTTPServer, BaseHTTPRequestHandler

host = ('', 8003)

class TodoHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        from urllib.parse import urlparse, parse_qsl
        import json
        parsed_url = urlparse(self.path)
        qs = dict(parse_qsl(parsed_url.query))
        a = int(qs.get("a", 0))
        b = int(qs.get("b", 0))
        self.send_response(200)
        self.send_header('Content-type', "application/json")
        self.end_headers()
        self.wfile.write(json.dumps(
            {
                "result": a + b
            }
        ).encode("utf-8"))


if __name__ == '__main__':
    server = HTTPServer(host, TodoHandler)
    print("Starting server, listen at: %s:%s" % host)
    server.serve_forever()
```

本地调用

```python
def add(a, b):
    return a+b

print(add(1+2))
```

总结说来，本地程序调用的过程大致可以分为几个步骤和阶段：

- 开发者开发好的程序，并进行编译，编译成机器认可的可执行文件。
- 运行可执行文件，调用对应的功能方法，期间会读取可执行文件中的机器指令，进行入栈，出栈赋值等操作。此时，计算机由可执行程序所在的进程控制。
- 调用结束，所有的内存数据出栈，程序执行结束。计算机继续由操作系统进行控制。

远程过程调用是在两台或者多台不同的物理机器上实现的调用，其间要跨越网络进行调用。因此，我们再想通过前文本地方法调用的形式完成功能调用，就无法实现了，因为编译器无法通过编译的可执行文件来调用远程机器上的程序方法。因此需要采用RPC的方式来实现远端服务器上的程序方法的调用。

RPC技术内部原理是通过两种技术的组合来实现的：**本地方法调用 和 网络通信技术。**

**如果想要做到不用url来确定调用方法和参数怎么办？**

改进一下client的代码

```python
import requests

class Client:
    def __init__(self, url):
        self.url = url

    def add(self, a, b):
        payload = {
            "method": "add",
            "params": [a, b],
            "jsonrpc": "2.0",
            "id": 0,
        }
        response = requests.post(self.url,json=payload).json()
        print(response)
        return response["result"]

cli = Client("http://localhost:8001/jsonrpc")
print(cli.add(1,2))
```

从上面的代码我们关注几点：

1. 通信使用了json，所以json中的内容应该如何写就成为了一种协议
2. 要想实现远程调用必须要要有网络连接
3. 上面通过http连接，可以发现客户端和服务器端之间可以独立，实际上除了通过json以外还可以通过xml作为数据格式
4. rpc不应该和http拿来比较
5. rpc也不应该和restful拿来比较，我们完全可以把上述代码通过client和server端的封装将rpc编程基于restful来实现
6. rpc可以理解为一种调用风格，具体实现可以随意写，至于底层是走tcp协议还是http协议看需求

##### rpc开发的要素分析

###### rpc开发的四大要素

RPC技术在架构设计上有四部分组成，分别是：**客户端、客户端存根、服务端、服务端存根。**

**客户端(Client)：**服务调用发起方，也称为服务消费者。

**客户端存根(Client  Stub)：**该程序运行在客户端所在的计算机机器上，主要用来存储要调用的服务器的地址，另外，该程序还负责将客户端请求远端服务器程序的数据信息打包成数据包，通过网络发送给服务端Stub程序；其次，还要接收服务端Stub程序发送的调用结果数据包，并解析返回给客户端。

**服务端(Server)：**远端的计算机机器上运行的程序，其中有客户端要调用的方法。

**服务端存根(Server Stub)：**接收客户Stub程序通过网络发送的请求消息数据包，并调用服务端中真正的程序功能方法，完成功能调用；其次，将服务端执行调用的结果进行数据处理打包发送给客户端Stub程序。

了解完了RPC技术的组成结构我们来看一下具体是如何实现客户端到服务端的调用的。实际上，如果我们想要在网络中的任意两台计算机上实现远程调用过程，要解决很多问题，比如：

- 两台物理机器在网络中要建立稳定可靠的通信连接。
- 两台服务器的通信协议的定义问题，即两台服务器上的程序如何识别对方的请求和返回结果。也就是说两台计算机必须都能够识别对方发来的信息，并且能够识别出其中的请求含义和返回含义，然后才能进行处理。这其实就是通信协议所要完成的工作。

![RPC过程](https://static01.imgkr.com/temp/70fbfea6496a46bc982e98d345227e2f.png)

在上述图中，通过1-10的步骤图解的形式，说明了RPC每一步的调用过程。具体描述为：

- 1、客户端想要发起一个远程过程调用，首先通过调用本地客户端Stub程序的方式调用想要使用的功能方法名；
- 2、客户端Stub程序接收到了客户端的功能调用请求，**将客户端请求调用的方法名，携带的参数等信息做序列化操作，并打包成数据包。**
- 3、客户端Stub查找到远程服务器程序的IP地址，调用Socket通信协议，通过网络发送给服务端。
- 4、服务端Stub程序接收到客户端发送的数据包信息，并**通过约定好的协议将数据进行反序列化，得到请求的方法名和请求参数等信息。**
- 5、服务端Stub程序准备相关数据，**调用本地Server对应的功能方法进行，并传入相应的参数，进行业务处理。**
- 6、服务端程序根据已有业务逻辑执行调用过程，待业务执行结束，将执行结果返回给服务端Stub程序。
- 7、服务端Stub程序**将程序调用结果按照约定的协议进行序列化，**并通过网络发送回客户端Stub程序。
- 8、客户端Stub程序接收到服务端Stub发送的返回数据，**对数据进行反序列化操作，**并将调用返回的数据传递给客户端请求发起者。
- 9、客户端请求发起者得到调用结果，整个RPC调用过程结束。

###### rpc需要使用到的术语

通过上文一系列的文字描述和讲解，我们已经了解了RPC的由来和RPC整个调用过程。我们可以看到RPC是一系列操作的集合，其中涉及到很多对数据的操作，以及网络通信。因此，我们对RPC中涉及到的技术做一个总结和分析：

- **1、动态代理技术：** 上文中我们提到的Client Stub和Sever Stub程序，在具体的编码和开发实践过程中，都是使用动态代理技术自动生成的一段程序。

- **2、序列化和反序列化：** 在RPC调用的过程中，我们可以看到数据需要在一台机器上传输到另外一台机器上。在互联网上，所有的数据都是以字节的形式进行传输的。而我们在编程的过程中，往往都是使用数据对象，因此想要在网络上将数据对象和相关变量进行传输，就需要对数据对象做序列化和反序列化的操作。

  ​			**序列化：**把对象转换为字节序列的过程称为对象的序列化，也就是编码的过程。

  ​			**反序列化：**把字节序列恢复为对象的过程称为对象的反序列化，也就是解码的过程。

我们常见的Json,XML等相关框架都可以对数据做序列化和反序列化编解码操作。后面我们要学习的Protobuf协议，这也是一种数据编解码的协议，在RPC框架中使用的更广泛。

##### 基于XML的RPC库

使用XML序列化实现RPC

服务端：

```python
from xmlrpc.server import SimpleXMLRPCServer
class calculate:
    def add(self, x, y):
        return x + y
    def multiply(self, x, y):
        return x * y
    def subtract(self, x, y):
        return abs(x-y)
    def divide(self, x, y):
        return x/y
obj = calculate()
server = SimpleXMLRPCServer(("localhost", 8088))
# 将实例注册给rpc server
server.register_instance(obj)
print("Listening on port 8088")
server.serve_forever()
```

客户端：

```python
from xmlrpc import client
# 指定对应服务地址
server = client.ServerProxy("http://localhost:8088")
print(server.add(2, 3))

```

> 相比于web服务能够请求的方式很多 但是rpc服务只能通过相应的代理客户端

##### 基于json的rpc技术

SimpleXMLRPCServer 是基于 xml-rpc 实现的远程调用，上面我们也提到 除了 xml-rpc 之外，还有 json-rpc 协议。

那 python 如何实现基于 json-rpc 协议呢？

答案是很多，很多web框架其自身都自己实现了json-rpc，但我们要独立这些框架之外，要寻求一种较为干净的解决方案，我们使用 [jsonrpclib](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ftcalmant%2Fjsonrpclib%2F)

它与 Python 标准库的 SimpleXMLRPCServer 很类似（因为它的类名就叫做 SimpleJSONRPCServer ，不明真相的人真以为它们是亲兄弟）。或许可以说，jsonrpclib 就是仿照 SimpleXMLRPCServer 标准库来进行编写的。

它的导入与 SimpleXMLRPCServer 略有不同，因为SimpleJSONRPCServer分布在jsonrpclib库中。

服务端：

```python
from jsonrpclib.SimpleJSONRPCServer import SimpleJSONRPCServer

# 1. 实例化 server
server = SimpleJSONRPCServer(('localhost', 8081))
# 2. 将函数注册到server中
server.register_function(pow) # 注册内置函数
server.register_function(lambda x,y: x+y, 'add') # 注册匿名函数
server.register_function(lambda x: x, 'ping')
# 3. 启动server
server.serve_forever()
```

客户端：

```python
import jsonrpclib

# 建立连接
server = jsonrpclib.ServerProxy('http://localhost:8081')
print(server.add(5,6))
```

相比xmlrpc而言jsonrpclib不仅仅上面的功能 还有更多的功能比如：SSL加密、使用线程池等。

> 任何一个服务如果不具备并发接收和处理的能力那这个server就不能上线

多线程处理服务端

```python
from jsonrpclib.SimpleJSONRPCServer import PooledJSONRPCServer
from jsonrpclib.threadpool import ThreadPool

# Setup the notification and request pools
nofif_pool = ThreadPool(max_threads=10, min_threads=0)
request_pool = ThreadPool(max_threads=50, min_threads=10)

# Don't forget to start them
nofif_pool.start()
request_pool.start()

# Setup the server
server = PooledJSONRPCServer(('localhost', 8080), thread_pool=request_pool)
server.set_notification_pool(nofif_pool)

# Register methods
server.register_function(pow)
server.register_function(lambda x,y: x+y, 'add')
server.register_function(lambda x: x, 'ping')

try:
    server.serve_forever()
finally:
    # Stop the thread pools (let threads finish their current task)
    request_pool.stop()
    nofif_pool.stop()
    server.set_notification_pool(None)
```

在大型的服务中我们需要考虑的更多：超时机制（超时重试）、限流（高可用）、解耦（客户端和服务端中间加一层消息队列）、负载均衡（微服务即分布式应用的一直具体的体现）、序列化和反序列化数据压缩是否高效、框架是否支持多语言。

##### 基于zeromq的rpc框架

[zerorpc](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2F0rpc%2Fzerorpc-python)是利用 zeroMQ消息队列 + msgpack 消息序列化(二进制) 来实现类似 grpc 的功能，跨语言远程调用。

主要使用到 zeroMQ 的通信模式是 ROUTER–DEALER，模拟 grpc 的  请求-响应式 和 应答流式 RPC ：

zerorpc 还支持 PUB-SUB 通信模式的远程调用。

zerorpc实际上会依赖msgpack-python, pyzmq, future, greenlet, gevent

###### 一元调用

**服务端**

```python
import zerorpc

class HelloRPC(object):
    def hello(self, name):
        return f"Hello, {name}" 

s = zerorpc.Server(HelloRPC()) # 将类注册进去
s.bind("tcp://0.0.0.0:4242")
s.run()
```

客户端

```python
import zerorpc

c = zerorpc.Client()
c.connect("tcp://127.0.0.1:4242")
print(c.hello("RPC"))
```

###### 流式响应

服务端

```python
import zerorpc

class StreamingRPC(object):
    @zerorpc.stream #@zerorpc.stream这里的函数修饰是必须的，否则会有异常，如TypeError: can’t serialize
    def streaming_range(self, fr, to, step):
        return range(fr, to, step)

s = zerorpc.Server(StreamingRPC())
s.bind("tcp://0.0.0.0:4242")
s.run() 
```

服务端可以不停的产生数据

> 我们可以类比于python中的生成器

客户端

```python
import zerorpc

c = zerorpc.Client()
c.connect("tcp://127.0.0.1:4242")

for item in c.streaming_range(10, 20, 2):
    print(item)
```

###### 传入多个参数

服务端

```python
import zerorpc

class myRPC(object):
    def listinfo(self,message):
        return "get info : %s"%message

    def getpow(self,n,m):
        return n**m           

s = zerorpc.Server(myRPC())
s.bind("tcp://0.0.0.0:4242")
s.run()
```

客户端

```python
import zerorpc

c = zerorpc.Client()
c.connect("tcp://127.0.0.1:4242")
print(c.listinfo("this is test string"))
print(c.getpow(2,5))
```

![zerorpc的架构图](https://static01.imgkr.com/temp/622ce73ffe324fcca3fd283d278857a5.jpg)

我们看到zerorpc的调用过程更加完善，能够实现异步、限流、负载均衡功能。

