
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

> 触发器是和数据表有关的数据库对象，可以在CUD之前或之后出发并执行触发器中指定的sql语句集合，常应用于：数据校验、日志记录等

- NEW / OLD
	- insert - NEW 表示将要新增的数据
	- update - NEW 表示修改之后的，OLD表示修改之前的
	- delete - OLD 表示删除之前的数据

## 1. 创建触发器

```sql
create trigger 触发器名称
before/after insert/update/delete
on 表名
for each row -- 行级
begin
	-- 具体内容
end;
```

```sql
create table `teacher` (
`id` int unsigned primary key auto_increment,
`name` varchar(30) not null
);

# 创建触发器
create trigger `t_lession_insert`
after insert
on `lession`
for each row
begin
	insert into `teacher` (`name`) value ('Mr.X');
end;

insert into `lession` (`lession_name`) value ('Oracle');
```


# 三、视图

> 视图（View）是一种虚拟存在的表。视图并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。通俗的讲，视图就是一条SELECT语句执行后返回的结果集。所以我们在创建视图的时候，主要的工作就落在创建这条SQL语句上

- 视图相对于普通的表的优势主要包括以下几项
	- 简单：使用视图的用户完全不需要关心后面对应的表的结构、关联条件和筛选条件，对用户来说已经是过滤好的复合条件的结果集
	- 安全：使用视图的用户只能访问他们被允许查询的结果集，对表的权限管理并不能限制到某个行某个列，但是通过视图就可以简单的实现
	- 数据独立：一旦视图的结构确定了，可以屏弊表结构变化对用户的影响，源表增加对列对视图没有影响；源表修改列名，则可以通修改视图来解决，不会造成对访问者的影响

```sql
# 视图的创建
create view `v_sel_stu` as select `name`, `age`, `score`, `classid` from `stu`;
# 使用创建的视图来查询
select * from `v_sel_stu`;
select `name`, `score` from `v_sel_stu`;
# 查询视图的结构
desc `v_sel_stu`;
# 被创建的视图可以当做数据表来对其进行使用
```

```sql
# 视图的创建
create view `v_sel_stu` as select `name`, `age`, `score`, `classid` from `stu`;

select * from `v_sel_stu`;
select `name`, `score` from `v_sel_stu`;

# 查询视图结构
desc `v_sel_stu`;
```


# 四、事务处理

```sql
set autocommit=0; # 关闭自动提交，相当于是开启事务处理
set autocommit=1; # 开启自动提交，每一个命令都自动提交，相当于是关闭事务处理

start transaction; # 开启一个事务
commit; # 提交
rollback; # 事务回滚
```

```sql
# 事务处理 （需要数据表的类型要是InnoDB）
select * from `stu`;
start transaction; -- 开启事务
delete from `stu` where `id` = 7;
rollback; # 事务回滚（rollback使用之后事务会自动关闭，如果想再次使用，需要重新开启）
commit; # 提交确认，commit提交之后和rollback一样事务也会跟随自动关闭
```


# 五、存储过程和函数

> 经过编译存储并存储在数据库中的一段SQL语句的集合

- 过程：没有返回值的函数
- 函数：有返回值的过程

## 1. 创建和调用存储过程

```sql
create procedure procedure_name([args])
begin
-- sql语句 ...
end;
```

> 注意在命令终端中默认分隔符问题，修改为$ `delimiter $`

```sql
# 储存过程
# 创建
create procedure `pro_sel_stu`()
begin
	select * from `stu`;
end;
# 调用
call `pro_sel_stu`();
```

## 2. 查看存储过程

> MySQL数据库中proc表当中存储了自定义存储过程

```sql
# 查询指定的存储过程
select name from mysql.proc where db=`lx_java`;
# 查询全部存储过程
show procedure status;
# 查询创建存储过程是使用 的sql语句
show create procedure pro_sle_stu;
```


## 3. 语法

### a. 变量

- declare - 变量的声明

> 通过declare可以定义一个局部变量，该变量的作用范围只能在begin到end之间

```sql
# 变量的使用
create procedure `pro_demo01`()
begin
	declare num int default 18; -- 声明一个变量
	select num + 20;
end;

call `pro_demo01`();
```

- set - 赋值

> 可以通过set指令给变量赋值或表达式

```sql
# 变量赋值
# 通过set
create procedure `pro_demo02`()
begin
	declare `age_val` int default 0;
	set `age_val` = 18;
	select `age_val`;
end;
call `pro_demo02`();

# 通过查询
create procedure `pro_demo03`()
begin 
	declare `age_val` int default 0;
	select `age` into `age_val` from `stu` where `name` = 'Jack';
	select concat('age = ', `age_val`);
end;
call `pro_demo03`();
```

### b. if 判断

```sql
# if 判断
create procedure `pro_demo04`()
begin
	declare `age_val` int default 0;
	declare `res_val` varchar(30);
	select `age` into `age_val` from `stu` where `name` = 'Rolly';
	if `age_val` >= 22 then
		set `res_val` = '适婚';
	elseif `age_val` >= 18 then
		set `res_val` = '成年';
	else
		set `res_val` = '未成年'; 
	end if;
	select concat('res_val = ', `res_val`);
end;
call `pro_demo04`();
```

### c. 参数传递

- in

```sql
# in
create procedure `pro_demo05`(in `age` int)
begin
	declare `res` varchar(30) default '';
	if `age` >= 22 then
		set `res` = '适婚';
	elseif `age` >= 18 then
		set `res` = '成年';
	else
		set `res` = '未成年';
	end if;
	select concat('res = ', `res`);
end;
call `pro_demo05`(19);
```

- out

```sql
# out 
create procedure `pro_demo06`(in `age` int, out `res` varchar(30))
begin
	if `age` >= 22 then
		set `res` = '适婚';
	elseif `age` >= 18 then
		set `res` = '成年';
	else
		set `res` = '未成年';
	end if;
end;
call `pro_demo06`(17, @res);
select concat('res = ', @res);
```

### d. case

```sql
# case
create procedure `pro_demo07`(in `val` int)
begin
	case `val`
		when 1 then
			select 'A';
		when 2 then
			select 'B';
		else
			select 'X';
	end case;
end;
call `pro_demo07`(2);

create procedure `pro_demo08`(in `val` int)
begin
	case
		when 1 = `val` then
			select 'A';
		when 2 = `val` then
			select 'B';
		else
			select 'X';
		end case;
end;
call `pro_demo08`(1);
```

### e. while

```sql
# while
create procedure `pro_demo09`(in `num` int)
begin
	declare `sum` int default 0;
	declare `index` int default 0;
	while `index` <= `num` do
		set `sum` = `sum` + `index`;
		set `index` = `index` + 1;
	end while;
	select concat('sum = ', `sum`);
end;
call `pro_demo09`(100);
```

### f. repeat

```sql
# repeat
create procedure `pro_demo10`(in `num` int)
begin
	declare `sum` int default 0;
	declare `index` int default 0;
	repeat
		set `sum` = `sum` + `index`;
		set `index` = `index` + 1;
		until `index` > `num`
	end repeat;
	select concat('sum = ', `sum`);
end;
call `pro_demo10`(100);
```

### g. loop

```sql
# loop
create procedure `pro_demo11`(in `num` int)
begin
	declare `sum` int default 0;
	declare `index` int default 0;
	s:loop
		set `sum` = `sum` + `index`;
		set `index` = `index` + 1;
		if `index` > `num` then
			leave s;
		end if;
	end loop;
	select concat('sum = ', `sum`);
end;
call `pro_demo11`(100);
```

### h. 游标

```sql
# 游标
create procedure `pro_demo12`()
begin
	declare `stu_name` varchar(80);
	declare `stu_age` int;
	declare `res` cursor for select `name`, `age` from `stu`;
	open `res`; -- 开启游标
	fetch `res` into `stu_name`, `stu_age`;
	select concat(`stu_name`, ', ', `stu_age`);
	fetch `res` into `stu_name`, `stu_age`;
	select concat(`stu_name`, ', ', `stu_age`);
	close `res`;
end;
call `pro_demo12`();
```

### i. 存储函数

```sql
# 函数
create function `func01`(`stu_id` int)
returns varchar(64) -- 返回值类型
begin
	declare `stu_name` varchar(64);
	select `name` into `stu_name` from `stu` where `id` = `stu_id`;
	return `stu_name`;
end;
select `func01`(2);
select * from `stu` where `name` = func01(2);
```


