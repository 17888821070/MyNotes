# 锁

![img](imgs\锁分类.png)

## 1. 按锁的粒度分类

### 1.1 分类（表锁&行锁）

* 行锁
  * 开销大，加锁慢；会出现死锁；锁定粒度小，发生锁冲突的概率低，并发度高
  * 适用于有大量按索引条件并发更新少量不同数据，同时又有并发查询的应用。
* 页锁
  * 开销和加锁速度介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般
* 表锁
  * 开销小，加锁快；不会出现死锁；锁定力度大，发生锁冲突概率高，并发度最低
  * 适合以查询为主，只有少量按索引条件更新数据的应用

![1598681865941](imgs\1598681838324.png)

MyISAM只支持表锁

Innodb支持表锁和行锁，默认使用行锁

### 1.2 状态（Innodb）

只有明确主键，才会执行行锁，否则执行表锁（MyISAM不支持主键，所以没有行锁）

* 无锁

  ```sql
  # 主键值不存在，不锁
  select * from MyTable where id = -1 for update
  ```

* 行锁

  ```sql
  # 主键明确，锁行
  select * from MyTqble where id = 2 for update
  select * from MyTable where id = 2 and name = '李籽兴' for update
  ```

* 表锁

  ```sql
  # 主键不明确
  select * from MyTable where id <> 2 for update
  select * from MyTable where name = '李籽兴' for update
  ```

## 2. 算法(锁机制)

### 2.1 行锁算法

* Record Lock（普通行锁）

  * 键值在条件范围内
  * 记录存在
  * 单个行记录上的锁

* Gap Lock（间隙锁）

  * 键值不在条件范围内，叫做“间隙”GAP，引擎会对这个间隙加锁，这种机制叫做GAP机制

  * 间隙锁，锁定一个范围，但不包括记录本身。GAP锁的目的，是为了防止同一事务的两次当前读，出现幻读的情况

    当我们**用范围条件检索数据**而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给**符合范围条件的已有数据记录的索引项加锁**；对于键值在条件范围内但并不存在的记录，叫做“间隙（GAP)”。InnoDB也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁。

    值得注意的是：间隙锁只会在 `Repeatableread`隔离级别下使用~

    例子：假如emp表中只有101条记录，其empid的值分别是1,2,...,100,101

    ```
    Select * from  emp where empid > 100 for update;
    ```

    上面是一个范围查询，InnoDB**不仅**会对符合条件的empid值为101的记录加锁，也会对**empid大于101（这些记录并不存在）的“间隙”加锁**。

    InnoDB使用间隙锁的目的有两个：

    - **为了防止幻读**(上面也说了， `Repeatableread`隔离级别下再通过GAP锁即可避免了幻读)

    - **满足恢复和复制的需要**

      - MySQL的恢复机制要求：**在一个事务未提交前，其他并发事务不能插入满足其锁定条件的任何记录，也就是不允许出现幻读**

* Next-Key Lock（行&间隙）

  * 键值一部分在条件范围内，一部分不在条件范围内

  * 1+2，锁定一个范围，并且锁定记录本身。对于行的查询，都是采用该方法，主要目的是解决幻读的问题。
  
    ```sql
    # id 当前只有50条记录
    select * from MyTable where id>30 for update
    ```

### 2.2 表锁算法

* 意向锁
  * 当一个事务带着表锁去访问一个被加了行锁的资源，那么拥有行锁的事务的表锁升级位表意向锁，将表锁住
  ```sql
  # 事务A持有行锁
  select * from MyTable wehre id = 10 for update
  # 事务B持有表锁
  select * from MyTable where name='李籽兴' for update
  
  # 当事务A先启动，事务B后启动，事务A对id=10的数据的行锁会升级位表意向锁
  ```
* 自增锁

	* 如果一个事务正在相关表中插入自增数据，会将表锁住，其他表必须等待

## 3. 锁实现

### 3.1 共享锁和排他锁

行锁和表锁是粒度的概念，共享锁和排他锁是他们的具体实现

![1598750056498](imgs\1598750056498.png)

* 无锁

```sql
BEGIN;
SELECT * FROM `a`;
COMMIT;
```

* 共享锁（读锁）
  允许多个事务同时获得共享锁，允许读数据

```mysql
BEGIN;
-- 获取排他锁
SELECT * FROM `a` where a1 = 1 for share;
COMMIT;
```

* 排他锁（写锁）
想要获得排他锁，就要等其他事务释放该数据的共享锁或排他锁
```mysql
BEGIN;
-- 获取排他锁
SELECT * FROM `a` where a1 = 1 for update;
update ...
delete ...
insert ...
COMMIT;
```
未commit前，for share 可以多次获取。
未commit前，for update只有一个事务可以获取。且不能有share被人获取着

### 3.2 乐观锁和悲观锁

> 不管是什么锁都需要加失败尝试

* 乐观锁

  一般通过版本号进行更新操作

  ```sql
  update user set name = '李籽兴' where id=1 and version = 1;
  ```

* 悲观锁

  

