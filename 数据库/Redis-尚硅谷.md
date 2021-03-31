Redis--尚硅谷：https://www.bilibili.com/video/BV1oW411u75R?p=14&spm_id_from=pageDriver

笔记来自：https://blog.csdn.net/u011863024/article/details/107476187

### NoSQL数据库的四大分类

- ##### KV

  - 新浪：BerkeleyDB + Redis
  - 美团：Redis + tair
  - 阿里、百度：memcache + Redis

- ##### 文档型数据库（bson格式比较多）

  - CouchDB
  - MongoDB
    - MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。
    - MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

- ##### 列存储数据库

  - Cassandra、HBase
  - 分布式文件系统

- ##### 图关系数据库

  - 它不是放图形的、放的是关系比如：朋友圈社交网络、广告推荐系统
  - 社交网络、推荐系统。专注于构建关系图谱
  - Neo4j、InfoGrid

### 分布式数据库CAP原理

#### 传统的ACID分别是什么

A (Atomicity) 原子性
C (Consistency) 一致性
I (Isolation) 独立性
D (Durability) 持久性
关系型数据库遵循ACID规则，事务在英文中是transaction，和现实世界中的交易很类似，它有如下四个特性：

**1、A (Atomicity) 原子性** 原子性很容易理解，也就是说事务里的所有操作要么全部做完，要么都不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要回滚。比如银行转账，从A账户转100元至B账户，分为两个步骤：1）从A账户取100元；2）存入100元至B账户。这两步要么一起完成，要么一起不完成，如果只完成第一步，第二步失败，钱会莫名其妙少了100元。

**2、C (Consistency) 一致性** 一致性也比较容易理解，也就是说数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束。

**3、I (Isolation) 独立性** 所谓的独立性是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。比如现有有个交易是从A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元的

**4、D (Durability) 持久性** 持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使出现宕机也不会丢失。

#### CAP

C:Consistency（强一致性）：
A:Availability（高可用性）
P:Partition tolerance（分区容错性）一个分布式系统里面，节点组成的网络本来应该是连通的。然而可能因为一些故障，使得有些节点之间不连通了，整个网络就分成了几块区域。数据就散布在了这些不连通的区域中。这就叫分区。

当你一个数据项只在一个节点中保存，那么分区出现后，和这个节点不连通的部分就访问不到这个数据了。这时分区就是无法容忍的。CAP理论就是说在分布式存储系统中，最多只能实现上面的两点。

而由于当前的网络硬件肯定会出现延迟丢包等问题，所以分区容忍性是我们必须需要实现的。所以我们只能在一致性和可用性之间进行权衡，没有NoSQL系统能同时保证这三点。

CA 传统Oracle数据库,单点集群，满足一致性和可用性，可扩展性不强；
AP 大多数网站架构的选择，满足可哟哦关系和分区容错性，对一致性要求低一些；
CP Redis、Mongodb；满足一致性和分区容错性，通常性能不是很高。
注意：分布式架构的时候必须做出取舍。

一致性和可用性之间取一个平衡。多余大多数web应用，其实并不需要强一致性。因此牺牲C换取P，这是目前分布式数据库产品的方向。

#### BASE

BASE就是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。

BASE其实是下面三个术语的缩写：

基本可用（Basically Available）
软状态（Soft state）
最终一致性（Eventually consistent）
它的思想是通过**让系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上改观。**为什么这么说呢，缘由就在于大型系统往往由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，要想获得这些指标，我们必须采用另外一种方式来完成，这里BASE就是解决这个问题的办法。

### Redis入门概述

#### Redis是什么

**Redis:REmote DIctionary Server(远程字典服务器)**是完全开源免费的，用C语言编写的，遵守BSD协议，是一个**高性能的(key/value)分布式内存数据库，基于内存运行 并支持持久化的NoSQL数据库**，是当前最热门的NoSql数据库之一，也被人们称为数据结构服务器。

Redis 与其他 key - value 缓存产品有以下三个特点：

- Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储
- Redis支持数据的备份，即master-slave模式的数据备份

#### Redis能干嘛

内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务
取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面
模拟类似于HttpSession这种需要设定过期时间的功能
发布、订阅消息系统
定时器、计数器

#### 去哪下

Redis官网
Redis中文网

安装Redis:

1下载redis指定版本的源码压缩包到当前目录：

wget http://download.redis.io/releases/redis-3.0.7.tar.gz

2解压缩redis源码压缩包：

tar xzf redis-3.0.7.tar.gz

3建立一个redis目录的软连接，指向redis-3.0.7（创建目录的软链接，是为了不把redis目录固定在指定的版本上，有利于未来版本升级，安装软件的好习惯。）

ln -s redis-3.0.7 redis

4进入redis目录：

cd redis

5编译（编译之前确保系统已经安装gcc）：

make

6安装：

make install 

7查看版本：

redis-cli -v

启动redis:默认配置、允许配置、配置文件启动三种。

默认配置：



#### 怎么玩

## 数据类型、基本操作和配置

持久化和复制，RDB/AOF
事务的控制
复制（主从关系）
启动后杂项基础知识
C:\Program Files\Redis-x64-3.2.100>redis-benchmark.exe 测试redis在机器运行的效能

#### 单进程

**因为线程切换的开销远大于多线程对效率的提升**

单进程模型来处理客户端的请求。对读写等事件的响应 是通过对epoll函数的包装来做到的。Redis的实际处理速度完全依靠主进程的执行效率
Epoll是Linux内核为处理大批量文件描述符而作了改进的epoll，是Linux下多路复用IO接口select/poll的增强版本， 它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

默认16个数据库，类似数组下表从零开始，初始默认使用零号库，可在配置文件配置
select命令切换数据库
dbsize查看当前数据库的key的数量
flushdb：清空当前库
flushall；通杀全部库
统一密码管理，16个库都是同样密码，要么都OK要么一个也连接不上
Redis索引都是从零开始
为什么默认端口是6379

### 常用五大数据类型简介

#### String（字符串）

string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。
string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。
string类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M

##### 内部编码:

Integer

ziplist（压缩列表）

raw



#### Hash（哈希，类似java里的Map）

Redis hash 是一个键值对集合。
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。
类似Java里面的Map<String,Object>

#### List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。

##### 内部编码:

###### ziplist（压缩列表）：

列表元素个数小于list-max-ziplist-entries配置（默认512个） && 列表中每个元素的值都小于list-max-ziplist-value配置（默认64字节）。

###### linkedlist:

列表不满足ziplist的条件时。

###### quicklist：

当列表较大时获取中间范围的元素性能会变差，可以使用该编码，它结合了ziplist和linkedlist的特点，获取列表中间范围的元素时可以高效完成。

##### list使用场景：

消息队列

文章列表

##### 小技巧：

lpush + lpop = stack

lpush + rpop = queue

lpush + ltrim = capped collection （有限集合）

lpush + brpop = message queue(消息队列)



#### Set（集合）

Redis的Set是string类型的无序集合。它是通过HashTable实现实现的。

##### 内部编码：

###### intset 整数集合

集合元素个数小于sett-max-intset-entries配置（默认512个） && 列表中每个元素都是整数。减少内存使用。

###### hashtable 哈希表

当集合类型不满足intset的条件时。

##### set使用场景：

用户标签：

sadd user:1:tags 美食 旅游 电影

sadd user:2:tags 美食 科技  计算机

sadd tags 美食 user:1 user:5 user:90

##### 小技巧:

sadd = tagging(标签)

spop/srandmember = random item(生成随机数，比如抽奖)

sadd + sinter = social graph (社交需求) 

#### Zset(sorted set：有序集合)

Redis zset 和 set 一样也是string类型元素的集合，且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。
redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的，但分数(score)却可以重复。

##### 内部编码：

###### ziplist 压缩列表：

有序集合元素个数小于zset-max-ziplist-entries配置（默认128个） && 列表中每个元素的值都小于zset-max-ziplist-value配置（默认64字节）。

###### skiplist(跳跃表)：

当ziplist条件不满足时，有序集合会使用skiplist作为内部实现。

##### 有序集合使用场景：

排行榜系统。视频网站的排行榜根据多个维度进行排行。

记录某个用户的视频点赞数：zadd user:ranking:2021_03_30  288888 蹦蹦和蜜糖麻麻 

zadd user:ranking:2021_03_30  388888 小贝

之后再获得点赞，可以使用zincrby: zincryby zadd user:ranking:2021_03_30  1 蹦蹦和蜜糖麻麻 

#### 键管理

#### Key

**常用命令：**

| 描述     | 命令                                            |
| -------- | ----------------------------------------------- |
| 重命名键 | rename key newkey / renamenx(确保newkey不存在)/ |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |
|          |                                                 |

命令	描述
DEL key	该命令用于在 key 存在时删除 key。
DUMP key	序列化给定 key ，并返回被序列化的值。
EXISTS key	检查给定 key 是否存在。
EXPIRE key seconds	为给定 key 设置过期时间，以秒计。
EXPIREAT key timestamp	EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。
PEXPIRE key milliseconds	设置 key 的过期时间以毫秒计。
PEXPIREAT key milliseconds-timestamp	设置 key 过期时间的时间戳(unix timestamp) 以毫秒计
KEYS pattern	查找所有符合给定模式( pattern)的 key 。
MOVE key db	将当前数据库的 key 移动到给定的数据库 db 当中。
PERSIST key	移除 key 的过期时间，key 将持久保持。
PTTL key	以毫秒为单位返回 key 的剩余的过期时间。
TTL key	以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
RANDOMKEY	从当前数据库中随机返回一个 key 。
RENAME key newkey	修改 key 的名称
RENAMENX key newkey	仅当 newkey 不存在时，将 key 改名为 newkey 。
SCAN cursor [MATCH pattern] [COUNT count]	迭代数据库中的数据库键。
TYPE key	返回 key 所储存的值的类型。
**案例**：
keys *
exists key的名字，判断某个key是否存在
move key db —>当前库就没有了，被移除了
expire key 秒钟：为给定的key设置过期时间
ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期
type key 查看你的key是什么类型

### 常用命令



#### String:单值单value

常用命令：
命令	描述
SET key value	设置指定 key 的值
GET key	获取指定 key 的值。
GETRANGE key start end	返回 key 中字符串值的子字符
GETSET key value	将给定 key 的值设为 value ，并返回 key 的旧值(old value)。
GETBIT key offset	对 key 所储存的字符串值，获取指定偏移量上的位(bit)。
MGET key1 [key2…]	获取所有(一个或多个)给定 key 的值。
SETBIT key offset value	对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
SETEX key seconds value	将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。
SETNX key value	只有在 key 不存在时设置 key 的值。
SETRANGE key offset value	用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。
STRLEN key	返回 key 所储存的字符串值的长度。
MSET key value [key value …]	同时设置一个或多个 key-value 对。
MSETNX key value [key value …]	同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
PSETEX key milliseconds value	这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。
INCR key	将 key 中储存的数字值增一。
INCRBY key increment	将 key 所储存的值加上给定的增量值（increment） 。
INCRBYFLOAT key increment	将 key 所储存的值加上给定的浮点增量值（increment） 。
DECR key	将 key 中储存的数字值减一。
DECRBY key decrement	key 所储存的值减去给定的减量值（decrement） 。
APPEND key value	如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾。
**案例**：
set/get/del/append/strlen
Incr/decr/incrby/decrby,一定要是数字才能进行加减
getrange/setrange
setex(set with expire)键秒值/setnx(set if not exist)
mset/mget/msetnx
getset(先get再set)

#### List:单值多value

**常用命令：**
命令	描述
BLPOP key1 [key2 ] timeout	移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
BRPOP key1 [key2 ] timeout	移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
BRPOPLPUSH source destination timeout	从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
LINDEX key index	通过索引获取列表中的元素
LINSERT key BEFORE/AFTER pivot value	在列表的元素前或者后插入元素
LLEN key	获取列表长度
LPOP key	移出并获取列表的第一个元素
LPUSH key value1 [value2]	将一个或多个值插入到列表头部
LPUSHX key value	将一个值插入到已存在的列表头部
LRANGE key start stop	获取列表指定范围内的元素
LREM key count value	移除列表元素
LSET key index value	通过索引设置列表元素的值
LTRIM key start stop	对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
RPOP key	移除列表的最后一个元素，返回值为移除的元素。
RPOPLPUSH source destination	移除列表的最后一个元素，并将该元素添加到另一个列表并返回
RPUSH key value1 [value2]	在列表中添加一个或多个值
RPUSHX key value	为已存在的列表添加值
**案例：**
lpush/rpush/lrange  可以理解为lpush是从左向右推入元素，rpush是从由向左推入元素
lpop/rpop
lindex，按照索引下标获得元素(从上到下)
llen
lrem key 删N个value
ltrim key 开始index 结束index，截取指定范围的值后再赋值给key
rpoplpush 源列表 目的列表
lset key index value
linsert key before/after 值1 值2
性能总结：

它是一个字符串链表，left、right都可以插入添加；
如果键不存在，创建新的链表；
如果键已存在，新增内容；
如果值全移除，对应的键也就消失了。
链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。

#### Set：单值多value

**常用命令：**
命令	描述
SADD key member1 [member2]	向集合添加一个或多个成员
SCARD key	获取集合的成员数
SDIFF key1 [key2]	返回给定所有集合的差集
SDIFFSTORE destination key1 [key2]	返回给定所有集合的差集并存储在 destination 中
SINTER key1 [key2]	返回给定所有集合的交集
SINTERSTORE destination key1 [key2]	返回给定所有集合的交集并存储在 destination 中
SISMEMBER key member	判断 member 元素是否是集合 key 的成员
SMEMBERS key	返回集合中的所有成员
SMOVE source destination member	将 member 元素从 source 集合移动到 destination 集合
SPOP key	移除并返回集合中的一个随机元素
SRANDMEMBER key [count]	返回集合中一个或多个随机数
SREM key member1 [member2]	移除集合中一个或多个成员
SUNION key1 [key2]	返回所有给定集合的并集
SUNIONSTORE destination key1 [key2]	所有给定集合的并集存储在 destination 集合中
SSCAN key cursor [MATCH pattern] [COUNT count]	迭代集合中的元素
**案例：**

sadd set01 1 2 3

semembers set01: 1 2 3

sadd/smembers/sismember
scard，获取集合里面的元素个数
srem key value 删除集合中元素
srandmember key 某个整数(随机出几个数)
spop key 随机出栈
smove key1 key2 在key1里某个值 作用是将key1里的某个值赋给key2
数学集合类
差集：sdiff
交集：sinter
并集：sunion

#### Hash：KV模式不变，但V是一个键值对

**常用命令：**
命令	描述
HDEL key field1 [field2]	删除一个或多个哈希表字段
HEXISTS key field	查看哈希表 key 中，指定的字段是否存在。
HGET key field	获取存储在哈希表中指定字段的值。
HGETALL key	获取在哈希表中指定 key 的所有字段和值
HINCRBY key field increment	为哈希表 key 中的指定字段的整数值加上增量 increment 。
HINCRBYFLOAT key field increment	为哈希表 key 中的指定字段的浮点数值加上增量 increment 。
HKEYS key	获取所有哈希表中的字段
HLEN key	获取哈希表中字段的数量
HMGET key field1 [field2]	获取所有给定字段的值
HMSET key field1 value1 [field2 value2 ]	同时将多个 field-value (域-值)对设置到哈希表 key 中。
HSET key field value	将哈希表 key 中的字段 field 的值设为 value 。
HSETNX key field value	只有在字段 field 不存在时，设置哈希表字段的值。
HVALS key	获取哈希表中所有值。
HSCAN key cursor [MATCH pattern] [COUNT count]	迭代哈希表中的键值对。
**案例:**
hset/hget/hmset/hmget/hgetall/hdel

hset user id 11

hget user id : 11

hlen
hexists key 在key里面的某个值的key
hkeys/hvals
hincrby/hincrbyfloat
hsetnx

## 配置文件redis.conf

Redis 的配置文件位于 Redis 安装目录下，文件名为 redis.conf(Windows 名为 redis.windows.conf)。

你可以通过 CONFIG 命令查看或设置配置项。

语法

Redis CONFIG 命令格式如下：

redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME
实例

redis 127.0.0.1:6379> CONFIG GET loglevel

1) "loglevel"
2) "notice"

参数说明
redis.conf 配置项说明如下：(注意，下面配置项说明并不全，了解更多请检阅redis.conf。)

| 序号 | 配置项                                                   | 说明                                                         |
| ---- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 1    | daemenize no                                             | Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ） |
| 2    | pidfile /var/run/redis.pid                               | 当 Redis 以守护进程方式运行时，Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定 |
| 3    | port 6379                                                | 指定 Redis 监听端口，默认端口为 6379，作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女 Alessia Merz 的名字 |
| 4    | bind 127.0.0.1                                           | 绑定的主机地址                                               |
| 5    | timeout 300                                              | 当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能  |
| 6    | loglevel notice                                          | 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice |
| 7    | logfile stdout                                           | 日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null |
| 8    | databases 16                                             | 设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id |
| 9    | save <secondes><changes>                                 | Redis 默认配置文件中提供了三个条件：<br/>save 900 1<br/>save 300 10<br/>save 60 10000<br/>分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。<br/>指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合 |
| 10   | rdbcompression yes                                       | 指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大 |
| 11   | rdbfilename dump.rdb                                     | 指定本地数据库文件名，默认值为 dump.rdb                      |
| 12   | dir ./                                                   | 指定本地数据库存放目录                                       |
| 13   | slvaeof <masterip><masterport>                           | 设置当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步 |
| 14   | masterauth <master-password>                             | 当 master 服务设置了密码保护时，slav 服务连接 master 的密码  |
| 15   | requirepass foobared                                     | 设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH 命令提供密码，默认关闭 |
| 16   | maxclients 128                                           | 设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息 |
| 17   | maxmemory <bytes>                                        | 指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区 |
| 18   | appendonly no                                            | 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no |
| 19   | appendfilename appendonly.aof                            | 指定更新日志文件名，默认为 appendonly.aof                    |
| 20   | appendfsync everysec                                     | 指定更新日志条件，共有 3 个可选值：<br/>no：表示等操作系统进行数据缓存同步到磁盘（快）<br/>always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）<br/>everysec：表示每秒同步一次（折中，默认值） |
| 21   | vm-enabled no                                            | 指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制） |
| 22   | vm-swap-file /tmp/redis.swap                             | 虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享 |
| 23   | vm-max-memory 0                                          | 将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0 |
| 24   | vm-page-size 32                                          | Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值 |
| 25   | vm-pages 134217728                                       | 设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。 |
| 26   | vm-max-threads 4                                         | 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4 |
| 27   | glueoutputbuf yes                                        | 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启 |
| 28   | hash-max-zipmap-entries 64<br/>hash-max-zipmap-value 512 | 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法 |
| 29   | activerehashing yes                                      | 指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍） |
| 30   | include /path/to/local.conf                              | 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件 |

## Redis持久化

Redis持久化之RDB（）

Redis持久化之AOF(Append only )

## Redis的事务

## Redis的发布订阅

## Redis的复制（Master/Slave）

## Redis的Java客户端Jedis