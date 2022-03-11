# MySQL

## DDL

- 操作数据库

```java
// 查询所有数据库
show databases;
// 查询当前数据库
select database();
// 创建
create database if not exists test_db default charset utf8mb4;
// 删除
drop database if exists test_db;
// 使用
user test_db;
```

- 操作表

```java
// 查询所有表
show tables；
// 查询表结构
desc `test_table`;
// 查询指定表的建表语句
show create table `test_table`;
// 创建表
create table `test_table`(
  id int not null auto_increment comment '编号',
  name varchar(255) default null comment '名称',
  age int default null comment '年龄',
  gender tinyint unsigned(1) default null comment '性别',
  primary key(id)
) character_set = utf8mb4, collate = utf8mb4_general_ci, comment '用户表';
// 添加表字段
alter table `test_table` add `nickname` varchar(255) default null comment '昵称';
// 修改表字段
alter table `test_table`change `nickname` `username` varchar(30) default null comment '用户名';
alter table `test` modify `gender` tinyint(1) unsigned;
// 删除表字段
alter table `test_table` drop `username`;
// 修改表名
alter table `test_table` rename to `test`;
// 删除指定表
drop table if exists `test`;
// 删除指定表，并重新创建表
truncate table `test`;
```

## DML

操作表数据的增删改

```java
//  给指定字段添加数据
insert into `test`(id, name, age, gender) values(1, 'aaa', 20, 1);
// 给全部字段添加数据
insert into `test` values(1, 'aaa', 20, 1);
// 批量添加数据
insert into `test`(id, name, age, gender) values(3, 'aaa', 20, 1), (4, 'bbb', 20, 0);
// 修改数据按条件
update `test` set name = 'zzz' where id = 3;
// 修改全表数据
update `test` set name = 'zzz';
// 删除数据按条件
delete from `test` where id = 3;
// 删除全表数据
delete from `test`;
// delete 不能删除一个字段(用 update )
```

## DQL

操作表数据的查询

优先级：where > group by > having > order by > limit

where 和 having 的区别

- where 是 分组之前进行过滤，having 是分组之后进行过滤
- where 不能使用聚合函数，having 可以

```java

// 去重查询
select distinct age from `test;
// 查询姓名为两个字的员工信息 (_ 匹配单个字符，% 匹配任意字符)
select * from `test` where name like '__';
// 查询身份证号最后一位是 X 的员工信息
select * from `test` wherer idcard like '%X';

常见聚合函数: (null 值不参与计算) 聚合函数在分组时执行
- count：统计数量
- max：最大值
- min：最小值
- avg：平均值
- sum：求和

分组：group by 分组之后，查询的字段一般为聚合函数和分组字段
// 根据性别分组，统计男性员工 和 女性员工的数量
select gender, count(*) from `emp` group by gender;
// 根据性别分组，统计男性员工 和 女性员工的平均年龄
select gender, avg(age) from `emp` group by gender;
// 查询年龄 <45 的员工，并根据工作地址分组，获取员工数量 >=3 的工作地址
select `address`, count(*) num from `emp` where `age` < 45 group by `address` having num >= 3;

排序：order by 字段1 排序方式1，字段2 排序方式2;
- ASC：升序（默认）
- DESC：降序
多字段排序时，当第一个字段值相同时，才会根据第二个字段进行排序
// 根据年龄对公司的员工进行升序排序
select * from `emp` order by `age`；
// 根据年龄对公司的员工进行降序排序
select * from `emp` order by `age` desc；
// 根据年龄对公司的员工进行升序排序，年龄相同再按照入职时间进行降序排序
select * from `emp` order by `age` asc, `entrydate` desc;

```

综合

```java
// 查询年龄为 20、21、22、23 的员工信息
select * from `emp` where `age` in(20, 21, 22, 23);
// 查询性别为 男，并且年龄在 20-40 (含) 的姓名为 三个字的员工
select * from `emp` where `gender` = '男' and `age` between 20 and  40 and `name` like '___';
// 统计员工表中，年龄小于 60 的男性员工和女性员工的人数
select `gender`, count(*) from `emp` where `age` < 60 group by `gender`;
// 查询所有年龄 <= 35 的员工的姓名和年龄，并对查询结果按年龄升序排序，如果年龄相同按入职时间降序排序
select `name`, `age` from `emp` where `age` <= 35 order by `age` asc, `entrydate` desc; 
// 查询性别为男，且年龄在20-40（含）的前五个员工信息，对查询结果按年龄升序排序，年龄相同按入职时间降序排序
select * from `emp` where `gender` = '男' and `age` between 20 and 40 order by `age` asc, `entrydate` desc limit 5;

```

## DCL

管理 MySQL 用户及其权限

```java
// 创建用户
create user 'admin'@'%' identified with mysql_native_password by 'admin';
// 修改用户密码
alter user 'root'@'localhost' identified with mysql_native_password by 'root';
// 修改用户主机
update user set Host = '%' where User = 'root';
// 删除用户
drop user 'admin'@'%';
// 查询权限
show grants for 'root'@'%';（usage 表示没有权限 all 表示有所有权限）
// 授予权限
grant all on *.* to 'root'@'%';
// 撤销权限
revoke all on *.* to 'root'@'%';

```

## 函数

- 字符串

```java
- concat() 字符串拼接
select concat('Hello', ' MySQL');
- lower() 字符串转小写
select lower('HELLO');
- upper() 字符串转大写
select lower('hello');
- rpad() 右填充至n个
select rpad('01', 5, '-'); --> '01---'
- lpad() 左填充至n个
select lpad('01', 5, '-'); --> '---01'
- trim() 去除头尾的空格
select trim(' Hello', ' MySQL ');
- substring() 截取字符
select substring('Hello MySQL', 1 ,5) 索引从1开始截取5个字符
```

- 数值

```java
- ceil(x) 向上取整
select ceil(1.5);
- floor(x) 向下取整
select floor(1.5);
- mod(x, y) 返回 x/y 的模 (取余)
select mod(3, 4); --> 3
select mod(5, 4); --> 1
- rand() 返回 0 ~ 1 的随机数
select rand();
- round(x, y) 求参数 x 的四舍五入的值，保留 y 位小数
select round(2.345, 2); --> 2.35
- 例：生成一个六位数的随机验证码
select rpad(round(rand() * 100000, 0), 6, '0');
```

- 日期

```java
- curdate() 返回当前日期
select curdate();
- curtime() 返回当前时间
select curtime();
- now() 返回当前日期和时间
select now();
- year(date) 获取指定 date 的年份
select year(now());
- month(date) 获取指定 date 的月份
select month(now());
- day(date) 获取指定 date 的日期
select day(now());
- date_add(date, INTERVAL expr unit ) 对 date 进行追加时间
select date_add(now(),interval 2 day);
- datediff(date1, date2) 返回 date1、date2 之间的天数
select datediff('2022-03-01','2022-03-04'); --> -3
```

- 流程

```java
- if(value, t, f) 如果 value 为 true 则返回 t，否则 f
select if(true, 'OK', 'Error');
- ifnull(value1, value2) 如果 value1 不为空 则返回 value1，否则 返回 value2
select ifnull('OK', 'Default');
- case when [val1] then [res1]...else [default] end 如果 val1 为 true，返回 res1, ...否则返回 default 
- case [expr] when [val1] then [res1]...else [default] end 如果 expr 的值等于 val1，返回 res1, ...否则返回 default 
select `name`, case `address` when '北京' then '一线' when '上海' then '一线' else '二线' end as '工作地址' from `emp`;
```

## 多表查询

多表查询会产生笛卡尔积：集合 A 和集合 B 所有元素的组合。故多表查询要消除笛卡尔积

- 内连接 (两表交集)

```java
- 隐式内连接
// 查询每一个员工的姓名，及其关联的部门名称
select e.name, d.name from `emp` e, `dept` d where e.dept_id = d.id;
// 显式内连接
select e.name, d.name from `emp` e inner join `dept` d on e.dept_id = d.id;
```

- 外连接

```java
- 左外连接 (左表所有数据 + 两表交集)
// 查询 emp 表的所有数据和对应的部门
select e.*, d.name from `emp` e left join `dept` d on e.dept_id = d.id;
- 右外连接 (右表所有数据 + 两表交集)
// 查询 dept 表的所有数据和对应的员工
select e.name, d.* from `emp` e right join `dept` d on e.dept_id = d.id;
```

- 自连接（两张表是同一张表）

```java
- 内连接
// 查询员工及其所属领导的名字
select a.name , b.name from `emp` a, `emp` b where a.id = b.managerid;
- 外连接
// 查询员工及其所属领导的名字，如果没有领导也要查出来
select a.name , b.name from `emp` a left join `emp` b on e.id = b.managerid;
```

- 联合查询 ( 查询的字段必须一致)

```java
- union all (合并多次查询的全部数据)
// 将薪资低于 5000 的员工和年龄大于 50 的员工全部查出来
select * from `emp` where salary < 5000 union all select * from `emp` where age > 50;
- union  (合并多次查询的全部数据并去重)
// 将薪资低于 5000 的员工和年龄大于 50 的员工全部查出来，不重复
select * from `emp` where salary < 5000 union  select * from `emp` where age > 50;
```

- 子查询

```java
- 标量子查询
// 查询销售部的所有员工信息
select * from `emp` where dept_id = (select id from `dept` where name = '销售部');

- 列子查询 (in、not in、any、some、all)
// 查询销售部和市场部的所有员工信息
select * from `emp` where dept_id in (select id from `deop` where name ='销售部' or name ='市场部');
// 查询比财务部所有人工资都高的员工信息
select * from `emp` where salary > all (select salary from `emp` where dept_id = (select id from `dept` where name = '财务部'));
// 查询比研发部任意一人工资高的员工信息
select * from `emp` where salary > any (select salary from `emp` where dept_id = (select id from `dept` where name = '研发部'));

- 行子查询 (=、<>、in、not in)
// 查询与张无忌的薪资及直属领导相同的员工信息
select * from `emp` where (salary, managerid) = (select salary, managerid from `emp` where name = '张无忌');
- 表子查询 (in)
// 查询鹿杖客和宋远桥的职位和薪资相同的员工信息
select * from `emp` where (job, salary) in (select job, salary from `emp` where name = '鹿杖客' or name = '宋远桥');
// 查询入职日期是 2006-01-01 之后的员工信息及其部门信息
select e.*, d.* from (select * from `emp` where entrydate > '2006-01-01') e left join dept d on e.dept_id = d.id;
```

## MySQL 有哪几种数据存储引擎？有什么区别

通过 show engines 指令可以看到所有支持的数据存储引擎。最常用的是 MyISAM 和 InnoDB 两种

MyISAM 和 InnoDB 区别：

- 存储文件。MyISAM 每个表有三个个文件。mdy 表数据文件 、myi 索引文件和 sdi 表结构文件。而 InnoDB 每个表只有一个 idb 文件
- InnoDB 支持事务、支持行级锁、支持外键
- InnoDB 支持 XA 事务 (分布式事务协议) XA START/END/PREPARE/COMMIT
  - XA 事务就是两阶段提交的一种实现方式，定义了事务管理器 TM 和资源管理器 RM 之间的接口
  - prepare 阶段： TM 向所有 RM 发送 prepare 指令，RM 接受指令后执行数据修改和日志记录，返回是否可以提交给 TM
  - commit 阶段：TM 接受所有 RM 的 prepare 结果，如果所有 RM 均返回可以提交，则向所有 RM 发送事务提交命令，如果有 RM 返回不可提交或超时，则向所有 RM 发送事务回滚命令
- InnoDB 支持 Savepoints （提供一种部分回滚的机制)

## 什么是脏读、幻读、不可重复读？要怎么处理

脏读：一个事务读到另一个事务还没有提交的数据

不可重复读：一个事务多次读取同一条记录，读取的结果不同，update 引起

幻读：一个事务在查询数据时，没有对应的数据行，但是在插入数据时，这行数据又存在。insert 引起

处理的方式有很多种：加锁、事务隔离、MVCC

- 加锁
  
  脏读：在修改时加独占锁，直达事务提交才释放。读取时加共享锁，读完释放

  不可重复读：读数据时加共享锁，写数据时加独占锁，行锁

  幻读：加范围锁，表锁

## 事务的基本特性和隔离级别有哪些

事务：表示多个数据操作组成一个完整的事务单元，这个事务内的所有数据操作要么同时成功，要么同时失败

事务的特性：ACID

1、原子性: 事务是不可分割的，要么完全成功，要么完全失败

2、一致性：事务完成时，必须使所有的数据都保持一致状态

3、隔离性：数据库提供的隔离机制，保证事务不受外部并发操作的影响下隔离运行

4、持久性：事务一旦提交或回滚，它对数据库中的数据的改变是永久的

事务的隔离级别

指令 show VARIABLES like 'TRANSACTION%'

set transaction level xxx 设置下次事务的隔离级别
set session transaction level xxx 设置当前会话的事务的隔离级别
set global transaction level xxx 设置全局事务隔离级别

MySQL 中有五种隔离级别

NONE: 不使用事务
READ UNCOMMITED：允许脏读
READ COMMITED：防止脏读，最常用的隔离级别
REPEATABLE READ: 防止脏读和不可重读。MySQL 默认
SERIALIZABLE：事务串行，可以防止脏读、幻读、不可重复读

五种隔离级别，级别越高，事务的安全性更高，但是并发的性能越低

## 为什么 InnoDB 存储引擎选择使用 B+tree 索引结构

B-tree：一个最大度数(一个节点的子节点个数)为 5 的 B-tree，每个节点最多存储 4 个 key，5 个指针

B+ tree：所有的数据都保存在叶子节点；叶子节点形成了一个单向链表；MySQL进行了优化，叶子节点形成了双向链表

- 相对于二叉树搜索数和红黑树，层级更少，搜索效率高
- 对于 B-tree，无论是叶子节点还是非叶子节点，都会存储数据，这样导致一页(16kb)中存储的键值减少，指针跟着减少，树的高度增加，导致性能降低；而且 B+ tree； 中叶子节点形成了双向链表，便于范围搜索和排序
- 对于 Hash 索引，只支持等值匹配，不支持范围搜索和排序

## InnoDB 存储引擎下的索引分类

- 聚集索引：索引结构的叶子节点保存了行数据，必须有且只有一个
- 二级索引：索引结构的叶子节点关联的是对应的主键，可以存在多个

## InnoDB 主键索引的 B+tree 高度为多高呢

假设：一行数据为 1k，一页中可以存储 16 行这样的数据。InnoDB 的指针占用 6 个字节的空间，主键即使为 bigint, 占用字节数为 8

高度为 2 时：n * 8 + (n +1) * 6 = 16 * 1024 算出 n 约为 1170 ，1171 * 16  = 18736；
高度为 3 时：1171 * 1171 * 16  = 21939856；

结论：数据量为几万条以下时，树的高度为 2，数量为几千万以下时，树的高度为 3

## 索引使用

- 语法

```java
- 创建
// 表：tb_user
// name 字段为姓名字段，该字段的值可能会重复，为该字段创建索引
create index idx_user_name on tb_user(name);
// phone 手机号字段的值是非空，且唯一，为该字段创建唯一索引
create unique index idx_user_phone on tb_user(phone);
// 为 profession、age、status 创建联合索引
create index idx_user_pro_age_sta on tb_user(profession, age, status);
- 删除
drop index idx_user_name on tb_user;
- 查看
show index from tb_user;
```

- 性能分析

```java
- 查看 数据库 sql 执行频率
show session/global status like 'com_______'; (7 个 _)

- 开启慢查询
show variables like 'slow%';
set global slow_query_log=ON;
set global slow_launch_time=2;

- profile
// 查看是否支持 profile
select @@have_profiling;
// 查看是否开启 profile
select @@profiling;
// 开启 profile
set global profiling=1;
// 查看分析
show profiles;

- explain 执行计划
id：id 相同，按顺序执行，id 不同，值越大，越先执行
select_type: 表示 select 类型，常见的取值有 simple(简单表，即不使用表连接或子查询)、primary(主查询，即外层的查询)、union(union 中的第二个或者后面的查询语句)、subquery(select/where 之后包含了子查询) 等
type：表示连接类型，性能由好到差的连接类型为 NULL、system、const(唯一索引)、eq_ref(唯一索引)、ref(非唯一索引)、range、index、all
possible_key：显示可能应用在这张表上的索引，一个或多个
key：实际使用的索引，如果为 NULL，则没有使用索引
key_len：索引中使用的字节数，该值为索引字段最大可能长度，并非实际长度，在不损失精确性的 前提下，长度越短越好
rows：MySQL 认为必须要执行查询的行数，是一个估计值，并不总是准确
filtered：返回结果的行数占需读取行数的百分比，值越大越好
extra：type 为 index的情况 ：using index condition (查询使用了索引，但是需要回表)、using where;using index(查询使用了索引，但是需要的数据在索引列中都能找到，无需回表)
```

- 使用规则

1. 最左前缀法则

如果索引了多列（联合索引），要遵守最左前缀法则。即查询从索引最左列开始，并且不跳过索引中的列。如果跳过某一列，后面的字段索引会失效

```java
对于 a、b、c 三个字段的联合索引，查询为 a、ab、abc 走索引，ac 只有 a 走索引，c 失效
```

2. 范围查询

联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效。使用 >=、<= 可以避免

```java
对于 a、b、c 三个字段的联合索引，查询为 a = 1 and b > 1 and c = 1，a，b 走索引，c 失效。a = 1 and c = 1 and b > 1 也是一样。
```

3. 索引列运算
 
不要在索引列上进行运算操作，否则索引失效

```java
select * from tb_user where substring(phone,10,2) = '15';
```

4. 字符串不加引号索引会失效

5. 模糊查询

含有头部模糊匹配会失效

```java
select * from test where name = '%工程%';
select * from test where name = '%工程';
```

6. or 连接的条件

or 的一边查询的列没有索引，另一边也不会走

7. 数据分布的影响

如果 MySQL 评估使用索引比全表扫描更慢，则不使用索引

- SQL 提示

SQL 提示是优化数据库的一个重要手段，即在 SQL 语句中加入一些人为的提示来达到优化操作的目的

```java
use index；推荐使用索引
select * from tb_user use index(idx_user_name) where name = '软件';
ignore index；忽略使用索引
select * from tb_user ignore index(idx_user_name) where name = '软件';
force index；强制使用索引
select * from tb_user force index(idx_user_name) where name = '软件';
```

- 覆盖索引

即查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到

尽量使用覆盖索引，减少 select *;

- 前缀索引

当字段类型为(varchar、text)时，有时候需要索引很长的字符串,会让索引变得很大，查询时会浪费大量磁盘IO。则可以只将一部分前缀作为索引

```java
create index id_email_5 on tb_user(email(5));
```

前缀长度的选择：

选择性=不重复的索引值/记录总数

```java
select count(distinct substring(email, 1, 5))/ count(*) from tb_user;
```

选择性越高越好，唯一索引的选择性是1，也是最好的选择性

## 索引设计原则

1. 针对于数据量较大(百万以上)，且查询比较频繁的表建立索引
2. 针对常作为查询条件(where)、排序(order by)、分组(group by) 操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段的长度较长，建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表
6. 要控制索引的数量，太多会影响增删改的效率
7. 如果索引列不能存储 NULL 值，在建表时使用 NOT NULL 来约束

## SQL 优化

- 主键设计原则

1. 满足业务需求的情况下，尽量降低主键的长度，过长会影响索引的效率
2. 插入数据时，尽量选择顺序插入，选择使用自增主键(无序插入可能会发生页分裂)
3. 尽量不要使用 UUID 做主键或其他自然主键，如身份证号
4. 避免对主键修改

- order by 优化

Using filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区 sort buffer 中完成排序操作，所有不是通过索引直接返回排序结果的排序的都是 filesort

Using index：通过有序索引顺序扫描直接返回有序数据，不需要额外排序，操作效率高

```java
create index id_user_age_po_ad on tb_user(age asc, phone desc);
select * from tb_user where age asc and phone desc;
```

1. 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
2. 尽量使用覆盖索引
3. 多字段排序时，一个升序一个降序，此时需要注意联合索引在创建时的规则(ASC/DESC)
4. 如果不可避免的出现 filesort，大数据量排序时，可以适当增大排序缓冲区的大小 sort_buffer_size(default 256k)

- group by 优化

Using temporary：临时表

1. 在分组时，通过索引可以提升效率
2. 分组时，索引的使用也满足最左前缀法则