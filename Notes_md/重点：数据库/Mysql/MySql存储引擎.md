# 存储引擎

### 1.  Innodb

- 存储大小：64TB

- 事务：支持事务

- 索引：聚集索引/非聚集索引

  ```txt
  InnoDB默认创建聚簇索引
  1、有主键时，根据主键创建聚簇索引
  2、没有主键时，使用一个唯一且不为空的列作为主键，创建聚簇索引
  3、上两种都没有，InnoDB自己创建一个虚拟的聚簇索引（使用隐藏字段DB_ROW_ID）
  
  InnoDB可以手动创建辅助索引（非聚簇索引）
  1、此时的辅助索引的叶子结点存储的数据是主键值
  2、通过辅助索引查找时会查找两次，根据辅助索引找到主键值，主键值查找数据
  ```

- 存储文件：数据文件和索引文件是同一个文件

  - .frm （存储表定义）
  - .ibd（数据文件和索引）

- 锁：支持行级锁和表级锁，默认行级锁

- 外键：支持外键

### 2. MyISAM

- 存储大小：256TB

- 事务：不支持

- 索引：非聚集索引

  ```txt
  只有非聚簇索引，可以没有主键
  MyISAM的非聚簇索引的叶子结点存放的数据时实际数据的物理地址
  ```

* 存储文件：数据文件和索引文件不是同一个文件
  - .frm（存储表定义）；
  - .MYD（MYData，存储数据）；
  - .MYI（MYIndex，存储索引）。

- 锁：表级锁
- 外键：不支持

## 3. Memory

* 存储文件：
  * .frm文件 （存储表结构）
  * 数据存在于内存中，使用Hash索引，访问速度非常快，服务关了数据就丢失

## 4. Merge

* MERGE存储引擎是一组MyISAM表的组合，这些MyISAM表必须结构完全相同，MERGE表本身并没有数据，对MERGE类型的表可以进行查询、更新、删除操作，这些操作实际上是对内部的MyISAM表进行的。

## 5. 存储引擎的选择

* MyISAM：
  1. 读、插入为主，很少修改和删除
  2. 对数据一致性要求不高（没有事务）
  3. 并发性度不高
* Innodb
  1. 对操作的完整性要求高
  2. 并发情况下数据一致性要求高
  3. 更新、删除操作频率高

* Memory
  1. 数据量小，访问速度要求非常高，表数据可以从其他地方恢复
* Merge
  1. 跟MyISAM要求一样，但数据量非常大，突破了M'y'ISAM的限制