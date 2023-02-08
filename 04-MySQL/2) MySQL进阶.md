
# 一、索引

## 1. 索引概述

- 索引（index）是帮助MySQL高效获取数据的**数据结构**（有序）
- 在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这此数据结构上实现高级查找算法，这种数据结构就是索引

## 2. 索引的优势与劣势

- 优势
	1. 提高数据检索的效率，降低数据库的IO成本
	2. 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗
- 劣势
	1. 实际上索引也是一张表，该表中保存主键与索引字段，并指向实体类的记录，所以索引列也是要占用空间的
	2. 虽然索引大提高了查询效率，同时却也降低更新表的速度，如对表进行`insert`、`update`、`delete`。因更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了添引列的字段，都会调整因为更新所带来的键值变化后的索引信息

## 3. 索引分类

1. 单值索引：即一个索引只包含单个列，一个表可以有多个单列索引
2. 唯一索引：索引列的值必须唯一，如果不设置非空属性，值可以为空
3. 复合索引：即一个索引包含多个列

## 4. 索引语法

> 索引在创建表的时候，可以同时创建 ，也可以随时增加新的索引

### a. 查看索引

```sql
show index from 表名;
```

```sql
# 查看索引
show index from `stu`;
show index from `student`;
```

### b. 创建索引

```sql
create index 索引号 ON 表名 (字段名...);
```

```sql
# 创建索引
create index `index score` on `stu`(`score`);
```

### c. 删除索引 

```sql
drop index 索引名 on 表名; 
```

```sql
# 删除一个索引
drop index `index score` on `stu`;
```

### d. alter命令操作索引

```sql
# 该语句添加一个主键，这意味着索引值必须是唯一，且不能为NULL
alter table tb_name add primary key(column_list);
# 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）
alter table tb_name add unique index_name(column_list);
# 添加普通索引，索引可以出现多次
alter table tb_name add index index_name(column_list);
# 该语句指定了索引为FULLTEXT，用于全文索引
alter table tb_name add fulltext index_name(column_list);
```

```sql
# alter 操作索引
create table `test_table` (
`id` int,
`name` varchar(30),
`gender` enum('-1', '0', '1'),
`score` float(6,1),
`tel` varchar(20)
);

show index from `test_table`;

alter table `test_table` add primary key(`id`);
alter table `test_table` add unique `gender`(`gender`);
alter table `test_table` add index `index_score`(`score`);
alter table `test_table` add fulltext `fulltext_tel`(`tel`);
```


# 二、触发器





