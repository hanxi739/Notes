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

#### 怎么玩

### 数据类型、基本操作和配置

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

#### Hash（哈希，类似java里的Map）

Redis hash 是一个键值对集合。
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。
类似Java里面的Map<String,Object>

#### List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。
它的底层实际是个链表

#### Set（集合）

Redis的Set是string类型的无序集合。它是通过HashTable实现实现的

#### Zset(sorted set：有序集合)

Redis zset 和 set 一样也是string类型元素的集合，且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。
redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的，但分数(score)却可以重复。
哪里去获得redis常见数据类型操作命令
Redis 命令参考
Redis 官网命令参考

#### Key

**常用命令：**

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

Redis的持久化

Redis的事务

Redis的发布订阅

Redis的复制（Master/Slave）

Redis的Java客户端Jedis