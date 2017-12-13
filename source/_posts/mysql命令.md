---
title: mysql命令
date: 2017-10-10 11:49:45
tags:
    -mysql命令
categories:
    -mysql
---
### 权限操作

1.创建用户，使之管理数据库
```bash
grant all (privileges) on *.* to root@localhost identified by ‘123456’;
```
说明:
* grant 与 on 之间是各种权限，例如:insert,select,update 等
* on 之后是数据库名和表名,第一个表示所有的数据库，第二个表示所有的表
* root 可以改成你的用户名，@后可以跟域名或 IP 地址，identified by 后面的是登录用的密码，可以省略，即缺省密码或者叫空密码

2.查看已创建的用户有哪些权限
```bash
show grants for suixin@'localhost'\G
```
3.更改用户密码
```bash
mysqladmin –u 用户名 –p 旧密码 password 新密码
```
4.删除用户
```bash
deleted from user where user=”root” and host=’localhost’;
```
5.查看数据库有哪些用户
```bash
select user,host from mysql.user;
```
6.创建一个用户 suixin 在特定客户端 it363.com 登录，可访问特定数据库fangchandb
```bash
grant select, insert, update, delete, create,drop on fangchandb.* to suixin@it363.com identified by ' passwd'
```
### mysql 系统命令
1.显示当前用户
```bash
select user();
```
2.显示当前日期
```bash
select now();
```
3.查看数据库版本
```bash
select version();
```
4.查看 mysql 数据库字符集
```bash
show variables like '%char%';
```
5.设置默认字符集为 utf8
```bash
在配置文件找到[mysqld] 添加 default-character-set=utf8
```
6.找回mysql密码（共四步）
```bash
./etc/init.d/mysqld stop 
mysqld_safe (‐‐defaults‐file=/data/3306/my.cnf) ‐‐skip‐grant‐tables &>/dev/null  &
说明:如果是多实例要指定配置文件
ps ‐ef |grep 3306  可以查看到忽略授权表的进程
mysql update mysql.user set password=PASSWORD('oldboy') where user='root' and host='localhost';
./etc/init.d/mysqld start
```

### 库操作

1.显示数据库
```bash
show databases;
```
2.创建数据库
```bash
create database name;
```
3.选择数据库
```bash
use databasename;
```

4.查看当前使用的数据库

```bash
select database();
```
5.删除数据库前，有提示
```bash
mysqladmin drop databasename;
```
6.直接删除数据库，不提醒
```bash
drop database databasename;
```
7.查看数据库版本和当前日期
```bash
select version(),current_date;
```
8.查看已建库的完整语句
```bash
show create database databasename;
```
9.修改数据库字符集
```bash
alter database name character set utf8;
```
10.创建 GBK 字符集的数据库
```bash
create database databasename character set gbk collate gbk_chinese_ci;
```
### 表操作

1.创建一 innodb 引擎字符集为 utf8 表 suixin
```bash
create table suixin(
id int(4) not null,
name varchar(16) not null
) ENGINE=innodb default charset=utf8;
```
2.插入数据
```bash
insert into suixin values(1,'Tom',),(2,'Joan');
```
3.查看库中的表
```bash
show tables;
```
4.查看表中所有数据
```bash
select * from suixin;
```
5.查看表 suixin 中前两行数据
```bash
select * from suixin limit 0,2;
```
6.显示具体的表结构，查看字段
```bash
desc(describe) tablename;
或
show columns from test;
```
7.重命名表
```bash
alter table t1 rename t2;
```
8.删除表
```bash
drop table tablename;
```
9.删除表中数据
```bash
delete 是逻辑删除表中的数据，一列一列的删除表中数据，速度比较慢
mysql> delete from suixin;
truncate 是物理删除表中的数据，一次性全部都给清空表中数据，速度很快
mysql> truncate table suixin;
```
10.删除表 suixin 中编号为 1 的记录
```bash
delete from suixin where id=1;
```
11.显示建表语句
```bash
show create table table_name;
```
12.更改表类型
```bash
alter table table_name engine innodb|myisam|memory;
```
13.显示详细信息，包括字符集编码
```bash
show full columns from tablename;
```
14．查看表 suixin 的字符集
```bash
show table status from databasename like '%suixin%'\G;
```
15.改变表的编码
```bash
Alter TABLE suixin CHARACTER SET gb2312;
或者
alter table 表名 convert to character set gbk; 修改表的字符集
```
16.修改表中数据
```bash
update suixin set name=’xiaoluo’where id=1;
```
17.把 id 列设置为主键
```bash
alter table suixin add primary key(id);
```
18.name字段上创建普通索引
```bash
方法一：
alter table robin.suixin add index index_name(name);
方法二：
create index index_name on robin.suixin(name);
```
19.在表 suixin 的字段 name 后插入手机号字段(shouji)，类型 char(11)
```bash
alter table robin.suixin add shouji char(11) after name;
```
22.在手机字段上对前 8 个字符创建普通索引。
```bash
alter table robin.suixin add index index_shouji(shouji(8));
```
23.查看创建的索引及索引类型等信息
```bash
show index from robin.suixin\G
```
24.删除 Name，shouji 列的索引。
```bash
alter table robin.suixin drop index index_name;
drop index index_shouji on oldboy.test;
```
25.对 Name 列的前 6 个字符以及手机列的前 8 个字符组建联合索引。
```bash
alter table robin.suixin add index index_name_shouji(name(6),shouji(8));
```
26.查询手机号以 135 开头的，名字为 oldboy 的记录（此记录要提前插入）。
```bash
select * from robin.suixin where name='oldboy' and shouji like '135%';
```
27.把 test 表的引擎改成 MyISAM。

MySQL 数据库 5.1 版本以前默认的引擎是 MyISAM

MySQL 数据库 5.5 版本以后默认的引擎都是 InnoDB

```bash
#首先现在查看一下 test 表的引擎
show create table robin.suixin\G
#修改默认引擎为 MyISAM
alter table robin.suixin ENGINE=MyISAM;
```
**扩展**

字段类型

1.INT[(M)] 型： 正常大小整数类型

2.DOUBLE[(M,D)] [ZEROFILL] 型： 正常大小(双精密)浮点数字类型

3.DATE 日期类型：支持的范围是 1000-01-01 到 9999-12-31。MySQL 以 YYYY-MM-DD格式来显示 DATE 值，但是允许你使用字符串或数字把值赋给 DATE 列

4.CHAR(M) 型：定长字符串类型，当存储时，总是是用空格填满右边到指定的长度

5.BLOB TEXT 类型，最大长度为 65535(2^16-1)个字符。

6.VARCHAR 型：变长字符串类型

### 备份数据库

mysqldump客户端可用来备份数据库

如果你在服务器上进行备份，并且表均为 MyISAM 表，应考虑使用 mysqlhotcopy，因为可以更快地进行备份和恢复。

有 3 种方式来调用 mysqldump：
```bash
shell> mysqldump [options] db_name [tables]
shell> mysqldump [options] ---database DB1 [DB2 DB3...]
shell> mysqldump [options] --all--database
```
如果没有指定任何表或使用了\-\-database 或\-\-all\-\-database 选项，则备份整个数据库。

**mysqldump 部分参数**：

\-\-add-drop\-\-database

    在每个 CREATE DATABASE 语句前添加 DROP DATABASE 语句。

\-\-all\-\-database，-A

    备份所有数据库中的所有表。与使用---database 选项相同，在命令行中命名所有数据库。

\-\-database，-B

    转储几个数据库。通常情况，mysqldump 将命令行中的第 1 个名字参量看作数据库名，后面的名看作表名。使用该项，它将所有名字参量看作数据库名。CREATE DATABASE IF NOT EXISTS db_name 和 USE db_name 语句包含在每个新数据库前的输出中。

\-\-flush-logs，-F

    刷新 binlog 日志

\-\-force，-f

    在表转储过程中，即使出现 SQL 错误也继续。

\-\-lock-all-tables，-x

    所有数据库中的所有表加锁。在整体备份过程中通过全局读锁定来实现。

\-\-master-data

    该选项将二进制日志的位置和文件名写入到输出中。必须启用二进制日志。如果该选项值等于 1，位置和文件名被写入 CHANGE MASTER 语句形式的转储输出，如果你使用该 SQL转储主服务器以设置从服务器，从服务器从主服务器二进制日志的正确位置开始。如果选项值等于 2，CHANGE MASTER 语句被写成 SQL 注释。如果 value 被省略，这是默认动作。

\-\-no-data，-d

    备份表结构

-t

    只导出数据

\-\-tables

    覆盖---database 或-B 选项。选项后面的所有参量被看作表名。

实际场景：
```bash
mysqldump –uroot –p123456 –c –R --master-data=2 –-add-drop-database –-single-transaction -–all-databases |gzip –qc > /tmp/all.zip
```
例子：

a.导出整个数据库 mysqldump -u 用户名 -p 数据库名 > 导出的文件名
```bash
[root@linux mysql]# mysqldump -u dbadmin -p myblog > /tmp/myblog.sql
```
b.导出一个表 mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
```bash
[root@linux mysql]# mysqldump -u dbadmin -p myblog wp_users> /tmp/blog_users.sql
```
c.导出一个数据库结构 -d 没有数据 --add-drop-table 在每个 create 语句之前增加一个 drop table
```bash
[root@linux mysql]# mysqldump -u dbadmin -p -d --add-drop-table myblog > /tmp/blog_struc.sql
```
d.导出数据库一个表结构
```bash
[root@linux mysql]# mysqldump -u dbadmin -p -d --add-drop-table myblog  wp_users> /home/zhangy/blog/database_bak/blog_users_struc.sql
```
e.导出数据库一个表数据,不包括表结构
```bash
[root@linux mysql]# mysqldump -t test order -u root -p > order.sql;
```