# volatile

## 原理

* 汇编指令码

  > 生成的汇编代码，加入volatile关键字时，会多出一个lock前缀指令
  > 　　lock前缀指令实际上相当于一个内存屏障（也成内存栅栏），内存屏障会提供3个功能：
  >
  > 　　1）它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；
  >
  > 　　2）它会强制将对缓存的修改操作立即写入主存；
  >
  > 　　3）如果是写操作，它会导致其他CPU中对应的缓存行无效。
  >
  > 
  >
  > lock指令：强制将工作内存副本变量值立即写回到主内存，并无效化其他工作内存的副本；
  >
  > ​					实际是在store的时候执行了一次JMM的lock操作，在write结束后执行JMM的unlock

* JVM指令码层次

  > 对volatile修饰的变量进行读写操作前后会加入内存屏障
  >
  > - 对 volatile 变量进行写操作的时候  其写指令 上会加入 StoreStoreBarrier，其后 加入 StoreLoadBarrier。
  >
  > - - StoreStore 保证了 storestore之前的指令不会和 其之后的指令发生指令重排。本身volatile写指令本身就是写指令这就保证了 volatile写操作会在其之前所有的写操作之前完成。
  >   - StoreLoad 保证了写指令写入的数据对所有的线程可见
  >
  > - ![1602637595701](img\1602637595701.png)
  >
  > - 对 volatile 变量进行读操作的时候  其写指令 上会加入LoadLoadBarrier，其后加入 LoadStoreBarrier。
  >
  >   - loadload保证了load的是读取的最新数据
  >   - loadstore保证了写入之前，其他线程的读取已生效（这就是没保证原子性的原因）
  >
  >   ![1602637729240](img\1602637729240.png)

## 内存屏障的类型

* **LoadLoad** 屏障： 对于这样的语句 Load1; LoadLoad; Load2，在Load2及后续读取操作要读取的数据被访问前，保证Load1要读取的数据被读取完毕。
* **StoreStore** 屏障： 对于这样的语句 Store1; StoreStore; Store2，在Store2及后续写入操作执行前，保证Store1的写入操作对其它处理器可见。
* **LoadStore** 屏障： 对于这样的语句Load1; LoadStore; Store2，在Store2及后续写入操作被执行前，保证Load1要读取的数据被读取完毕。
* **StoreLoad** 屏障： 对于这样的语句Store1; StoreLoad; Load2，在Load2及后续所有读取操作执行前，保证Store1的写入对所有处理器可见。它的开销是四种屏障中最大的（冲刷写缓冲器，清空无效化队列）。在大多数处理器的实现中，这个屏障也被称为**全能屏障**，兼具其它三种内存屏障的功能。