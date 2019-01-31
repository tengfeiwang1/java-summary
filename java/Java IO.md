# IO
Java 的 I/O 大概可以分成以下几类：
磁盘操作：File
字节操作：InputStream 和 OutputStream
字符操作：Reader 和 Writer
对象操作：Serializable
## 网络操作：Socket
ServerSocket：服务器端类
Socket：客户端类
服务器和客户端通过 InputStream 和 OutputStream 进行输入输出。

# nio
### 流与块
I/O 与 NIO 最重要的区别是数据打包和传输的方式，I/O 以流的方式处理数据，而 NIO 以块的方式处理数据。
面向流的 I/O 一次处理一个字节数据：一个输入流产生一个字节数据，一个输出流消费一个字节数据。
优点:为流式数据创建过滤器非常容易，链接几个过滤器，以便每个过滤器只负责复杂处理机制的一部分。
缺点:不利的一面是，面向流的 I/O 通常相当慢。
面向块的 I/O
优点:一次处理一个数据块，按块处理数据比按流处理数据要快得多。
缺点:但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。

I/O 包和 NIO 已经很好地集成了，java.io.* 已经以 NIO 为基础重新实现了，所以现在它可以利用 NIO 的一些特性。例如，java.io.* 包中的一些类包含以块的形式读写数据的方法，这使得即使在面向流的系统中，处理速度也会更快。


channel
buffer
selector:主要用于网络非阻塞通信


内存映射

nio使内存映射文件变得简单：https://www.cnblogs.com/ixenos/p/5863921.html