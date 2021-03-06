# Redis介绍

## 1、Redis是什么redis.cn redis.io


Not only sql，将数据存储到内存中，加速数据的读取

## 2、Redis特点


* 基于内存（速度快）
* 支持数据持久化（数据写入到硬盘上）
* 以key-value方式存储数据，提供list、set、sortedSet、hash等数据结构

![1601968845178](imgs\1601968845178.png)

## 3、文件存储发展过程

| 文件（txt,java...） | 数据库（mysql）                                              | nosql（redis） |      |      |      |
| ------------------- | ------------------------------------------------------------ | ---- | ---- | ---- | ---- |
| 全量扫描，io慢      | 1、文件分治成小的page（仍需全量扫描） | <font color="blue">特征: 1、key-value存储；</font> |      |      |      |
| <font color="red">缺点：全表扫面，很慢</font> | 2、出现索引page，但需要全量扫描索引page | <font color="blue">特征: 2、速度快；</font> |      |      |      |
|  | 3、加入B+树数据结构，快速找到索引page，再找到数据page | <font color="blue">特征: 3、工作线程单线程；多线程IO（6.x才有的）</font>在多线程事务秒杀等情况下，单线程能保证多线程的任务串行化执行，不会出现并发安全问题 |      |      |      |
|  | 4、不使用索引时全量扫描page | <font color="blue">特征: 4、value有类型，且每种类型有自己的本地方法；</font>与memcache对比：redis保存的是list，map等，读取时有很多对应的方法，可以取回部分数据；memcache保存的是json字符串，读取时获取整个字符串，需要用户自行反序列化。 |      |      |      |
|  | <font color="red">缺点：表中数据量大时，增删改查会受影响</font> | <font color="blue">特征: 5、可持久化</font> |      |      |      |
|  | <font color="red">增删改：一定会变慢，因为要维护索引的结构</font> | <font color="blue">特征: 6、EPOLL</font> |      |      |      |
|  | <font color="red">查：如果是单事务，则不会有太大影响，因为B+树层数基本不变，仍可快速定位page；如果是高并发，每个线程都会读索引page，就一样会受io读取的影响</font> | 默认端口号：6379 |      |      |      |
| 解决方案：不全表扫描—>数据库 | 解决方案：常用数据放到内存—>nosql |      |      |      |      |
常识：

硬盘：读取速度受带宽，吞吐（百M，G），寻址时间（ms）限制

内存：读取速度受寻址时间（ns）限制

硬盘里1T数据，放到内存里会变小—>有些同样的数据会只有一份，不同的对象指向同一个地址

连接池是一个socket的list，跟多线程无关，可以在单线程或对线程内处理和使用socket

## 安装

![1602038845001](imgs\1602038845001.png)

## 配置

redis的配置文件redis.conf，基本配置都在这里

## 常见问题

### 1、缓存和数据库的一致性问题

要求同步性一致性高的情况，尽量不同使用redis，只能采取适当的方法降低不一致的概率

### 2、缓存雪崩问题，如何解决

缓存雪崩问题：一次性加入缓存的数据过多，导致内存过高，从而影响内存的使用导致服务宕机

解决方案：1. redis集群，通过集群方式将数据放置；2. 后端服务降级和限流：当一个接口请求次数过多，那么就会添加过多数据，可以对服务进行限流，限制访问的数量，这样就可以减少问题的出现

### 3、缓存击穿问题，如何解决

缓存击穿问题：用户恶意模拟请求很多缓存不存在的数据，缓存中没有，就会直接读取数据库，导致数据库压力大

解决方案：使用互斥锁

### 4、缓存的并发竞争问题

redis本身就是单线程执行指令的，先到先执行，其余阻塞。

或者直接在客户端将消息放到队列，使其串行化，一个一个发送执行。

### 5、redis是单线程的，但是为什么还是这么快呢，

1.单线程，避免线程之间的竞争

2.是内存中的，使用内存的，可以减少磁盘的io

3.多路复用模型，用了缓冲区的概念，selector模型来进行的


