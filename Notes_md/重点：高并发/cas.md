## CAS

> compare and swap：比较并交换

## 原理

>  CAS有三个关键值
>
> ​	1、内存旧值
>
> ​	2、计算欲写入值
>
> ​	3、写入前内存值
>
> CAS保证数据写入原子性的操作
>
> ​	1、获取内存值，记为内存旧值
>
> ​	2、进行计算得到需要写入内存的值
>
> ​	3、执行Unsafe的cas操作，传入旧值、计算值，cas原语是原子操作
>
> ​			3.1 内存lock加锁
>
> ​			3.2 读取此时内存新值
>
> ​			3.3 比较传入的旧值和此新值
>
> ​					相同则将值写入返回true
>
> ​					不同则返回false回到步骤1
>
> ​			3.4 内存lock解锁
>
> ```java
> //Unsafe类的getAndAddInt方法举例
> //根据val1和val2可以指定内存中的一个地址，val4是
> public final int getAndAddInt(Object var1, long var2) {
>     int var5;
>     do {
>         var5 = this.getIntVolatile(var1, var2);
>     } while(!this.compareAndSwapInt(var1, var2, var5, var5 + 1));
> 
>     return var5;
> }
> ```

## 应用场景

与volatile一起使用，实现乐观锁，volatile保证可见性和有序性，cas保证原子性

应用于并发量不高的场景，因为高并发场景的cas操作执行成功率下降，会一直循环不是放cpu浪费资源

## 具体使用场景

1、ConcurrentHashMap在操作hash表数组上的根节点时使用了cas操作，对数组和Entry的key和value使用volatile修饰

2、Atomic系列，方法都使用了cas操作，其中value属性使用volatile修饰

