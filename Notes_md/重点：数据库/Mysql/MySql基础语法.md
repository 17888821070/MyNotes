[目录](目录.md)

# Mysql基础语法

## 1. 数据库语法

* 增

```mysql
create database MyDataBase;
```

* 删

```mysql
drop database MyDataBase
```

```mysql
mysqladmin -u root -p drop MyDataBase
```

* 选

```mysql
use MyDataBase
```

## 2. 数据类型
### 2.1 数值类型

* TINYINT 
  * 1byte
* smallInt
  * 2bytes
* mediumint
  * 3bytes
* Int
  * 4Bytes
* bigInt
  * 8Bytes
* float
  * 4bytes
* double
  * 8buyes
* DECIMAL
  * 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2

### 2.2 字符串类型

* char
* blob
* text

| **类型**   | **大小**              | **用途**                        |
| :--------- | :-------------------- | :------------------------------ |
| CHAR       | 0-255 bytes           | 定长字符串                      |
| VARCHAR    | 0-65535 bytes         | 变长字符串                      |
| TINYBLOB   | 0-255 bytes           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 bytes           | 短文本字符串                    |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 bytes        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                    |

### 2.3 时间类型

- date
  - 3 bytes, YYYY-MM-DD，日期
- time
  - 3 bytes HH:mm:ss，持续时间
- year
  - 1 byte YYYY，年份
- datatime
  - 8 bytes YYYY-MM-DD HH:mm:ss，混合日期和时间
- timestamp
  - 4 bytes 时间戳

## 3. 表语法

* 增

```mysql
create table MyTable(
	字段名 类型 字段属性
)ENGINE=引擎名 default CHARSET=字符类型

-- 字段属性：
-- 1. 主键：Primary Key
-- 2. 唯一：Unique
-- 3. 非空：Not null
-- 4. 自增：Auto_increment
-- 4. 默认值：default XXX

//例子
create table MyTable(
	id int primary key,
    idcard varchar(25) unique,
    card int default 0
)ENGINE=Innodb default charset=utf8
```

* 删

```mysql
drop table MyTable
```

* 改

```mysql
alter table MyTable + 
-- 1. 添加字段：add 字段 字段类型
-- 2. 删除字段：drop 字段 字段类型
-- 3. 修改字段：change 字段 新字段 新数据类型/modify 字段 新数据类型
-- 4. 修改字符集：character set 字符集名
-- 5. 添加索引：add index 索引名
-- 6. 修改表名：rename to 新表名
```

* 查

```mysql
show tables
desc 表名
```

## 4. 数据操作

### 4.1 增

```mysql
insert into MyTable (字段1，字段2，字段3...) values (值1，值2，值3)
```

### 4.2 删

```mysql
delete from MyTable ...
```

### 4.3 改

```mysql
update MyTable set 字段1=值1, 字段2=值2...
```

### 4.4 查

```mysql
select 
	[需要查的内容] 
from 
	MyTable 
where 
	[条件] 
group by 
	[分组字段]
having 
	[条件]
order by
	[排序字段][ASC/DESC]
limit
	[起始][数目]
```
#### 4.4.1 需要查的内容：

> * 直接写字段 
>
>   * `select 字段,字段，字段 from 表`
>
> * 去重`select distinct(字段) from 表`
>
>   * 注：`select distinct(字段1)，字段2 from 表`，如果字段1相同的行，而字段2不想要的行，不会去重
>  
>       select distinct(name), id from mytable
>  
>       得到
>  
>   李 1
>    
>   李 2
>    
>   李 3
>
> * 计算 
>
>   * `select code1+code2 from 表`
>
> * 起别名
>
>   * `select code1+ code2 as totalcode from 表`
>
> * 聚合函数，只有在group by使用时才能使用
>
>   * 用于group by时的非group by 字段
>
>     ```mysql
>     select sum(id), name from MyTable group by name
>     -- name是排序字段，可以直接作为返回字段，id不是排序字段，不能直接返回，需要进行聚合
>     ```
>
>   * 聚合函数：
>     1. sum 求和
>     2. count 求个数
>     3. max 求最大
>     4. min 求最小
>     5. avg 求平均
>
>   

#### 4.4.2 where条件

> - < > = <= >= <>
>   Between 值 and 值
>   In (值, 值, 值, 值)
>   IS NULL
>   AND && OR || NOT 
> - Like :  语法：`where 字段 like ‘%a_b%’`
>   - '%'表示任意个数的字符
>   -  '_'表示一个字符
> - 正则表达式REGEXP：`语法：where 字段 REGEXP 条件`
>   - 以abc开头：`where name regexp '^abc'`
>   - 以xyz结尾：`where name regexp 'xyz$'`
>   - 包含666：`where name regexp '666'`
>   - 连续3-5个元音字符开头，数字结尾：`where name regexp '^[aeiou]{3,5}|[0-9]&'`

#### 4.4.3 分组

> - 以一个或多个字段进行分组
>
> - `group by 字段1，字段2`
>
>   所有字段相等的数据行算是同一组的数据
>
> * 查询的字段可以是分组字段中的任意

#### 4.4.4 过滤

> * 常与Group by联合使用，对分组结果进行筛选
> * 可以和聚合函数联合使用
> * `select avg(age) as avgage, name from MyTable order by name having avgage>10`

#### 4.4.5 排序

> * 以一个或多个字段排序
> * `order by 字段1,字段2 ASC/DESC`

#### 4.4.6 分页

> * 根据数据值进行分页
>
> * `limit a,b` 从第a条开始的b条数据
> * `limit b` 从第1条开始的b条数据

#### 4.4.7 合并

* UNION 语句：用于将不同表中相同列中查询的数据展示出来；（不包括重复数据）
* UNION ALL 语句：用于将不同表中相同列中查询的数据展示出来；（包括重复数据）

#### 4.4.8 连接

* 内连接

  * 交叉连接

    ```mysql
    SELECT
    	a.a1,
    	b.b1
    FROM
    	a CROSS JOIN b 
    on a.a1=b.b1
    ```

    Cross join 得到na*nb条数据，on在结果中筛选

  * 相等连接

    ```mysql
    select
        a.a1,
        a.a2,
        b.b1,
        b.b2
    from 
        a inner join b 
        on a.id=b.id
    ```
    只要a.id=b.id的值

  * 不等连接

    ```mysql
    select
        a.a1,
        a.a2,
        b.b1,
        b.b2
    from 
        a inner join b 
        on a.id<>b.id
    ```
    只要a.id<>b.id的值、

  * 自然连接

    ```mysql
    select
        a.a1,
        b.a1
    from 
        a NATURAL join b
    ORDER BY a.a1
    ```
    查找所有相同字段进行对比，只有所有同名字段都匹配才合格

* 外连接

  * 左外连接
  
    ```mysql
    select
        a.a1,
        a.a2,
        b.b1,
        b.b2
    from 
        a left join b 
        on a.id=b.id
    ```
    保存a表的所有数据，将b表按照a.id=b.id 的规则插入，如果a一条对那个b多条，则将a复制多份，并补充属于b的字段
  
  * 右外连接
  
    ```mysql
    select
        a.a1,
        a.a2,
        b.b1,
        b.b2
    from 
        a right join b 
        on a.id=b.id
    ```
    保存b表的所有数据，将a表按照a.id=b.id 的规则插入，如果b一条对那个a多条，则将b复制多份，并补充属于a的字段
  
  * 外连接
  
    ```mysql
    select
        a.a1,
        a.a2,
        b.b1,
        b.b2
    from 
        a full join b 
        on a.id=b.id
    ```
  
    保存ab表所有的数据库
































