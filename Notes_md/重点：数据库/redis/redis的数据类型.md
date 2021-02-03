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

> set key value
>
> get key

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

有序无重复

元素带分数的set，根据分数排序

> zadd kley score1 v1 score2 v2 score3 v3 插入v1 v2 v3分别具有的分数是s1 s2 s3

### ??

二进制安全？