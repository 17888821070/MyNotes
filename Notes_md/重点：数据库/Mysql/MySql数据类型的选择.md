# 数据类型的选择

## 1. char和varchar

> 保存少量字符串

* char固定长度、varchar可变长度
* char处理速度快，但浪费存储空间
*  MyISAM选用char、Innodb选用varchar

![1598771484092](imgs\1598771484092.png)

上图显示了将各种字符串值保存到CHAR(4)和VARCHAR(4)列后的结果，最后一行的值只适用MySQL运行在非“严格模式”时，如果MySQL运行在严格模式，超过列长度的值将不会保存，并且会出现错误提示

## 2. Text和Blob

> 保存大文本
>
> Text保存字符数据
>
> Blob可以保存照片等信息

* 要避免无用的检索，不需要Text和Blob数据时要避免检索，*啥的少用
* 可以吧Text和Blob数据放在一个单独的表中

## 3. 浮点数和顶点数

浮点数：float、double    普通带小数点的数据

顶点数：decimal、numberic	货币等需要完全精确的数据

## 4. 日期类型的选择

Year、Date、Time、DateTime、Timestamp

* 只需要年，用Year
* 需要存时间很久远的数据，用DateTime
* 需要给不同时区的用户使用，用TimeStamp