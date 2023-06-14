# 一、简介

- 关系形数据库（SQL）：磁盘读写性能差，数据关系负载，扩展性差，不便于大规格集群
- 非关系形数据（NOSQL）：降低读写次数，简化数据间关系

> 对关系型数据库做优化、补充，将数据放在RAM里处理，最终还是要落在持久层

- Redis是一个常用的NOSQL，它是一个键值对数据库（Map集合）
	- 数据之间没有必然关联
	- 单线程机制
	- 高性能
	- 多数据类型
		- String 字符串
		- list 列表
		- hash 散列
		- set 集合
		- sorted_set 有序集合
	- 支持持久化
- Redis的应用
	- 热点数据查询 / 任务队列 / 及时信息查询 / 时效性信息 / 分布式数据共享 / 消息队列 / 分布式锁


# 二、Redis安装与配置

- Liunx
	- 不同Liunx版本中安装方式不同（redis.io）

- Windows深度融全Ubuntu安装
	1. 如果没有windows终端，就去商店安装Windows Terminal
	2. 控制面板->程序->启用或关才Windows程序->适用于Liunx的Windows子系统
	3. 从应用商城安装Ubuntu系统（如果WSL2未更新加载Linux内核时失败就上网下载更新包）
	4. 替换软件源 `vi /etc/apt/sources.list` （没有魔法就替换软件源，软件源网上找）
	5. 应用新的软件源 `sudo apt-get update`
	6. 通过apt命令安装redis`sudo apt-get install redis-server`（最新参考redis官网）


# 三、快速上手

## 1. 设置命令

> 设置信息，设置键值对
> 语法：`set key value`
> 案例：`set teacher Jack`

## 2. 查询命令

> 根据键获取值，不存在返回空（nil）
> 语法：`get key`
> 案例：`get teacher`

## 3. 删除命令

> 根据键进行删除
> 语法：`del key`
> 案例：`del teacher`

## 4. 清屏命令

> 清除屏幕上所有历史信息，只保留一行提示符
> 语法：`clear` （ctrl + l）

## 5. 帮助命令

> 查看命令帮助
> 语法：
> 	`help 命令名` （`help set`）
> 	`help @组名`（`help @string`）

## 6. 退出命令

> 退出redis客户端
> 语法：
> 	`exit`
> 	`quit`

## 7. 设置多个

> 添加或修改多个数据
> 语法：`mset key01 val01 key02 val02 ...`
> 案例：`mset teacher Jack student Rose`

## 8. 获取多个

> 一次获取多个数据
> 语法：`mget key01 val01 key02 val02`


# 四、数据类型

> 根据有高并发和有可能成为高并发、高频访问和复杂的数据而设计的

## 1. string

### 1) 相关命令

> 字符串类型

命令 | 说明
:- | :-
`strlen key` | 求key中存储的字符串长度
`append key value` | 向key中追加字符串
`incr key` | 自增1（最大取值范围64个二进制位：2<sup>64-1</sup>-1）
`incrby key increment` | 自增increment整数
`incrbyfloat key increment` | 自增increment浮点数
`decr key` | 自减1
`decrby key increment` | 自减increment
`setex key seconds value` | 设置key的同时，指定生命周期，秒为单位
`psetex key seconds value` | 同上，毫秒为单位

> 当重新设置时，生命周期将会被清除

### 2) 格式规范

格式规范 | 范例
:- | :-
表名:主键名:主键值:字段名 | set stu:id:22:age 29 <br/> get stu:id:22:age
表名:主键名:主键值 | set stu:id:22 {id:22,name:Jack,age:29} <br/> get stu:id:22

## 2. hash

> 可以理解成是键值对的集合

### 1) 相关命令

命令 | 说明
:- | :-
`hset key field value` | 设置数据
`hget key field` | 获取键中对应字段的数据
`hgetall key` | 根据键获取数据
`hdel key field01 [field02 ...]` | 删除键中指定字段的数据
`hsetnx key field value` | 如果不存在则设置，如果存在则不动
`hmset key field01 value01 [field02 value02 ...]` | 设置多个
`hmget key field01 [field02...]` | 获取多个
`hlen key` | 获取字段长度
`hexists key field` | 获取字段是否存在
`hkeys key` | 获取指定key中所有的字段
`hvals key` | 获取指定key中所有的值
`hincrby key field incrment` | 主键自增指定的increment小数
`hincrbyfloat key field increment` | 主键自增指定的increment浮点数

### 2) 注意事项

- hash中的value只能存储字符串，不允许存储其他类型的数据，也不允许嵌套
- hash最多可以存储2<sup>23</sup>-1个键值对
- hash类型类似对的存储形式，并且可以娄活删除对象属性，但数据类型单一，并不适合存储完整的对象
- hgetall容易降低效率，慎用

## 3. list

> 有序集合，可用于存储多个数据，底层数据结构是双向链表

### 1) 相关命令

命令 | 说明
:- | :-
`lpush key value01 [value02 ...]` | 从左边添加数据
`rpush key value02 [value02 ...]` | 从右边添加数据
`lrange key start stop` | 从左边（头）获取数据，start表示从哪儿开始，stop表示到哪儿结束（-1表示最后）
`lindex key index` | 获取指定索引对应的数据
`llen key` | 获取列表长度
`lpop key` | 从左边移除数据
`rpop key` | 从右边移除数据
`blpop key01 [key02 ...] timeout` | 延迟移除
`brpop key01 [key02 ...] timeout` | 延迟移除
`lrem key count value` | 删除指定的数据（count是删除几个，要删除的值）

### 2) 注意事项

- list中保存的数String类型，数据最多2<sup>32</sup>-1个
- list中有索引，可以通过索引操作list，但是操作数据通常一队列的形式进行入队列与出队列操作，或者一栈的形式进行进出栈操作

## 4. set

> 与list类似，底层存储结构为哈希表

### 1) 相关命令

命令 | 说明
:- | :-
`sadd key member01 [number02 ...]` | 添加数据
`smembers key` | 获取全部数据
`srem key member01 [member02 ...]` | 删除数据
`scard key` | 获取集合中数据个数
`sismember key member` | 判断集合中是否存在指定的数据
`srandmember key [count]` | 随机获取count个
`spop key` | 获取并移除
`sinter key01 [key02]` | 求交集（两个集合相同的部分）
`sunion key01 [key02]` | 求并集（两个集合去重后的所有内容）
`sdiff key01 [key02]` | 求差集（A集合中存在但是B集合中不存在的）
`sinterstore destination key01 [key02]` | 求交集并同时存储到指定集合中
`sunionstore destination key01 [key02]` | 求并集并同时存储到指定集合中
`sdiffstore destination key01 [key02]` | 求差集并同时存储到指定集合中
`smove source destination member` | 将指定数据从原始集合中移动到目标集合

### 2) 注意事项

- set类型不允许出现重复的值，如果重复，后面的添加会失败
- set 的底层虽然与hash存储结构相同，但是没有value的空间

## 5. sorted_set

> 有序的set

### 1) 相关命令

命令 | 说明
:- | :-
`zadd key score01 member01 [score02 member02]` | 添加数据，其中score为排序字段
`zrange key start stop [withscores]` | 获取数据，start开始到stop结束，withscores表示输出排序字段
`zrevrange key start stop [withscores]` | 同上，逆序
`zrem key member [member ...]` | 删除数据
`zrangebyscore key min max [withscores] [limit]` | 根据区间进行查询，从小到大
`zrevrangebyscore key max in [withscores]` | 同上从大到小
`zremrangebyrank key start stop` | 根据索引删除
`zremrangebyscore key min max` | 根据排序字段区间删除
`zcard key` | 获取集合中数据的个数
`zcount key min max` | 获取排序字段区间的数据个数
`zinterstore destination numkeys key01 [key02 ...]` | 求和交集
`zunionstore destination numkeys key01 [key02]` | 求和并集
`zrank key member` | 获取数据对应的索引值，从前往后数
`zrevrank key member` | 同上，从后往前数
`zscore key member` | 获取值
`zincrby key increment member` | 修改值

### 2) 注意事项

- score数据的内存点用空间是64个二进制位
- score保存的数据可以是浮点数，对应double类型，容易丢失精度
- sorted_set底层存储是基于set，所以数据同样不允许重复

## 6. Bitmaps

> 一种空优的解决方案，本质就是String类型 ，基二进制位进行操作
> 一个字节等8个二进制位，每个二进制位可以记录一个数据的状态

命令 | 说明
:- | :-
`getbit key offset` | 获取指定key对应偏移量上面的bit值
`setbit key offset value` | 设置指定key对应偏移量上面的bit值，value只能是1或者0

> 统计状态可以使用位运算的方式进行统计

命令 | 说明
:- | :-
`bitop op destKey key01 [key02 ...]` | 对于key进行按位与、或、非、异或运算，将结果存放到deskey中
`bitcount key01 [start end]` | 统计key中1的数量

## 7. HyperLogLog

> 用于统计不重复数据的数量，统计数据可能会存在误差。误差范围在0.81%，每个key最大空间为12K，合并后为12K

命令 | 说明
:- | :-
`pfadd key element01 [element02 ...]` | 添加数据
`pfcount key01 [key02 ...]` | 统计数据
`pfmerge destkey sourcekey01 [sourcekey02 ...]` | 合并数据

## 8. GEO

> 坐标操作（经纬度），用于导航 / 地图

命令 | 说明
:- | :-
`geoadd key longitude latitude member [...]` | 添加坐标
`geopos key member` | 获取坐标
`geolist key member01 member02 [unit]` | 计算坐标距离
`georadius key longitude latitude radius m \ mk \ ft \ mi [withcoord] [withdist] [count count]` | 从坐标求范围内数据
`georadiusbymember key member radius m \ km \ ft \ mi [withcoord] [withdist] [count count]` | 根据点求范围内数据
`geohash key member01 [member02 ...]` | 点对应坐标hash值


# 五、通用命令

## 1. key 命令

> key用于表示数据的键，key的本身就是一个字符串

命令 | 说明
:- | :-
`del key` | 删除指定的key
`exists key` | 判断指定的key是否存在
`type key` | 获取key类型
`expire key seconds` | 为指定的key设置有效期，秒为单位
`pexpire key milliseconds` | 同上，毫秒为单位
`expireat key timestamp` | 同上，使用时间戳，秒为单位
`pexpireat key milliseconds-timestamp` | 同上，毫秒为单位
`ttl key` | 获取指定key的有效期，秒为单位<br/>-1没设置有效期、-2失效了、数值具体效期
`pttl key` | 同上，毫秒为单位
`persist key` | 将时效性key转换为永久性
`keys pattern` | 查询key<br/>\*任意数量的任意字符<br/>?匹配单个任意字符<br/>\[\]匹配一个指定的符号
`rename key newkey` | 为指定的key改名，如果newkey存在则覆盖
`renamenx key new key` | 同上，不覆盖
`sort` | 对所有的key排序（list / set / sorted_set）

## 2. 数据库命令

> Redis一共给我提供了16个数据库，编号分别是0~15

命令 | 说明
:- | :-
`select index` | 选择数据库
`quit` | 退出
`ping` | 检查是否连通
`echo message` | 输出信息
`move key db` | 将指定的key移动到指定的db
`dbsize` | 数据库中key的数量
`flushdb` | 清空当前库
`flushall` | 清空所有库

> 清空容易出现阻塞现象


# 六、关闭Redis

> 客户端命令

命令 |  说明
:- | :-
shutdown | 关闭正在运行的redis-server


# 七、启动多个redis-server

## 1. 通过命令换端口

命令 | 说明
:- | :-
`redis-server --port 9527` | 指定使用9527作为启动redis的端口

> 指定端口的启动方式，登录的时候也同样需要制定端口号如：`redis-cli [-h 127.0.0.1] -p 9527`

## 2. 通过配置文件启动

- 配置文件所在位置：`/etc/redis/redis.conf`
- 简化配置文件中内容：`sudo cat /etc/redis/redis.conf | grep -v "#" | grep -v "^$" > redis-6379.conf`

> 配置文件中常用参数说明

|序号|配置项|说明|
|-|-|-|
|1|daemonize no|Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ）|
|2|pidfile /var/run/redis.pid|当 Redis 以守护进程方式运行时，Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定|
|3|port 6379|指定 Redis 监听端口，默认端口为 6379，作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女 Alessia Merz 的名字|
|4|bind 127.0.0.1|绑定的主机地址|
|5|timeout 300|当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能|
|6|loglevel notice|指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice|
|7|logfile stdout|日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null|
|8|databases 16|设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id|
|9|`save <seconds><br>Redis` 默认配置文件中提供了三个条件：<br>**save 900 1**<br>**save 300 10**<br>**save 60 10000**<br>分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。|指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合|
|10|rdbcompression yes|指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大|
|11|dbfilename dump.rdb|指定本地数据库文件名，默认值为 dump.rdb|
|12|dir ./|指定本地数据库存放目录|
|13|`slaveof <masterip> <masterport>`|设置当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步|
|14|`masterauth <master-password>`|当 master 服务设置了密码保护时，slave 服务连接 master 的密码|
|15|requirepass foobared|设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH ``<password>` 命令提供密码，默认关闭|
|16|maxclients 128|设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息|
|17|`maxmemory <bytes>`|指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区|
|18|appendonly no|指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no|
|19|appendfilename appendonly.aof|指定更新日志文件名，默认为 appendonly.aof|
|20|appendfsync everysec|指定更新日志条件，共有 3 个可选值：<br><br>- **no**：表示等操作系统进行数据缓存同步到磁盘（快）<br>- **always**：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）<br>- **everysec**：表示每秒同步一次（折中，默认值）|
|21|vm-enabled no|指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制）|
|22|vm-swap-file /tmp/redis.swap|虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享|
|23|vm-max-memory 0|将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0|
|24|vm-page-size 32|Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值|
|25|vm-pages 134217728|设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。|
|26|vm-max-threads 4|设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4|
|27|glueoutputbuf yes|设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启|
|28|hash-max-zipmap-entries 64<br>hash-max-zipmap-value 512|指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法|
|29|activerehashing yes|指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍）|
|30|include /path/to/local.conf|指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件|

- `redis-6379.conf`中保留以下内容即可
```
daemonize yes
port 6379
bind 127.0.0.1
logfile redis-6379.log
dir /home/maqf/redisData
```

- 通过配置文件启动redis
`sudo redis-server /etc/redis/redis-6379.conf`

- 查看启动的进程
`ps -ef | grep redis`


# 八、持久化

- RDB - 记录快照：当前啥样就保存成啥样
- AOF - 记录过程：保存所有操作过程

## 1. RDB模式（快照）

- RDB模式 使用save做持久化操作

配置 | 说明
:- | :-
`dbfilename dump.rdb` | 设置和本地数据库文件名，默认值为dump.rdb-(dump.端口号.rdb)
`dir` | 设置存储.rdb文件的路径
`rdbcompression yes` | 设置存储时是否进行压缩，默认为yes
`rdbchecksum yes` | 设置是否进行RDB文件格式校验，默认开启

`/etc/redis/redis-6379.conf`

```conf
daemonize yes
bind 127.0.0.1
port 6379
logfile /root/redisData/redis-6379.log
dir /root/redisData
dbfilename dump.6379.rdb
rdbcompression yes
rdbchecksum yes
```

> 不建议直接使用save命令，因为可能会产生命令的阻塞，可以使用bgsave后台保存，可以解决阻塞问题 

配置 | 说明
:- | :-
`stop-writes-on-bgsave-error yes` | 后台存储过程中如果出现错误，是否停止，默认使用开启
`save second changes` | 满足的限定时间范围内key的变化数量达到了指定的数量就自动执行bgsave命令<br>second:监控的时间范围<br>changes：监控key的变化量

- 重启、关闭服务器

命令 | 说明
:- | :-
`debug reload` | 服务器运行过程中重启
`shutdown save` | 关闭服务器同时保存数据

- RDB模式的优缺点
	- 优点
		- RDB是一个紧凑压缩的二进制文件，存储效率高
		- RDB内部存储的是Redis在某个时间点的数据快照，适合用于数据备份，全量复制等场景
		- RDB恢复数据的速度要比AOF快
	- 缺点
		- RDB方式无论是执行指令还是利用配置，都无法做到实时持久化，具备数据可丢失性
		- `bgsave`指令每次运行都要创建一个子进程，有性能上的损耗
		- Redis持久化的文件可能在版本间存在不兼容的情况，需要通过数据库中专解决兼空性问题

## 2. AOF模式（过程）

- AOF开关配置

配置 | 说明
:- | :-
`appendonly yes` | 开始AOF持久化模式
`appendfilename filename` | AOF备份文件名`appendonly-端口号.aof`
`dir` | 存储目录

- AOF写数据的是哪种策略

配置 | 说明
:- | :-
`appendfsync always` | 每次，安全性最高，性能最低
`appendfsync everysec` | 每秒，默认，安全与性能相对平衡
`appendfsyc no` | 系统配置，不可控

```
daemonize yes
bind 127.0.0.1
port 6379
logfile /root/redisData/redis-6379.log
dir /root/redisData
dbfilename dump.6379.rdb
rdbcompression yes
rdbchecksum yes
appendonly yes
appendfilename appendonly.6379.aof
appendfsync always
```

- AOF的重写

> 解决重复的冗余指令，系统后帮我们实现

1. 手动重写指令

命令 | 说明
:- | :-
`bgrewriteaof` | 手动执行后台重写

2. 自动重写配置

配置 | 说明
:- | :-
`auto-aof-rewrite-min size` | 自动重写AOF的最小尺寸，达到了size就重写（对比原有）
`auto-aof-rewrite-percentage percentage` | 自动重写的百分比（基础尺寸）

> 常用配置，配置后可用info指令查看设置结果

```
# 代表当前AOF文件空间和上次重写后AOF空间的比值
auto-aof-rewrite-percentage 100
# AOP超过10m就开始收缩
auto-aof-rewrite-min-size 10mb
```

## 3. RDB vs AOF

持久化方式 | RDB | AOF
:- | :- | :-
存储空间 | 小（数据级-压缩）| 大（指令级-重写）
存储速度 | 慢 | 快
恢复速度 | 快 | 慢
数据安全性 | 可能丢失数据 | 依据策略决定
资源消耗 | 高/重量级 | 低/轻量级
启动优先级 | 低 | 高


# 九、事务处理

## 1. 简介

> Redis事务就是一个命令执行的队列，将一些列的预定义命令包装成一个整体（一个队列），当执行时，一次性按照顺序一次执行，中间不允许被打断或者干扰

## 2. 基本操作

事务执行边界命令

命令 | 说明
:- | :-
`multi` | 开启事务
`exec` | 执行事务
`discard` | 取消事务的执行

> 在事务处理中，语法错误会报错，正确的则正常执行

## 3. 锁

> 监控数据的变化，根据数据的状态做选择性的操作

命令 | 说明
:- | :-
`watch key01 [key02 ...]` | 对指定的key进行监控，在exec之前，如果key发生了变化，终止事务的执行
`unwatch` | 取消所有对key的监控

> 事务中不能使用watch，会报错


## 4. 分布式锁

> 类似于Java中的线程同步锁

命令 | 说明
:- | :-
`setnx lock-key val` | 上锁！设置成功返回1，设置失败返回0
`del key` | 删除锁

> 	为避免死锁，可以通过`expire`为锁添加有效时间

命令 | 说明
:- | :-
`expire lock-key second` | 秒为单位
`pexpire lock-key millseconds` | 毫秒为单位

---


# 十、删除策略

## 1. 过期数据

> 通过`ttl`指令查看数据的有效期，-2表示已过期的数据，已被删除的数据，或者是未定义的数据。

## 2. 数据删除策略

> 数据删除策略设计的初衷 - 用于平衡CPU性能

- 定时删除（空优）：创建一个定时器当key设置有过期时间，且过期时间到达，由定时器任务立即执行删除
	- 优点：节约存储空间
	- 缺点：CPU性能损耗
- **惰性删除（时优）**：数据到期时，不做任何处理，下次访问的时候被删除
	- 优点：节约CPU性能
	- 缺点：内存空间占用量大
- **定期删除（平衡）**：定期的对每个库中的key做轮巡访问，随机挑选若干个key，如果过期就删除

## 3. 逐出算法

> 内存不够了，需要淘汰出一部分数据，被淘汰的数据可以从数据库里读回来

- 相关配置

配置 | 说明
:- | :-
`maxmemory` | 占用物理内存比例，默认值0，表示不限制，生产环境中需设置，通常为60%左右
`maxmemory-samples` | 随机获取检测删除数据
`maxmermory-policy` | 达到最大内存后，对被挑选出来的数据进行删除的策略（逐出算法）

- `maxmemory-policy`配置选项
	- **检测易失数据**
		- volatile-lru：最长时间没被使用的数据
		- volatile-lfu：在一定时间内使用频率最少的数据
		- volatile-ttl：根据有效期
		- volatile-random：随机
	- 检测全库数据
		- allkeys-lru ...
		- allkeys-lfu ...
		- allkeys-random ...
	- 放弃数据驱逐
		- no-envication：禁止驱逐，会引发内存溢出Out Of Memory(OOM)


# 十一、主从复制

## 1. 简介

> 主从复制就是将一个主服务器的数据，及时有效的共享复制到其他从的服务器中，一个主服务器可以对应多个从服务器，也就是一对多的关系，每个从服务器只对应一个主服务器

工作职责
- 主
	- 写数据
	- 执行写入操作是将出现变化的数据自动同步到各个从服务器
	- 读取数据（少用）
- 从
	- 读取数据
	- 写数据（禁止）

作用
- 读写分离
- 负载均衡
- 故障恢复
- 数据冗余（备份）
- 实现高可用

## 2. 工作流程

- 主从复制过程的三个主要阶段
	1. 建立连接（从连接主）
	2. 数据同步（主向从）
	3. 命令传播（互相发送指令）

### 1) 主从连接方式

> 本质上就是建立主从之间的socket

- 方式一：客户端发送命令

```
# slaveof <masterip> <masterport>

redis-cli -p 9527  # 登录从服务器

slaveof 127.0.0.1 6379  # 连接主服务器
```

- 方式二：启动服务器参数

```
# redis-server --slaveof <masterip> <masterport>

# 启动从服务器的同时，连接主服务器
redis-server /etc/redis/redis-9527.conf --slaveof 127.0.0.1 6379
```

- 方式三：服务器配置

配置 | 说明
:- | :-
`slaveof <masterip> <masterport>` | 从服务器配置文件添加连接主服务器文件的内容

`slaveof 127.0.0.1 6379`

- 断开连接

命令 | 说明
:- | :-
`slaveof no one` | 从服务器执行，断开与主的连接

- 授权访问

	主服务器配置文件设密码

配置 | 说明
:- | :-
`requirepass <password>` | 主（master）服务器配置文件中添加密码

	主客户端发送命令设置密码

命令 | 说明
:- | :-
`config set requirepass <password>` | 设置密码
`config get requirepass` | 获取密码

	客户端验证密码

- Shell登录命令`redis-cli -a <password>`连接时验证密码
- Redis验证命令`auth <password>`连接后验证密码
- 客户端配置文件中配置`masterauth <password>`通过配置文件中的设置验证密码

### 2) 相关过程说明

- 数据同步：全量数据的复制，底层是RDB的同步。增量（部分复制）复制，是AOF
	- 复制缓冲区配置`repl-backlog-size 1mb` - 默认1MB，通常设置为主服务器内存的60%左右
- 命令传播：反复执行命令传播，这个阶段可能会出现断网或其他终断现象
	- 闪断闪连：忽略
	- 短时间终断：部分复制
	- 长时间终断：全部复制
- 心跳机制：实时保持主从双方在线
	- 当从服务器多数掉线、延迟高的时候。主服务器为保障数据稳定性，可以拒绝同步操作

配置 | 说明
:- | :-
`min-slaves-to write 2` | 从服务器连接数量少于2个，强制关闭主服务器写入功能，停止同步
`min-slaves-max-lag 10` | 从服务器延迟大于10s，强制关闭主服务器写功能，停止同步

- 释放从服务器

配置 | 说明
:- | :-
`repl-timeout 60` | 默认值为60秒，超时则会释放从服务器

- 解决数据不（同步）一致

配置 | 说明
:- | :-
`slave-serve-stale-data yes/no` | 慎用，开启后只响应info/slaveof等少数命令



# 十二、哨兵

## 1. 简介

哨兵（Sentinel）解决主服务器宕机问题，让原主下岗，新主上岗

- 作用
	- 监控：主从之间是否正常运行
	- 通知：当被监控的服务器出现问题时，告知其他客户端
	- 自动故障转移：选择新的主服务器，将其他从连接到新主服务器





















