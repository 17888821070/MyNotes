## 1、Redis运行流程

![1601969338066](C:\Users\LZX\AppData\Roaming\Typora\typora-user-images\1601969338066.png)

> 1、客户端发送一条指令给Redis服务端
>
> 2、指令进入Redis服务器的指令队列
>
> 3、Redis按顺序执行队列中的命令（原因是Redis是单线程的）
>
> 4、返回结果

##  2、Redis原理

> Redis实际是一个SocketServer

### 2.1 NIO [EPOLL](# 4、事件驱动IO（事件异步，读取数据同步，仍需程序调用read方法读取数据）)

* 手写一个简单的RedisServer

```java
public class RedisServer{
    public static void main(String[] args){
        ServerSocket server = new ServerSocket(6666);
        Socket client = server.accept();
        InputStream is = client.getInputStream();
        byte[] buffer = new byte[1024];
        int len = 0;
        while((len=is.read(buffer))!=-1){
            System.out.print(new String(buffer,0,len));
        }
    }
}

public class JedisClient {
    public static void main(String[] args) {
        Jedis js = new Jedis("127.0.0.1",6666);
        js.set("ages","1,2,3,4,5,6,7,8,9");
        js.close();
    }
}
```

```txt
运行结果：						RESP协议
*3							-- 表示有三个数据
$3							-- 第一个数据的长度（SET长度是3）
SET
$4							-- 第二个数据的长度（ages长度是4）
ages
$17							-- 第三个数据的长度（1,2,3,4,5,6,7,8,9长度是17）
1,2,3,4,5,6,7,8,9
```

### ### 2.2 单工作线程，多IO线程

> 1、redis server调用epoll，返回有哪些客户端准备好了数据
>
> 2、redis server一个一个读取客户端io中的数据
>
> 3、对数据进行处理

* 单工作线程无多IO线程情况下

  > 读取指令，计算，写入结果，一个一个的客户端进行处理
  >
  > ![1602061049690](imgs\1602061049690.png)

* 单工作线程，多IO线程情况下（6.x才有的）

  > 多个io线程一次读取多个客户端的指令
  >
  > 工作线程，按顺序指令
  >
  > io线程在各自的计算结束后进行写入
  >
  > ![1602061133837](imgs\1602061133837.png)

### 扩展知识：

> #### 四种同步IO模型
>
> <font color="red">注：阻塞io和非阻塞io和多路复用是内核的不同类型的socket</font>
>
> ##### 1、阻塞IO（同步）
>
> ​		<font color="red">阻塞io（Socket）:会阻塞在read方法 。问题：就不能再继续接收其他的连接了</font>
>
> ​				使用方法：每accept一个，就开一个线程，去read
>
> ​				问题：创建线程很浪费
>
> ```java
> serversocket.bind
> serversocket.listen
> while(true){
> 	socket = serversocket.accept()
> 	new Thread() while(true) socket.read()
> }
> ```
>
> 
>
> ##### 2、非阻塞IO（同步）
>
> <font color="red">	非阻塞io（Socket-NoBolcking）：read方法不会阻塞，而是会返回异常，并且向下执行</font>
>
> ​				使用方法：普通非阻塞IO：每accept一个，就放入list，遍历list里的socket，read接收数据
>
> ​				问题：read会有用户态和内核态的切换过程，当连接数量大的时候，很多是无效的read，浪费资源
>
> ```java
> serversocket.bind()
> serversocket.listen()
> List<socket> sockets
> while(true){
> 	newsocket = serversocket.accept()
> 	sockets.add(newsocket);
> 	foreach(socket : sockets) socket.read();
> }
> ```
>
> 
>
> ##### 3、多路复用IO（同步）
>
> ​	<font color="red">selset 多路复用，由内核进行socket的遍历，将有效的read返回，减少了用户态和内核态的切换次数</font>
>
> ​				使用方法：调用内核select()，<font color="blue">传入socket连接描述符</font>，连接描述符传入内核，内核对socket进行遍历，检查是否能read到数据，<font color="blue">返回能read到数据的socket连接描述符</font>
>
> ​				详细描述：
>
> ​						1、用户创建ServerSoceket，bind，listen
>
> ​						2、用户维护socket连接List
>
> ​						3、用户循环调用非阻塞accept，获取到新的之后放入list
>
> ​						4、用户循环调用select
>
> ​								a、输入：list的所有连接传入内核
>
> ​								b、内核遍历所有的连接，查看数据是否准备好，即read是否能成
>
> ​								c、输出：所有能够数据已准备好，能read成功的连接的list
>
> ​						5、用户遍历所有能连接成功的list
>
> ​						6、继续从第3步开始执行
>
> ​				问题：内核需要主动遍历查看是否有完成，由cpu时间片的浪费
>
> ```java
> serversocket.bind()
> serversocket.listen()
> List<socket> sockets
> while(true){
>     newsocket = serversocket.accept()
>  	sockets.add(newsocket)
>  	readysockets = serversocket.select(sockets);
>  	foreach(socket:readysockets) socket.read();
> }
> ```
>
> 
>
> ##### 4、事件驱动IO（事件异步，读取数据同步，仍需程序调用read方法读取数据）
>
> ​	<font color="red">epoll 事件驱动IO，Kernel维护一个红黑树和一个链表，红黑树保存需要监听的socket连接，链表保存触发了响应事件的socket连接</font>
>
> ​				使用方法：创建ServerSocekt时调用epoll_create，创建内核epoll，返回其文件描述符，epoll_ctl将Serversocket绑定在epoll事件的红黑树上，循环epoll_wait，接收到ServerSocket的accept事件调用epoll_ctl将ClientSocket绑定在epoll事件的红黑树上，接收到ClientSocket的read事件，调用read方法
>
> ​				详细描述：
>
> ​						1、用户创建ServerSoceket，bind，listen 假设：ServerSoceket文件描述符为fd6
>
> ​						2、用户调用epoll_create
>
> ​								a、开启事件驱动io，在内核中开辟空间，返回其文件描述符 假设：fd5
>
> ​						3、用户调用epoll_ctl，将fd6与fd5绑定，表示需要监听事件的socket
>
> ​						4、用户循环调用epoll_wait
>
> ​								a、如果内核触发了ServerSocket的accept事件，accept拿到新的连接，调用epoll_ctl绑定											到fd5进行事件监听					
>
> ​								b、如果内核触发了ClientSocket的read事件，read得到数据
>
> ​						5、继续从第3步开始执行
>
> ```java
> serversocket.bind(); //servesocket 编号fd6
> serversocket.listen();
> epoll_create(); //server 编号fd5
> epoll_ctl(fd5,fd6); //serversocket绑定在Kernel的
> while(true){
>     epoll_wait();
>     if(servercocket的accept事件) 
>     {
>      	clientsocket = fd6.accept()
>         epoll_ctl(fd5,clientsocket);   
>     }
>     else(clientsocket的read事件) {
>      	data =clientsocke.read();   
>     }
> }
> ```
>



> #### 操作系统cpu进程切换
>
> * 环境介绍
>
>   * 一个CPU、一个内核Kernel、多个用户程序（进程）、一个网卡、一个键盘、一个鼠标
>
> * 问题描述
>
>   * <font color="red">只有一个CPU，怎么执行多个不同的进程的内容，怎么接收网卡的信息，怎么响应键盘鼠标的输入？</font>
>
> * 相关概念
>
>   * 核心态：执行系统相关的程序
>
>   * 用户态：执行用户的程序
>
>   * 中断：cpu从用户态切换到核心态的唯一方式
>
>   * 中断号：内核维护的所有驱动的中断的终端号
>
>   * 中断回调函数：所有中断都有对应的中断回调函数CallBack
>
>     注：中断号和中断回调函数维护在Kernel中，当中断触发之后会回调对应的函数
>
> * 中断的方式
>
>   * CPU主动中断：晶振时间中断
>
>     时间片时间到达后主动中断，每秒进行若干次震荡，震多少次就是切成了多少个时间片
>
>   * 用户程序中断：软中断
>
>   * 硬件触发中断：硬中断
>
> * 网卡读取数据流程
>
>   ​	1、内存中会给网卡开辟一小片内存区域
>
>   ​	2、网卡接收到数据后放到为其开辟的内存区域DMA（直接内存访问）
>
>   ​	3、当数据满了，网卡触发硬中断（假设是100号中断）
>
>   ​	4、Kernel，调用100号中断的callback事件![1602052697436](imgs\1602052697436.png)



### 2.3 持久化



## 3、Jedis原理

- 连接服务器：使用的Socket
- 发送指令：按照RESP协议传输Redis指令



### 3.1 Jedis使用入门

```java
Jedis js = new Jedis("127.0.0.1:6666");
js.set("name","李籽兴");
js.close();
```

### 3.2 手写一个简单的Jedis

```java
public class JedisDemo{
    Socket socket ;

    //构造连接服务端
    public JedisDemo(String host, int port) throws IOException {
        socket = new Socket(host,port);
    }
    public void close() throws IOException {
        socket.close();
    }

    //设置Redis键值对
    public String set(String key, String value) throws IOException {
        StringBuffer sb = new StringBuffer();
        sb.append("*3").append("\r\n");
        sb.append("$3").append("\r\n");
        sb.append("SET").append("\r\n");
        sb.append("$").append(key.length()).append("\r\n");
        sb.append(key).append("\r\n");
        sb.append("$").append(value.length()).append("\r\n");
        sb.append(value).append("\r\n");

        OutputStream os = socket.getOutputStream();
        os.write(sb.toString().getBytes());
        InputStream is = socket.getInputStream();
        byte[] buffer = new byte[1024];
        int len = is.read(buffer);
        return new String(buffer,len);
    }

    //获取Redis键的值
    public String get(String key) throws IOException {
        StringBuffer sb = new StringBuffer();
        sb.append("*2").append("\r\n");
        sb.append("$3").append("\r\n");
        sb.append("GET").append("\r\n");
        sb.append("$").append(key.length()).append("\r\n");
        sb.append(key).append("\r\n");

        OutputStream os = socket.getOutputStream();
        os.write(sb.toString().getBytes());
        InputStream is = socket.getInputStream();
        byte[] buffer = new byte[1024];
        int len = is.read(buffer);
        return new String(buffer,len);
    }
}
```

## 多行执行命令

组装Pipeline命令，一次发送，返回结果为多组