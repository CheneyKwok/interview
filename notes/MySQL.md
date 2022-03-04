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