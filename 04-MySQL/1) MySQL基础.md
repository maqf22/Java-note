
# 一、MySQL环境搭建及配置

1. 安装MySQL
2. 配置环境变量path，在path值当中添加mysql可执行文件所有在目录
3. 在命令行输入mysql -u root -h 127.0.0.1 -p 回库
4. 使用 \\s 命令查看当前MySQL配置信息，端口号以及字符集
5. 设置utf8字符编码-在my.ini配置文件当中

> 在\[client\]段落后添加：default-charater-set=utf8
> 在\[mysqld\]段落后添加：character_set_server=utf8

注意：若在修改字符集之前有数据库或者表存在，并且部分字段信息是???形式可以使用一下语句调整表结构

```sql
mysql> alter table 表名 convert to character set utf8;
```


# 二、概念

- 数据：Data
- 数据库：DataBase（DB）
- 数据库管理系统：DataBase Management System（DBMS）
- 数据库系统：DataBase System（DBS）
- SQL语言分为四个部分
	- DDL(Data Definition Language)：数据定义语言 - (create, drop, alter...)
	- DML(Data Manipulation Language)：数据操作语言 - (insert, update, delete...)
	- DQL(Data Query Language)：数据查询语言 - (select...)
	- DCL(Data Contorl Language)：数据控制语言 - (grant, comit, rollback...)


# 三、连接数据库

> mysql -h 主机名 -u 用户名 -p 密码 库名

```bat
mysql # 采用匿名方式登录本机服务器
mysql -h localhost -u root -p # 常规登录本机服务器
mysql -u root -p # 默认本机登录
mysql -u root -p testData # 登录同时直接进入testData数据库
```

```bat
mysql -uroot -proot # 直接使用用户名和密码登录
```

```sql
# 查看当前MySQL中已存在的数据库
show databases;

# 查看当前正在使用的数据库，NULL表示没有使用数据库
select database();

# 进入数据库
use test;
```


# 四、数据库基本操作

- 每一条命令都要以分号结束，表示完成
- 可以将一条命令在任意空格处拆分成多行，当遇到分号时结束当前命令的执行
- 可以通过在命令结尾添加\\c的方式来取消当前命令的执行
- 可以使用\\q、exit、quit来退出MySQL终端
- 可以通过\\h、?、help命令在MySQL中查看帮助信息
- 可以通过\\G来将查询到的结果纵向显示在命令终端当中
- 可以使用\\s来查看当前数据库服务器状态（配置）


# 五、基本的MySQL数据库操作指令

## 1. 针对数据库的基本操作

```sql
create database 数据库名称; -- 创建数据库
use 数据库名称; -- 进入数据库
select database(); -- 查看当前数据库
drop database 数据库名称; -- 删除数据库
show databases; -- 查看当前MySQL中存在的数据库
show create database 数据库名称; -- 查看创建数据库语句
```

注意：
1. MySQL数据库当中命令不区分大小写
2. 在window中，数据库名称同样不区分大小写，但在Linux中严格区分大小写
3. 每创建一个数据库，就会在data目录下创建一个以数据库名称命名的目录

## 2. 针对数据表的最基本操作

```sql
-- 创建一个数据表
create table 数据表名(字段1信息，字段2信息...);
-- 查看当前数据库的表
show tables;
-- 查看数据表的结构
desc 数据表名;
-- 删除一个数据表
drop table 数据表名;
-- 修改一个数据表的结构
alter table 表名称 相关操作;
-- 向数据表中添加一个条数据
inser into 表名称(字段名) values (字段值);
-- 查看数据表中指定字段的信息
select 字段名 from 表名;
-- 更新数据表中指定条件，指定字段的信息
update 表名 set 字段名=新值 [where 条件];
-- 指定表当中根据指定条件删除数据
delete form 表名 [where 条件];
-- 查看创建数据表时系统执行的命令
show create table 数据表名;
```

```sql
-- 创建一个数据表，id/name/age都是字段名（列名）
create table student(id int, name varchar(32), age int);

-- 查看数据表的结构
desc student;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(32) | YES  |     | NULL    |       |
| age   | int(11)     | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

-- 修改数据表 - 添加一个gender字段
alter table student add gender varchar(4);
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  |     | NULL    |       |
| name   | varchar(32) | YES  |     | NULL    |       |
| age    | int(11)     | YES  |     | NULL    |       |
| gender | varchar(4)  | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+

-- 删除gender字段
alter table student drop gender;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(32) | YES  |     | NULL    |       |
| age   | int(11)     | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

-- 向数据表中插入数据
insert into student (id, name, age) value (1, 'Jack', 19);

-- 查看数据表中的数据
select * from student;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | Jack |   19 |
|    2 | Rose |   18 |
+------+------+------+
select name, id, age from student;
+------+------+------+
| name | id   | age  |
+------+------+------+
| Jack |    1 |   19 |
| Rose |    2 |   18 |
+------+------+------+

-- 修改指定条件数据表内容（没有条件修改的是的所有）
update student set id=3 where name="Rose";
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | Jack |   19 |
|    3 | Rose |   18 |
+------+------+------+

-- 删除指定条件的数据（没有条件删除所有）
delete from student where id=3;
+------+------+------+
| id   | name | age  |
+------+------+------+
|    1 | Jack |   19 |
+------+------+------+

-- 查看创建数据表时系统给我们执行的命令
show create table student\G;
*************************** 1. row ***************************
       Table: student
Create Table: CREATE TABLE `student` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(32) DEFAULT NULL,
  `age` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8

-- 删除数据表
drop table student;
```


# 六、数据表的概念

- 数据表是数据库中最基本的对象单元
- 记录称为：行
- 字段称为：列
- 设计表之前需寻找数据表对应的实体和属性的对应关系
	- 实体：要针对什么具体的事物创建对应的数据表
	- 属性：该事物具备的属性，在表中以字段作为诠释
		- ex：学生为实体，学号、姓名、性别等为属性
- ER图（实体联系图）
- 数据类型：数据表中的属性具备适当的数据类型，避免数据冗余
- 数据表设计三范式
	- 第一范式：数据库表中的每一列都不可再分，也就是原子性
	- 第二范式：在满足第一范式基础上要求每个字段都和主键完整相关，而不是仅和主键部分相关（主要针对联合主键而言）
	- 第三范式：表中的非主键字段和主键字段直接相关，不允许间接相关


# 七、四大数据类型

## 1. 数值型

1. **tinyint / 1字节**
2. smallint / 2字节
3. mediumint / 3字节
4. **int / 4字节**
5. **bigint / 8字节**
6. **float / 4-8字节**
7. double / 8字节
8. decimal / 自定义 / 以字符串形式表示浮点数

> **整型注意事项**：int(3)、smallint(3)等，整型后面的数字不会影响值的取值范围，但是会影响显示范围，如果超出范围，超出部分不会显示在查询结果当中

> **浮点型注意事项**：浮点数后面数字，将存入的数值四舍五入。ex `将1.234存入float(6,1)数据字段中，结果是1.2，其中6表示长度，1表示小数位长度，会四舍五入`

## 2. 字符串类型

1. **char\[(M)\] / M字节 / 定长字符串**
2. **varchar\[(M)\] / L + 1字节 / 可变长字符串**
3. tinyblob、tinytext / L + 1字节 / 小blob（二进制）和文本串
4. blob、tiny / L + 2字节 / 小blob（二进制）和文本串
5. mediumblob、mediumtext / L + 3字节 / 中blob（二进制）和文本串
6. longblob、longtext / L + 4字节 / 大blob（二进制）和文本串
7. **enum / 1 或 2字节 / 枚举**
8. set / 1/2/3/4/8字节 / 集合

> **字符串类型注意事项：**
> char和varchar长度在0-255之间
> 如果插入数据长度大于指定长度，将会截取指定长度作为存储
> char不可变长、varchar可变
> char存储不足指定长度再空格补全。varchar存储不足则不补全
> char效率高于varchar，但是空间大，不灵活（时优）
> blob类型和text类型用于存放大数据
> blob类型区分大小写，text类型不区分大小写
> enum和set是特列的串类型。值必须在定义字段时给定
> enum只能选择一个值插入数据，set可以选择多个

## 3. 日期和时间

1. date/ 3字节 / YYYY-MM-DD
2. time / 3字节 / hh:mm:ss
3. datetime / 8字节 / YYYY-MM-DD hh:mm:ss
4. timestamp / 4字节 / YYYYMMDDhhmmss
5. year / 1字节 / YYYY

## 4. NULL值

> 注意事项：
> 1. NULL不意味着没有值或者未知值
> 2. 可以检测某个值是否为NULL
> 3. 不能对NULL值进行算述运算，对NULL进行算述运算得到的结果还是NULL
> 4. 0或NULL都意味着false，其余的值都意味着true


# 八、类型转换

> 在MySQL当中类型的转换可以参考Java中类型转换


# 九、字段属性

- unsigned 无符号正整数
- zerofill 用0补齐不足位数
- auto_increment 自增属性
- null / not null 默认为NULL、不允许为空
- default 为字段设置默认值


# 十、索引

> 通过索引可以提高查询速度。增加数据查询的效率和准确性，在MySQL中有四类索引
> 索引是帮助MySQL进行高效查询的数据结构

1. **主键索引 primary key**
2. **唯一索引 unique**
3. 常规索引 index
4. 全文索引 fulltext

- 二叉树数据结构
	- 左子树小
	- 右子树大
- 优势和劣势
	- 优势：
		- 提高数据检索率，减低数据库的IO成本
		- 通过索引对数据进行排序，降低数据的成本，降低CPU负荷
	- 劣势：
		- 实际上也是一张表，该表中保存了主键的索引字段，并指向实体类的记录，所以索引字段也是要占用空间的
		- 虽然索引可以大提升查询效率，但是同时也降低了更新表的速度，如对insert、update、delete。因为更新表的时候，MySQL不仅要保存数据，还要保存一下索引文件，每次对表的更新，都会更新索引对应的信息


# 十一、数据表的类型及存储位置

> MyISAM和InnoDB两种表类型在MySQL中应用最广泛

- MyISAM特点
	- 优点：成熟、稳定、数据表效率高、易于管理
	- 缺点：不支持事物管理、不支持外键
- InnoDB特点
	- 优点：支持事物处理、支持外键
	- 缺点：数据表处理效率一般

```sql
alter table 表名 engine="InnoDB";
```


# 十二、创建完整的数据表

- 表结构

```sql
+---------+---------------------+------+-----+---------+----------------+
| Field   | Type                | Null | Key | Default | Extra          |
+---------+---------------------+------+-----+---------+----------------+
| id      | int(10) unsigned    | NO   | PRI | NULL    | auto_increment |
| name    | varchar(64)         | NO   |     | NULL    |                |
| gender  | enum('0','1','2')   | YES  |     | 2       |                |
| age     | tinyint(3) unsigned | YES  |     | 18      |                |
| score   | float               | YES  |     | 0       |                |
| tel     | varchar(20)         | NO   | UNI | NULL    |                |
| classid | varchar(16)         | YES  |     | LXJava  |                |
+---------+---------------------+------+-----+---------+----------------+
```

- 用于创建数据表的sql语句

```sql
mysql> create table stu (
	# id 整型 无符号 主键索引 自增属性
    -> id int unsigned primary key auto_increment,
    # name 可变长字符串（64长度） 不允许为空
    -> name varchar(64) not null,
    # gender 枚举类型字符串（预设值） 默认值 "2"
    -> gender enum("0", "1", "2") default "2",
    # age 小整型 无符号 默认值18
    -> age tinyint unsigned default 18,
    # score 浮点型（6位宽度显示，小数点后保留1位） 默认0
    -> score float(6.1) default 0.0,
    # tel 可变长字符串（长度20） 不允许为空 唯一性约束
    -> tel varchar(20) not null unique,
    # classid 可变长字符串（长度16） 默认值 "LXJava"
    -> classid varchar(16) default "LXJava");
Query OK, 0 rows affected (0.34 sec)
```

- 查看表结构

```sql
desc stu;
```

> **注意事项：**
> 字段之间要使用逗号分隔
> 最后一个字段不能有逗号
> 表名和字段名尽量不要和MySQL关键字相同
> 如果一定要使用关键字，可以用反引号将表名或字段名括起来
> 使用反引号会使建表效率增高
> 数据表名称和字段名不能重名
> auto_increment 属性必须依赖主键或者唯一性索引


# 十三、修改数据表结构

> 语法：`alter table 表名 action`
> 通过alter 语句可以对表进行以下操作

```sql
# 修改字段时同时修改字段名以及字段属性
alter table tName change oldName newName varchar(32) not null;
# 修改字段属性时不修改字段名称
alter table tName modify oldName varchar(32) not null;
# 添加字段
alter table tName add colName varchar(32) not null;
# 删除字段
alter table tName drop colName;
# 更改表名
alter table oldName rename as newName;
# 更改 auto_increment 属性的初始值
alter table tName auto_increment=1;
```

```sql
alter table student change name stuName varchar(128) not null;
alter table student modify name varchar(128) not null;
alter table student add address varchar(128) not null;
alter table student drop address;
alter table student rename as stu;
alter table stu auto_increment=1;
```


# 十四、删除数据表

- 备份数据表

```bat
# 备份指定数据库中的数据表
mysqldump -uroot -p 库名 表名 > 文件名.sql
# 备份指定的数据库中的所有的数据表
mysqldump -uroot -p 库名 > 文件名.sql
# 恢复备份文件到数据库
mysql -uroot -p 库名 < 文件名.sql
```

- 语法drop table 表名

```sql
drop table tableName01, tableName02;
```


# 十五、其他数据表操作

- 查看当前数据库中已存在的数据表

```sql
show tables;
```

- 查看创建某数据表时使用的使用的sql语句

```sql
show create table tableName;
```


# 十六、insert向数据表中插入数据

```sql
# 方法一：向指定的数据表、指定的字段中插入数据，注意：值的顺序需与字段的顺序一一对应
insert into tableName(col1, col2, ..., colN) value (val1, val2, ..., valN);
# 方法二：向指定数据表的所有字段插入数据，注意：值的顺序需与表结构的字段顺序一致
insert into tableName value (val1, val2, ..., valN);
# 方法三：多条数据插入（可指定字段，也可针对全部字段）
insert into tableName values
> (val1, val2, ..., valN),
> (val1, val2, ..., valN),
> (val1, val2, ..., valN);
# 方法四：利用查询语句进行插入
insert into tableName select val1, val2, ..., valN;
```

```sql
select `id`, `name`, `gender`, `age`, `score`, `tel`, `classid` from stu;

# insert into
# 方式一：
insert into `stu` (`name`, `gender`, `age`, `score`, `tel`, `classid`)
value ('Ben', '0', '17', '92.5', '15012430012', 'lxJava');
insert into `stu` (`name`, `tel`) value ('Kitty', '13660048199');

# 方式二：
insert into `stu` value ('14', 'Rolly', '1', '22', '59.0', '15012430013', 'lxJava');

# 方式三：
insert into `stu` values
('17', 'Rollly', '1', '22', '59.0', '15012430016', 'lxJava'),
('18', 'Rolly', '1', '22', '59.0', '15012430017', 'lxJava');

insert into `stu` (`name`, `tel`) values
('Kitty01', '15012430018'),
('Kitty02', '15012430019');

# 方式四：子查询
select '21', 'Rolly', '1', '25', '69.5', '15012430013', 'lxJava';
insert into `stu` select '21', 'Rolly', '1', '25', '69.5', '15012430020', 'lxJava';
```


# 十七、update修改数据表中的数据

```sql
update tableName set col1=val1, col2=val2, ..., colN=valN [where requirement];
```

```sql
update `stu` set `gender`='0' where id=5;
update `stu` set `classid`='MySQL'; # 不加条件修改所有
update `stu` set `gender`='0', `age`='18' where id=6;
```


# 十八、delete删除数据表中的数据

```sql
delete from tableName [where requirement];
```


# 十九、select查询数据表中的数据

## 1. 查询语句的基本语法结构

```sql
select <[*/字段名]> from 表名 
[where 查询条件]
[group by 列名 [having 分组条件]] 
[order by 列名]
[limit x,y];
```

```sql
# 基础用法
select * from `stu`;
select `name`, `gender`, `score`, `classid` from `stu`;

# where 条件
select `name`, `gender`, `score`, `classid` from `stu` where `gender`='0';

# group by 分组
select `name`, `gender`, `score`, `classid` from `stu` group by `gender`;
select `gender`, count(`gender`) from `stu` group by `gender`; # 通常结合统计使用
select `gender`, count(`gender`) from `stu` group by `gender` having `gender`!='-1';

# order by 排序
select * from `stu` order by `age`; # 默认是升序排序
select * from `stu` order by `age` asc; # 默认是升序排序（asc默认可以省略）
select * from `stu` order by `age` desc; # 降序排序

# limit 限制
select * from `stu` limit 4; # 显示前四条
select * from `stu` limit 2, 4; # 跳过前两条显示四条
```

## 2. 为查询输出的结果字段名改名

```sql
select 字段名 as 新字段名 from 表名 ...;
```

```sql
# 为查询出来的字须改名
select `gender`, count(`gender`) as 'total' from `stu` group by `gender`;
select `gender`, count(`gender`) 'total' from `stu` group by `gender`; # as可省略
```


# 二十、查询语句中的运算符

## 1. 逻辑运算符

- AND 或&&
- OR 或 ||
- NOT 或 !
- XOR 或 ^ （逻辑异或）

```sql
# 逻辑运算符
# 与
select * from `stu` where true and true; # MySQL中推荐这种写
select * from `stu` where true && true;
select * from `stu` where `age` > 18 and `gender`='1';
select * from `stu` where `age` > 18 && `gender`='1';
# 或
select * from `stu` where true or false;
select * from `stu` where `gender` = '1' or `gender` = '0';
select * from `stu` where `gender` = '1' || `gender` = '0';
# 非
select * from `stu` where not true;
select * from `stu` where not `gender` = '-1';
select * from `stu` where !(`gender` = '-1');
select * from `stu` where `gender` != '-1'; # 效率最高（!=只运算了一次）
# 异或（一真一假为真）
select * from `stu` where true xor false;
select * from `stu` where true xor true;
select * from `stu` where false xor false;
select * from `stu` where true ^ false;
```

## 2. 关系运算符

- = 是否相等，相等为真
- <=> 是否相等，相等为真，可判断NULL（不推荐使用）
- != 或 <> 不等于值为真
- <、>、<=、>= 基本关系运算符
- is null 空为真
- is not null 不空为真
- between and 在从哪儿到哪儿之间
- not between and 不在哪儿到哪儿之间
- like 匹配字符串，有匹配为真
- not like 匹配字符串，没有匹配为真
- in 在集合里匹配，有结果为真
- not in 不在集合里为真

```sql
# 关系运算符
# 等于
select * from `stu` where `id` = 2;
select * from `stu` where `id` <=> 2;

# 不等
select * from `stu` where `id` != 3;
select * from `stu` where `id` <> 3;

# 大于（等于）/ 小于（等于）
select * from `stu` where `score` >= 72;
select * from `stu` where `score` <= 80;

# 空
select * from `stu` where `score` is null;
select * from `stu` where `score` is not null;

# between and
select * from `stu` where `score` >= 75 and `score` <= 90;
select * from `stu` where `score` between 75 and  90; # 等同上
-- select * from `stu` where not (`score` >= 75 and `score` <= 90);
select * from `stu` where `score` not between 75 and  90; # 等同上

# like 表示字符串匹配（不区分大小写） %表示不定长度字符的通配符
select * from `stu` where `name` like 'R%'; # 查询R开头的
select * from `stu` where `name` like '%I%'; # 查询有I的
# _（下划线）表示单个字符的通匹配符
select * from `stu` where `name` like '__L%'; # 查询第三个是L的

# in 
select * from `stu` where `gender` = '0' or `gender` = '1';
select * from `stu` where `gender` in('0', '1'); # 同上
select * from `stu` where `gender` not in('0', '1');
```

## 3. 算术运算符
 
- 参考Java当中的算述运算符

```sql
# 算术运算符
select * from `stu` where `id` % 2 = 1;
select `name`, `age` + 10 from `stu`;
……
```

## 4. 常用函数

- count() 统计计数
- sum() 求和
- avg() 求平均值
- max() 求最大值
- min() 求最小值
- concat() 字符串连接

```sql
# 常用函数
# 统计计数
select count(*) from `stu`;
select `gender`, count(`gender`) from `stu` group by `gender`;
# 求和
select sum(`score`) from `stu`;
# 平均值
select avg(`score`) from `stu`;
select avg(`score`) from `stu` where `gender` = '0';
# 最大值
select max(`score`) from `stu` where `gender` = '1';
select max(`score`) from `stu`;
# 最小值
select min(`score`) from `stu`;
# 字符串连接
select concat('hello', 'MySQL');
select concat(
	(select `name` from `stu` where `id` = 3),
	', ',
	(select `name` from `stu` where `id` = 4)
);
```


# 二十一、多表查询

## 1. 联合查询：union

- 语法：`select ... union select ...`
- 联合查询要求：联合查询结果联合显示
	- 多个联合查询的字段结果数量一致（字段类型可以不一致）
	- 联合查询的字段来源于第一个查询语句的字段
- 查询选项：与select选项类似
	- all：保留所有记录
	- distinct：保留去重记录（默认）
- where子句的使用

```sql
create table t02 like t01;
insert into ...
select * from t01 union select t02;
select * from t01 union all select t02;
```

```sql
create table `t01`(
`id` int unsigned primary key auto_increment,
`name` varchar(64) not null,
`score` float(6,1) default 60.0
);

create table `t02` like `t01`;

insert into `t01` (`name`, `score`) values
('ZhangSan', '90'),
('LiSi', '91'),
('WangWu', '92'),
('LaoLiu', '93'),
('ZhaoQi', '94');

insert into `t02` (`name`, `score`) values
('ZhangSan', '90'),
('LiSi', '91'),
('Andy', '99'),
('Lily', '98'),
('Lucy', '97');

# 联合查询union
select * from `t01` union select * from `t02`;
select * from `t01` union distinct select * from `t02`; # 同上，默认去重
select * from `t01` union all select * from `t02`; # 包括重复的数据

# 查询的字段数量必须相同, 结构可以不同
# 查询结果的字段，以前表为主
select `id`, `name`, `score` from `t01` union distinct select `id`, `score`, `name` from `t02`;
select `id`, `name`, `score` from `t01` union distinct select `id`, `name`, `classid` from `stu`;
```

- 联合查询排序
	union的优先级高于`order by`

```sql
select * from t01 union select * from t02 order by age;
```

	如果需要针对`select`结果分别排序需要使用`limit`

```sql
# 无效
(select * from t01 order by age desc) union (select * from t02 order by age);
# 有效
(select * from t01 order by age desc limit 9999) union (select * from t02 order by age limit 9999);
```

```sql
# 联合查询的排序
select * from `t01` union distinct select * from `t02` order by `score`;
# 分别排序
(select * from `t01` order by `score` desc limit 999) union distinct (select * from `t02` order by `score` limit 999);
```

## 2. 连接查询：内链接、外链接、交叉链接

### a. 交叉连接：（笛卡尔积）

```sql
# 创建必须要表和数据
create table `student` (
	`id` int unsigned primary key auto_increment,
	`stu_name` varchar(30) not null,
	`lession_id` int unsigned
);

insert into `student` (`stu_name`, `lession_id`) values
('Jack', '1'),
('Rose', '2'),
('Ben', '3'),
('Lily', '4'),
('Lucy', '5');

create table `lession` (
	`lession_id` int unsigned primary key auto_increment,
	`lession_name` varchar(60) not null
);

insert into `lession` (`lession_name`) values
('JavaSE'),
('MySQL'),
('JDBC'),
('Javascript'),
('Spring');

# 交叉查询
select * from `student`, `lession`;
select `student`.`id`,
`student`.`stu_name`,
`lession`.`lession_id`, 
`lession`.`lession_name`
from `student`, `lession` where `student`.`lession_id` = `lession`.`lession_id`;
```

### b. 内连接：`inner join` - 将两张表根据指定的条件连接起来，严格连接
- 内连接是将一张表的每一条记录去另外一张表根据条件匹配
	- 匹配成功：保留连接的数据
	- 匹配失败：左右表不保留 
- 语法：`左表 join 右表 on 连接条件`

```sql
select t01.*, t02.name as newName from t01 inner join t02 on t01.kid = t02.id;
```

> 内连接可以使用where代替on

```sql
# 内连接（内连接和交叉连接效果一样，但内连接效率高一点点）
select 
`student`.`id`, 
`student`.`stu_name`, 
`lession`.`lession_id`, 
`lession`.`lession_name`
from `student` inner join `lession` on `student`.`lession_id` = `lession`.lession_id;
```

### c. 外连接：`outer join`是一种不严格的连接方式

- 左连接：`left join` - 左表为主
- 右连接：`right join` - 右表为主

```sql
select t01.*, t02.name as newName from t01 left join t02 on t01.kid = t02.id;
select t01.*, t02.name as newName from t01 right join t02 on t01.kid = t02.id;
```

> 外连接不可以使用where代替on

```sql
# 外连接
# 左连接，以左表为主，将左表所有数据显示出来，即使没有符合条件的数据
select * from `student` left join `lession` on `student`.`lession_id` = `lession`.`lession_id`;
# 右连接，以右表为主，将右表所有数据显示出来，即使没有符合条件的数据
select * from `student` right join `lession` on `student`.`lession_id` = `lession`.`lession_id`;
```

### d. 自然连接：`natural join` - 是一种自动寻找连接条件的连接查询

- 自然内连接：`natural join`
- 自然外连接：`natural left/right join`
- 自然连接条件匹配模式：自然寻找相同字段名称作为连接条件（字段名称相同）
- 对表的结构设计要求极高

```sql
select * from t01 natural join t02;
```

> 一般没有人会用

```sql
# 自然连接
select * from `student` natural join `lession`;
```

### e. using关键字：自然连接合并字段`using(字段名)`

```sql
# using关键字
select * from `student` inner join `lession` using(`lession_id`);
select * from `student` left join `lession` using(`lession_id`);
```

