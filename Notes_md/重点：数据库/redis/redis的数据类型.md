# Redis的存储数据类型

## 键

String

> keys * 									--查看当前库有哪些key
>
> exists key 							  --判断key是否存在
>
> move key db 						--将key移动到其他库
>
> expire key 时间 					--设置key的过期时间
>
> ttl key 									--查看key的剩余过期时间，-1表示永不过期，-2表示已过期
>
> type key 								--查看key的类型
>
> object encoding key			--查看key在底层的数据类型

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

> * set key value  								--设置一个键值对
>
> * get key											--获取键对应的值	
>
> * append key value				 		--在key的值上追加一个value
>
> * getrange key begin end			  --获取begin到end索引的value区间，可以是负数，-1表示最后一个
>
> * strlen key									  --value的占用字节数，中文占用三个字节（不同编码不一样）
>
> * setbit key offset 0/1					--设置一个8位的字节，将从左向右数，offset位置设置位0/1，offset大于7														  时继续向右补充字节
>
> * getbit key									  --获取key的二进制编码的ascii码对应的字符
>
> * getbit key offset						   --获取key的二进制编码，从左向右数，offset位的值 0/1
>
> * bitcount key begin end			   --获取begin到end中1的个数
>
> * bittop and keyname key1 key2 --key1和key2的value按位与，值保存到keyname
>
> * bittop or keyname key1 key2    --key1和key2的value按位或，值保存到keyname

* 使用场景

> **字符串：**共享session，小文件（静态网页，小图片），对象（序列化的对象），取代原磁盘io操作
>
> **数值：**秒杀（限流，削峰，计数）
>
> **二进制：**
>
> ​	1、统计：任意时间窗口内，用户登录天数->哪天登录就setbit lzx 100 1 表示lzx在第100天登陆了
>
> ​    2、活跃用户：日期作为key，用户id作为偏移->setbit 1001 111 1，setbit 1001 112 1，setbit 1002 112 1，bittop and activeUser 1001 1002 
>
> ​	3、用户权限：100个权限，用户作为key，权限类型作为便宜->setbit lzx 2 1 lzx有的权限

### List

> 双向链表
>
> 模拟：
>
> ​	栈：同向，lpush+lpop 或 rpush+rpop
>
> ​	队列：异向，lpush+rpop 或 rpush+rpop
>
> ​	输入：lindex	

* 常用操作

> lpush key v1 v2 v3 						--左依次插入 得到 v3 v2 v1
>
> rpush key v1 v2 v3 						--右依次插入 得到 v1 v2 v3
>
> lrange key start end					  --取出value，len=-1表示全取
>
> lpop key 										--取最左
>
> rpop key 										--取最右
>
> lindex key x 									--从左数的x位置的数据，0开始
>
> ltrim key start end 						--删除start和end范围外的数据

* 使用场景

>数据共享，迁出

### Set

无序无重复

> sadd key v1 v1 v2 v3 v4 				--设置元素
>
> smembers key 							   --获取元素
>
> srem key v1 									--删除元素v1
>
> spop key 										--随机出栈
>
> srandmember key [3/-3/8/-8]     --正数取出去重集合，负数非去重集合，可能取不到足够的数据
>
> sunion key1 key2						  --取并集
>
> sinter   key1 key2 						--取交集
>
> sdiff     key1 key2						  --取差集，前一个集合相比后一个集合多的元素
>
> ​														-- key1:a b c d ; key2:c d e f 结果a b

* 使用场景

> 抽奖：
>
> ​	一堆里抽3个，每个人不可重复获奖 srandmember nums 3
>
> ​	一堆里抽3个，每个人可重复获奖     srandmember nums -3
>
> ​	一堆里抽3个，一次抽一个，抽到的人不参与后续抽奖 spop nums spop nums spop nums
>
> 推荐：
>
> ​	共同好友：交集
>
> ​	推荐好友：差集
>
> ​	猜你喜欢：并集

### Hash

键值映射

> hset key kkey kValue 					--设置key的值为<kkey,kvalue>
>
> hget key kkey 								--获取key的value中的kkey的值
>
> hgetAll key 									--key下的所有键，所有值
>
> hkeys key 									  --key下的键值对的所有key
>
> hvals key 									   --key下的键值对的所有值

* 应用场景

> 聚合场景：商品详情页，用户个人信息页

### Sorted Set

有序集合

>  需要有三个维度：元素自身，分值，排名
>
> 当存储数据小于64字节时，使用ziplist方式存储
>
> 当存储数据大于64字节时，使用skiplist方式存储

> zadd kley s1 v1 s2 v2 s3 v3 			--插入v1 v2 v3分别具有的分数是s1 s2 s3
>
> zrange key begin end withscores  --使用score排序，返回begin到end的数据
>
> zrank key val									-- 返回val的排名
>
> zrevrange key begin end				--反向排序取出begin到end的数据

* 使用场景

> 排行榜
>
> 有序事件
>
> 评论+分页

* **skiplist的底层原理**

> 普通链表的的有序插入，从一头开始遍历，找到大于自己的元素，入链，时间复杂度O(n)
>
> 跳跃表，增加了层的概念，最底层的链表包含所有的链表元素，越高层越倾向于稀疏，跳跃越大。当需要插入操作时，从高层开始遍历，找到比自己大或者null的节点，切换到下一层，直到找到最底层正确的插入位置。插入时操作：1、入链；2、建层
>
> ![1602127095835](imgs\1602126944947.png)
>
> 建层：随机造层，当小于最大层数ZSKIOLIST_MAXLEVEL时返回新建层，否则返回最大层，		
>
> ​			ZSKIOLIST_MAXLEVEL默认为64

## 二进制安全？

> 存储到redis的数据都是以字节数组的方式进行存储的，只要保证编码格式相同，就不会出现类型出错，溢出等异常
>
> 举例：数值类型 123 —> 存的是三个字节 1 2 3
>
> ​			字符串类型123 —>存的是三个字节 1 2 3
>
> ​			...