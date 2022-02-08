## 五种基本类型

**命令不区分大小写，变量区分大小写**

1. string
2. list
3. hash
4. set
5. sorted set

### string

```shell
redis-cli
127.0.0.1:6379> set dogg dog
OK
127.0.0.1:6379> keys *
1) "doggie"
2) "mice"
3) "dogg"
127.0.0.1:6379> get dogg
"dog"
127.0.0.1:6379> STRLEN dogg
(integer) 3
```



### hash

```shell
127.0.0.1:6379> hset dogg name dogg
(integer) 1
127.0.0.1:6379> hset dogg age 12 type dog
(integer) 2
127.0.0.1:6379> hmset dogg age 20 type dog
OK
127.0.0.1:6379> hget dogg age
"20"
127.0.0.1:6379> HGETALL dogg
1) "name"
2) "dogg"
3) "age"
4) "20"
5) "type"
6) "dog"
127.0.0.1:6379> HKEYS dogg
1) "name"
2) "age"
3) "type"
127.0.0.1:6379> hvals dogg
1) "dogg"
2) "20"
3) "dog"
```



### list

```shell
127.0.0.1:6379> lpush list s1
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "s1"
127.0.0.1:6379> lpush list s2
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "s2"
2) "s1"
127.0.0.1:6379> rpop list
"s1"
127.0.0.1:6379> lrange list 0 -1
1) "s2"
127.0.0.1:6379> lpush list s3 s4 s5
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "s5"
2) "s4"
3) "s3"
4) "s2"
```



### set

```shell
127.0.0.1:6379> sadd s 1
(integer) 1
127.0.0.1:6379> sadd s 3
(integer) 1
127.0.0.1:6379> sadd s 2
(integer) 1
127.0.0.1:6379> SMEMBERS s
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> sadd s 1
(integer) 0
127.0.0.1:6379> SMEMBERS s
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> SCARD s
(integer) 3
127.0.0.1:6379> SISMEMBER s 2
(integer) 1
127.0.0.1:6379> SISMEMBER s 5
(integer) 0
```



### sorted set

```shell
127.0.0.1:6379> zadd s 1 1
(integer) 1
127.0.0.1:6379> zadd s 2 2
(integer) 1
127.0.0.1:6379> zadd s 3 3
(integer) 1
127.0.0.1:6379> ZCARD s
(integer) 3
127.0.0.1:6379> ZRANGE s 0 -1
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> ZRANK s 1
(integer) 0
127.0.0.1:6379> ZREVRANK s 1
(integer) 2
127.0.0.1:6379> zscore s 2
"2"
```



## bitmap

### 什么是 bitmap

问题引出：有10亿个不重复的无序的数字，如果快速排序？

以 0010 1010 举例，以第几位出现 1，代表数字几。也就是说，只要我们构造出 2 ^ 32 个 bit 位，就可以不用存储 10 亿的数字（10 * 32 = 320 亿bit位）。

```shell
127.0.0.1:6379> setbit 20220105 111 1
(integer) 0
127.0.0.1:6379> strlen 20220105
(integer) 14
127.0.0.1:6379> setbit 20220105 2222 1
(integer) 0
127.0.0.1:6379> setbit 20220106 2222 1
(integer) 0
127.0.0.1:6379> setbit 20220106 112 1
(integer) 0
127.0.0.1:6379> bitop and loginEveryDay 20220105 20220106
(integer) 278
127.0.0.1:6379> bitop or loginAnyDay 20220105 20220106
(integer) 278
127.0.0.1:6379> bitcount loginEveryDay
(integer) 1
127.0.0.1:6379> bitcount loginAnyDay
(integer) 3
```



### bloomfilter

问题引出：如何判断一个元素是否存在与集合中。传统上，使用 hashmap。但是当集合中的元素增多，所需存储空间会越来越多。

布隆过滤器。应用 bitmap 的思想，使用数个 hash 函数（记作 k ），形成 k 个散列位点，将 k 个位点置为1. 检索时，检查这 k 个位点是否都是 1 就行。如此，不需要存储原始数据。缺点是，**存在误差**且不能删除元素。




## HyperLogLog

大规模数据的**去重**统计，**估计值**       [参考](https://www.jianshu.com/p/55defda6dcd2)

```shell
127.0.0.1:6379> PFADD user z3
(integer) 1
127.0.0.1:6379> PFADD user l4
(integer) 1
127.0.0.1:6379> PFADD user w5
(integer) 1
127.0.0.1:6379> PFCOUNT user
(integer) 3
127.0.0.1:6379> PFADD user q6
(integer) 1
127.0.0.1:6379> PFADD user w5
(integer) 0
127.0.0.1:6379> PFCOUNT user		# 去重统计
(integer) 4
127.0.0.1:6379> PFADD user2 z3
(integer) 1
127.0.0.1:6379> PFADD user2 l7
(integer) 1
127.0.0.1:6379> PFMERGE user user2	# PFMERGE 使用
OK
127.0.0.1:6379> PFCOUNT user2
(integer) 2
127.0.0.1:6379> PFCOUNT user
(integer) 5
```



## GEO

```shell
127.0.0.1:6379> geoadd china 116.23128 40.22077 beijing
(integer) 1
127.0.0.1:6379> geoadd china 121.48941 31.40527 shanghai
(integer) 1
127.0.0.1:6379> geoadd china 114.02919 30.58203 wuhan
(integer) 1
127.0.0.1:6379> geodist china wuhan beijing
"1090402.8389"
127.0.0.1:6379> geodist china wuhan beijing km
"1090.4028"
127.0.0.1:6379> georadius china 114 30 1000 km
1) "wuhan"
2) "shanghai"
127.0.0.1:6379> geopos china beijing
1) 1) "116.23128265142440796"
   2) "40.22076905438526495"
127.0.0.1:6379> georadiusbymember china beijing 2000 km withdist
1) 1) "wuhan"
   2) "1090.4028"
2) 1) "shanghai"
   2) "1088.6444"
3) 1) "beijing"
   2) "0.0000"
```



## pub / sub

```shell
➜  ~ redis-cli
127.0.0.1:6379> publish mq string1		# 没有订阅时，发布的消息也不会阻塞
(integer) 0
127.0.0.1:6379> publish mq string2		# 锚点1，开始有订阅
(integer) 1
127.0.0.1:6379> publish MQ string3		# 变量区分大小写
(integer) 0
127.0.0.1:6379> publish mq string5
(integer) 1
127.0.0.1:6379> pubsub channels
1) "mq"
127.0.0.1:6379> pubsub numsub mq
1) "mq"
2) (integer) 1

➜  ~ redis-cli		# another client start on 锚点1
127.0.0.1:6379> subscribe mq
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "mq"
3) (integer) 1
1) "message"
2) "mq"
3) "string2"
1) "message"
2) "mq"
3) "string5"

```



## 事务

redis 事务，只能保证命令的串行执行，可以理解为一个打包的批量执行脚本。非原子操作，中间出现错误**不会中断，不会回滚**。

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379(TX)> set s1 string1
QUEUED
127.0.0.1:6379(TX)> type s1
QUEUED
127.0.0.1:6379(TX)> sadd s1 1
QUEUED
127.0.0.1:6379(TX)> del s1
QUEUED
127.0.0.1:6379(TX)> sadd s1 1
QUEUED
127.0.0.1:6379(TX)> sadd s1 2 3 4
QUEUED
127.0.0.1:6379(TX)> smembers s1
QUEUED
127.0.0.1:6379(TX)> exec
1) OK
2) string
3) (error) WRONGTYPE Operation against a key holding the wrong kind of value
4) (integer) 1
5) (integer) 1
6) (integer) 3
7) 1) "1"
   2) "2"
   3) "3"
   4) "4"
```



## 切换数据库

```shell
➜  ~ redis-cli
127.0.0.1:6379> keys *
 1) "ktty"
 2) "mice"
 3) "loginEveryDay"
 4) "s1"
 5) "list"
 6) "s"
 7) "s3"
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
1) "kitty"
```