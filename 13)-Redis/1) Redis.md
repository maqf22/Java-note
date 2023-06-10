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
`increbyfloat key increment` | 自增increment浮点数
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






