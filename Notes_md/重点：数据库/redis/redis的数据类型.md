# Redis的存储数据类型

## 键

String

> keys * 查看当前库有哪些key
>
> exists key 判断key是否存在
>
> move key db 将key移动到其他库
>
> expire key 时间 设置key的过期时间
>
> ttl key 查看key的剩余过期时间，-1表示永不过期，-2表示已过期
>
> type key 查看key的类型

## 值

* 5种

### String 

* 操作类型

> 1、字符串操作
>
> 2、数值操作
>
> 3、二进制操作
>
> 注：无论是认为是字符串还是数值，都是按照二进制字节数组（123是【1】【2】【3】三个字节）
>
> ​		存入的数据默认是String类型，若对数据进行过inc等操作，会将类型标记为int数值类型

* 常用操作

> set key value  								--设置一个键值对
>
> get key											--获取键对应的值	
>
> append key value				 		--在key的值上追加一个value
>
> getrange key begin end			  --获取begin到end索引的value区间，可以是负数，-1表示最后一个
>
> strlen key									  --value的占用字节数，中文占用三个字节（不同编码不一样）
>
> setbit key offset 0/1					--设置一个8位的字节，将从左向右数，offset位置设置位0/1，offset大于7														  时继续向右补充字节
>
> getbit key									  --获取key的二进制编码的ascii码对应的字符
>
> getbit key offset						   --获取key的二进制编码，从左向右数，offset位的值 0/1
>
> bitcount key begin end			   --获取begin到end中1的个数
>
> bittop and keyname key1 key2 --key1和key2的value按位与，值保存到keyname
>
> bittop or keyname key1 key2    --key1和key2的value按位或，值保存到keyname

* 使用场景

> 字符串：session，小文件（静态网页，小图片），对象（序列化的对象），取代原磁盘io操作
>
>  数值：秒杀（限流，削峰，计数）
>
> 二进制：
>
> ​	1、统计：任意时间窗口内，用户登录天数->哪天登录就setbit lzx 100 1 表示lzx在第100天登陆了
>
> ​    2、活跃用户：日期作为key，用户id作为偏移->setbit 1001 111 1，setbit 1001 112 1，setbit 1002 112 1，bittop and activeUser 1001 1002 

### List

两端插入

> lpush key v1 v2 v3 左依次插入 得到 v3 v2 v1
>
> rpush key v1 v2 v3 右依次插入 得到 v1 v2 v3
>
> lrange key start len 取出value，len=-1表示全取
>
> lpop key 取最左
>
> rpop key 取最右
>
> lindex key x 从左数的x位置的数据，0开始

### Set

无序无重复

> sadd key v1 v1 v2 v3 v4 设置元素
>
> smembers key 获取元素
>
> srem key v1 删除元素v1
>
> spop key 随机出栈

### Hash

键值映射

> hset key kkey kValue 设置key的值为<kkey,kvalue>
>
> hget key kkey 获取key的value中的kkey的值

### Sorted Set

有序复

元素带分数的set，根据分数排序

> zadd kley score1 v1 score2 v2 score3 v3 插入v1 v2 v3分别具有的分数是s1 s2 s3

### ??

二进制安全？