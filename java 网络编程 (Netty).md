## java 网络编程 (Netty)



### 1. 网络基础知识



### 1.1 Socket (套接字)

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20210519141746839.png" alt="image-20210519141746839" style="zoom:50%;" />



Socket 是应用层与 TCP、UDP 之间的 中间软件抽象层，它是一组接口。

在设计模式中，Socket其实就是一个门面模式，它把复杂的 TCP/IP协议族隐藏在Socket接口后面。

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20210519142154918.png" alt="image-20210519142154918" style="zoom:50%;" />

* 服务端先初始化socket，与端口bind，对端口进行监听(listen)
* 调用accept阻塞，等待客户端连接
* 客户端初始化一个socket，连接服务器
* 连接成功，客户端发送数据请求
* 服务端接收请求并处理请求，发送回应数据给客户端
* 客户端读取数据，最后关闭连接，交互结束。



**网络中进程如何通讯？**

我们需要标识一个进程，本地进程间的通信可以依靠 PID 进行标识。网络中的进程依靠 I**P地址**唯一标识网络中的主机，传输层的 "**协议+端口**" 可以唯一标识主机中的应用程序(进程)



> 在将一个地址绑定到socket的时候，**请先将主机字节序转换成为网络字节序**，而不要假定主机字节序跟网络字节序一样使用的是Big-Endian。由于 这个问题曾引发过血案！公司项目代码中由于存在这个问题，导致了很多莫名其妙的问题，所以请谨记对主机字节序不要做任何假定，务必将其转化为网络字节序再 赋给socket。

