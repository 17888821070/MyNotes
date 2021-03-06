# redis持久化

redis将内存数据序列化到硬盘

重启时将硬盘中文件进行反序列化到内存

## 两种持久化方案：

### 一、RDB

> 快照保存:把redis中的数据以快照的方式保存在磁盘上（把当前时刻的数据拍成一张“照片”保存下来）
>
> 在指定时间间隔内，将内存中的数据进行快照，写入到二进制文件中，默认为dump.rdb

* 特点

> 默认的持久化方式
>
> 保存二进制文件
>
> 指定时间间隔持久化

* 提供的三种触发机制 save、bgsave、自动化

> #### 1.save触发方式
>
> ​	执行save指令期间，阻塞Redis服务器，不处理其他指令，直到RDB持久化结束。
>
> ​	覆盖掉老的rdb文件，不可取![img](imgs\save)
>
> #### 2.bgsave触发方式
>
> ​	执行bgsave指令期间，redis服务会在后台异步进行快照操作，同时可以响应客户端请求
>
> ​	redis服务器接收到bgsave指令后，执行fork执行创建子进程（此时阻塞不处理客户端命令，但很快），由子进程负责rdb持久化操作
>
> ​	基本上rdb操作都是使用bgsave	![img](imgs\bgsave.png)
>
> #### 3. 自动触发
>
> ​	redis.conf文件
>
> ​    **①save：**这里是用来配置触发 Redis的 RDB 持久化条件，也就是什么时候将内存中的数据保存到硬				盘。比如“save m n”。表示m秒内数据集存在n次修改时，自动触发bgsave。
>
> ​		默认如下配置：
>
> ​		#表示900 秒内如果至少有 1 个 key 的值变化，则保存save 900 1#表示300 秒内如果至少有 10 个 key 		的值变化，则保存save 300 10#表示60 秒内如果至少有 10000 个 key 的值变化，则保存save 60 10000

* 优缺点

> 优点：
>
> ​	文件紧凑
>
> ​	bgsave触发方式下，创建新的进程进行持久化，工作线程不需要进行IO操作
>
> ​	恢复速度快
>
> 缺点：
>
> ​	全量备份比较费时
>
> ​    需要开出新的进程
>
> ​	如果在两次bgsave期间出现断点等数据会丢失

### 二、AOF

> 日志保存：存储的是接收到得到写命令，每次接收到写命令都会追加到日志文件

* 持久化原理

> 每当有一个命令发送到redis服务器，就将其追加到aof日志文件
>
> ![img](imgs\aof.png)
>
> 问题：如果同一个key被修改过，那aof文件中就会出现两条写命令，早的写命令是无用的，且文件越来越大
>
> 解决方案：重写aof文件，根据数据库内容重写一份日志

* aof文件的重写原理

> 客户端调用bgrewriteaof
>
> redis服务器fork开启新的进程
>
> 新的进程根据当前数据快照进行aof日志文件重写
>
> ![img](imgs\aop重写原理.png)

* AOF的三种触发机制

> #### 1. always
>
> 每次有修改，就写日志
>
> 同步性强，性能差，违背了redis快的原则
>
> #### 2. everysec
>
> 每秒写一次日志
>
> 可能会丢1s的数据
>
> #### 3. no
>
> 不适用

* 优缺点

> 优点：
>
> ​	同步性较强，保护数据比较强
>
> ​	不会影响工作线程的正常工作
>
> ​	日志文件可读性强，可直接执行日志文件恢复文件
>
> 缺点：
>
> ​	日志文件大
>
> ​	恢复速度慢	