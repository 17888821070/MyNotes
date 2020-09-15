[目录](目录.md)

# 事务

## 1. 基本使用

* 开启事务

  ```mysql
  -- 显式地开启一个事务；
  Begin;
  Start TransAction;
  ```

*  提交事务

  ```mysql
  -- 使已对数据库进行的所有修改成为永久性的；
  Commit;
  ```

* 回滚事务

  ```mysql
  -- 结束用户的事务，并撤销正在进行的所有未提交的修改；
  Rollback;
  ```

* 设置回滚点

  ```mysql
  -- 允许在事务中创建一个保存点，一个事务中可以有多个 SAVEPOINT
  SavePoint 回滚点名
  ```

* 删除回滚点

  ```mysql
  -- 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
  Release savePoint 回滚点名
  ```

* 回滚到指定回滚点

  ```mysql
  -- 把事务回滚到标记点；
  RollBack to 回滚点名
  ```

* 设置事务隔离机制

  ```mysql
  -- InnoDB 存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 和 SERIALIZABLE。
  set TransAction
  ```

## 2. 事务的并发问题

* 脏读

  读取了事务未提交的数据，并且事务也回滚了

* 幻读

  两次读取数据库，数据条目不一致

  常发生在，其他事务进行了插入删除操作的时候

  解决方案：解决幻读需要将表锁定

* 不可重复读

  两次读取数据库，数据条目一致，内容有差异

  常发生在其他事务进行了修改操作的时候

  解决方案：解决不可重复读需要锁行

* 丢失更新

  多事务提交，覆盖了先提交的数据

## 3. 事务的隔离机制

* READ UNCOMMITED 读未提交

  是最低的事务隔离级别，它允许另外一个事务可以看到这个事务未提交的数据。
  什么并发问题都没解决

* READ COMMITED 读已提交

  保证一个事物提交后才能被另外一个事务读取。另外一个事务不能读取该事物未提交的数据。系统中最常用的级别
  解决了脏读

* REPEATABLE READ 可重复读

  这种事务隔离级别可以防止脏读，不可重复读，但是可能会出现幻象读。它除了保证一个事务不能被另外一个事务读取未提交的数据之外还避免了不可重复读。mysql的默认隔离级别
  解决了脏读和不可重复读

* SERIALIZATION 串行化

  这是花费最高代价但最可靠的事务隔离级别。读写都会锁住整张表，事务被处理为顺序执行。解决了脏读、不可重复读、幻读。

## 4. 基本要素/特性ACID

* Actomicty 原子性

  事务开始后的所有操作要么全完成，要么全不做

* Consistency一致性

  数据开始前和结束后，数据库的完整性约束没有被破坏

  例子：账户总金额时完整性约束，A向B转账，必须保证A扣除之后B能够收到

* Isolation隔离性

  同一时间，一个数据只能被一个事务操作

* Durability持久性

  事务完成后，事务对数据库的更改将保存，不能回滚

## 5. 多版本并发控制MVCC

### 5.1 什么是多版本并发控制

* 一种提高并发安全的技术
  1. 读读、读写、写写并发时不阻塞，写写时才相互阻塞
  2. 在RC、RR两个隔离级别下工作
  3. 实现依赖于添加的隐藏字段、ReadView、Undo Log

#### 5.1.1 隐藏字段

InnoDB添加了三个隐藏字段

* **DBTRXID:上次操作事务id**

  表示最近一次本条数据作修改的事务id

* **DBROLLPTR：回滚指针**

  指向当前行的Undolog信息，UndoLog数据的DBROLLPTR指向再之前的数据

* **DBROWID：行id**

  随行单调递增，当找不到主键时用这个

#### 5.1.2 ReadView

读视图，用于做可见性判断

* 主要变量：

  * **lowlimited：**

    下一个将被分配的id，目前出现的最大事务的id+1（表示下一个事务将被分配的id）

  * **trx_ids：** 

    活跃事务列表，创建此ReadView时，其他未提交的事务id的集合

  * **uplimited：**

    连续已提交事务id的下一个id，活跃事务列表中事务id最小的事务id，如果集合为空unlimited=lowlimited

#### 5.1.3 Undo Log

Undo Log存储的是老版本的数据，当事务对行数据不可见时，将在Undo Log中找到此数据的历史数据，根据历史数据链表，找到具有可见性的数据返回

分为Update Undo Log和Insert Undo Log

### 5.2 行修改的具体工作流程

事务A修改
1、锁住行
2、拷贝行数据到Undo log
3、修改行数据，修改事务ID为事务A的id、修改回滚指针为Undo log中数据的地址（然后还会将修改后的最新数据写入redo log）
4、事务提交，释放锁

由于有行锁，不会出现多个事务同时修改数据的情况，最后会在undo log中形成一条历史数据链

![1598665710715](imgs\1598665710715.png)

### 5.3 如何判断可见性

通过read view

* 在创建一个新事务后，执行第一条select时会创建一个read view，read view中保存了此时在流程中未提交的事务id（trx_ids）,下一条将要被分配的事务id（low_limit_id）,此时活跃的最小的事务id（up_limit_id）

  查看某一行数据时，将数据的DB_TRX_ID保存到变量trx_id
  比较算法：
  * 查看trx_id在[0,up_limit_id )[up_limit_id ,low_limit_id)[low_limit_id,∞)哪个区间
      * 在区间[0,up_limit_id )，说明创建Read View时数据已经提交，可见
      * 在区间[up_limit_id ,low_limit_id)，说明创建Read View时，事务可能还未提交，但现在已经提交了，需要在当时记录的活跃事务中找一下。在trx_ids中查找trx_id
          * 如果能查到，不可见
          * 如果查不到，可见
  * 可见
      将该可见行的值返回。
  * 不可见
      在该记录行的 DB_ROLL_PTR 指针所指向的undo log回滚段中，取出最新的的旧事务号DB_TRX_ID, 将它赋给trx_id，然后跳到步骤1重新开始判断。

![](imgs\可见性分析.png)

### 5.4 RC和RR解决脏读、重复读的原理

* RC解决了脏读，没解决重复读、幻读

  * 解决脏读：在RC事务中，执行每一次select语句时都会重置一次ReadView，每次select对数据的可见性不一样，但对未提交事务修改的数据都是没有可见性的，只能在Undo中取得旧的数据

  * 未解决不可重复读：RC事务中，执行每一次select语句时都会重置一次ReadView，每次select对数据的可见性不一样，当第二次select时可能相比第一次select时，有部分事务提交了，可见性发生了变化，读到的数据就不一致了

  * 未解决幻读：由于ReadView中对insert的操作不会对任何行加锁，其他事务中新的数据加入并提交后，每个事务都是有可见性的

* RR解决了脏读、不可重复读，没解决幻读

  * 解决脏读：在RR事务中，只有第一个select产生了ReadView，对未提交事务修改的数据都是没有可见性的，只能在Undo中取得旧的数据

  - 解决不可重复读：在RR事务中，只有第一个select产生了ReadView，在整个事务过程中，对数据的可见性是不会发生变化的，多次读取的数据都是最新的有可见性的数据，不会有不一致的情况

  - 未解决幻读：由于ReadView中对insert的操作不会对任何行加锁，其他事务中新的数据加入并提交后，每个事务都是有可见性的

### 5.5 Mysql的默认隔离级别、应该使用的隔离级别、为什么？

1. 默认可重复读隔离级别

2. 应该采用度已提交

3.  
   * 采用读未提交(Read UnCommitted),一个事务读到另一个事务未提交读数据，这个不用多说吧，从逻辑上都说不过去！
   * 采用串行化(Serializable)，每个次读操作都会加锁，快照读失效，一般是使用mysql自带分布式事务功能时才使用该隔离级别！
   * 采用RR
     缘由一：在RR隔离级别下，存在间隙锁，导致出现死锁的几率比RC大的多！
     缘由二：在RR隔离级别下，条件列未命中索引会锁表！而在RC隔离级别下，只锁行
     缘由三：在RC隔离级别下，半一致性读(semi-consistent)特性增加了update操作的并发性！

## 5.6 autocommit

autocommit=1的情况下，如果事务中有错误，事务前的内容是会提交的
```
set autocommit= 1

BEGIN;
update a set a2 = 11 where a1 = 1;
update a set a2 = 22 where a1 = 2;
update a set a2 = 'asd' where a1 = 3;
commit;
```

autocommit=0的情况下，如果事务中有错误，事务会会滚到事务开始前
```
set autocommit= 0

BEGIN;
update a set a2 = 11 where a1 = 1;
update a set a2 = 22 where a1 = 2;
update a set a2 = 'asd' where a1 = 3;
commit;;
```