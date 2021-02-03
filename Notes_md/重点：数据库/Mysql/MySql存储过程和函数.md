# 存储过程、函数、触发器

调用存储过程和函数可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。

## 1. 存储过程和函数

### 1.1 存储过程和函数是什么

* 存储过程和函数是事先经过编译并存储在数据库中的一段 SQL 语句的集合

### 1.2 存储过程和函数的区别

* 返回值

  存储过程没有返回值、函数有返回值

* 参数

  存储过程的参数可以是IN、OUT、INOUT类型，函数的参数只能是IN类型

## 2. 触发器

触发器是指一段代码，当触发某个事件时，自动执行这些代码

MySQL 数据库中有六种触发器：

Before Insert
After Insert
Before Update
After Update
Before Delete
After Delete
使用场景：

可以通过数据库中的相关表实现级联更改
实时监控表中字段的更改做出相应处理
注意：滥用会造成数据库及应用程序的维护困难

例子：
create TRIGGER testTrigger1(随便起) After(触发时机) delete(触发事件) 
ON user(监听的表) for each ROW
BEGIN
 INSERT INTO student (`stu_name`)（触发的操作） VALUES ('2');
END