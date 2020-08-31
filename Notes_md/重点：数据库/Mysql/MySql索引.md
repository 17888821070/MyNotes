[目录](目录.md)

# 索引

## 1.  索引的创建和删除

* 创建索引

  ```mysql
  -- 直接创建索引
  create [索引类型] 索引名 on 表名(字段名(长度))
  create unique index MyIndex on MyTable(cardNumber(10)) -- 例子
  
  -- 修改表 
  alter table 表名 add [索引类型] index 索引名(字段名(长度))
  alter table MyTable add unique index MyIndex(cardNumber(10))-- 例子
  
  -- 创建表时，加入索引
  create table 表名(
  	 [索引类型] index (字段1,字段2)}
  )
  create table 表名(){			--例子
  	 unique index (name,cardNumber)
  }
  
  
  -- 索引类型
  -- 1. 普通索引 index
  -- 2. 唯一索引 unique index
  -- 3. 全文索引 fulltext index
  -- 4. 组合索引 index 索引名(字段1，字段2)
  ```

* 删除索引

  ```mysql
  -- 直接删除 
  drop [索引类型] index 索引名 on 表名
  
  -- 修改表删除
  alter table 表名 drop [索引类型] index 索引名
  ```

## 2. 索引数据结构

### 2.1 B+树

* 多叉树
* 让数据维持在3-5层
* 每次IO一个page大小，一个page16KB，当一个节点的key值为8Byte时，就可以分支出16*1024/8个分支
* 子节点中包含父节点
* 叶子节点存索引和对应的数据
* 叶子节点形成了链表，指向下一个叶子节点
* 稳定，怎么都是IO树的高度次

### 2.2 Page

* page是InnoDB磁盘管理的最小单位，与数据库相关的所有内容都存储在这种page结构中

## 3. 索引类型（逻辑分类）

* 主键索引
* 常规非主键索引
* 唯一索引
* 符合索引
* 前缀索引
*  全文索引
* 外键索引
* 覆盖索引

## 4. 索引类型（叶子节点数据分类）

* 聚集索引

  1、叶子结点数据存储的是行数据
  2、索引和数据存储在一起
  3、建议使用int型自增的字段作为主键

  ​	-> int型是为了保证维护B+树时比较的方便性

  ​	-> 自增是为了防止在前面插入数据时需要调整B+树结构

* 非聚集索引

  1、索引文件和数据文件不是同一个文件，分别对应.MYI和.MYD文件
  2、索引B+树的叶子结点的数据存放的是真正数据在磁盘的的是指针或者主键值



