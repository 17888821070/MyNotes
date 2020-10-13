## sychronized

### 作用

> 保证共享变量的同步安全

### 用法

> * 修饰代码块
>
> * 修饰普通方法
> * 修饰静态方法

### 三种用法的区别

> 修饰代码块，锁住的是调用方法的对象（堆区）
>
> 修饰普通方法，锁住的是调用方法的对象（堆区）
>
> 修饰静态方法，锁住的是类的Class对象（堆区）

## synchronized保证线程同步的原理

> 1、synchronized会在代码块开始和结束的时候加入字节码指令monitorenter和monitorexit或者给同步方法加上acc_synchronized标识符
>
> 2、monitor是实际的对象监视锁，是一个C++类对象，存放在堆区对象的对象头中
>
> 3、monitorenter表示进入monitor，monitorexit表示退出monitor，同一时刻只有一个线程可以获取到monitor
>
> ​	<font color="red">保证了原子性，因为同一时间只有一个线程可以执行共享变量的操作，所以每个代码块就是原子的</font>
>
> ​	<font color="blue" >保证了有序性，因为将多线程并行对共享变量的操作退化成了多线程串行的操作，单线程内的指令重排不会引起最终结果的错误</font>
>
> 4、加上Monitor的原理

## monitor的原理

> monitor是一个C++类ObjectMonitor的对象，类属性如图

![1602551431918](img\1602551431918.png)

> ### OnjectMonitor关键属性
>
> ​	_owner：持有ObjectMonotor对象的线程
>
> ​	_Waitset：存放处于wait状态的线程队列
>
> ​    _EntryList：存放处于等待锁block状态的线程队列
>
> ​	_recursions：锁的冲入次数
>
> ​	_count：线程获取锁的次数
>
> ### monitor执行流程
>
> ​	1、当多线程同时执行同一个代码块时，所有线程都进入entryList
>
> ​	2、某个线程获取到锁：onwer设置为获取到锁的线程，count++
>
> ​	3、拥有锁资源的线程执行完同步代码块，owner置为null，count--，其他线程进行锁的获取
>
> ​	4、拥有锁资源的线程执行了wait方法，owner置为null，count--，此线程加入Waitset，等待唤醒
>
> ![1602552618064](img\1602552618064.png)
>
> ### monitor获取锁的方法
>
> ​	1、如果monitor的_owner是null，将 _owner设置为当前线程，锁重入计数 _recursions=1
>
> ​	2、如果monitor的_owner是当前线程，将重入锁计数 _recursions+1
>
> ​	3、如果monitor非空且非当前线程，自旋
>
> 
>
> ![img](img\lockenter.png)

### 解决了并发编程的三大特性问题

* 保证原子性

>  线程进入代码块必须持有对象锁，一次只能有一个线程持有锁，所以一个代码块内的内容是原子操作

* 可见性

> 

* 有序性