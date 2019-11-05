# 01_MySql基础及增删改查

## 1.数据库介绍

概念：数据库，顾名思义就是能够存储和管理“大量数据”的一种软件系统的统称

### 1.1主流数据库

主流数据库包括mysql，sql server，oracle，DB2，informix，sybase等

这些都是被称为“关系数据库“的一种遵循sql标准的软件产品



### 1.2mysql数据库概览

mysql基本结构如下

![](C:\Users\宋天\Desktop\mysql\img\mysql基本结构.png)

其中，实际表的数据和结构如下所示

![](C:\Users\宋天\Desktop\mysql\img\数据表结构.png)

名词解释：

- DBMS：数据库管理系统
- DB，DataBase：数据库，一个数据库系统中可以存放很多个数据库
- table：表，一个数据库中可以存放多个表
- row：行，指一行数据，一个表中可以有很多行
- record：记录，也是指一行数据
- column：列，指一列数据，一个表中可以有若干列
- field：字段，值数据表中的一列的名称，类似表头，一个表可以有若干字段名

### 1.3关系数据库

概念：

- 关系数据库时指基于关系模型而设计的数据库系统

- 所谓关系，其实就是指一个二维表（table）

- 一行有多个数据，就表示这多个数据是具有内在关系的（同属于一个“实体”）

- 比如：上述的两个表，就可以用“E-R”图（实体-关系图）表示

  ![](C:\Users\宋天\Desktop\mysql\img\E-R图1.png)

  ​		

  ![](C:\Users\宋天\Desktop\mysql\img\E-R图2.png)

## 2.访问mysql数据库服务器

### 2.1开启/关闭数据库服务器

1. 通过系统服务
2. 通过命令行
   - net start mysql 开启数据库服务
   - net stop mysql 关闭数据库服务

### 2.2命令行连接数据库

1. 进入cmd命令界面

   ```
   mysql -uroot -p+密码
   ```

   注：p后面直接加密码即可

2. 设置连接数据库后使用的字符集

   ```
   set names gbk;
   ```

   注：可以更改为自己需要的字符集

3. 退出数据库库连接

   ```
   quit;
   exit;
   \q;
   ```

   以上三选一



## 3.数据库操作

1. 查看所有数据库

   ```sql
   mysql> show databases;
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | performance_schema |
   | phpsql             |
   | sys                |
   | test               |
   +--------------------+
   6 rows in set (0.00 sec)
   ```

2. 创建新数据库

   格式：`create database 数据库名 [charset 字符集名称] [collate 校对规则明];`

   - 字符集名有：utf8，gbk，gb2312，big5，ascii等，推荐utf8

   - 校对规则名：通常不用写，而是使用所设定字符集的默认校对规则
     - 含义：每个字符集中的每个字符的“排序规则“
   - 查看可用字符集`show charset;`
   - 查看可用校对规则 `show collation;`

   ```sql
   mysql> create database phptest charset utf8;
   Query OK, 1 row affected, 1 warning (0.01 sec)
   ```

   

3. 查看数据库创建信息

   格式：`show create database 数据库名;`

   结果其实就是能看到指定数据的完整创建语句（含默认值的选项，比如charset， collate）。

   ```
   mysql> show create database phptest;
   +----------+-------------------------------------------------------------+
   | Database | Create Database                                                                                     |
   +----------+-------------------------------------------------------------+
   | phptest  | CREATE DATABASE `phptest` /*!40100 DEFAULT CHARACTER SET utf8 */ /*!80016 DEFAULT ENCRYPTION='N' */ |
   +----------+-------------------------------------------------------------+
   1 row in set (0.00 sec)
   ```

4. 删除现有数据库

   格式：`drop database 数据库名;`

   ```sql
   mysql> drop database test;
   Query OK, 7 rows affected (0.08 sec)
   ```

5. 修改现有数据库

   修改数据库，其实只是修改数据库的字符编码或校对规则，其实一般不需要修改

   格式：`alter database 数据库名 charset 新的字符集名称 collate 新的校对规则名;`

   ```sql
   mysql> alter database phptest charset gbk;
   Query OK, 1 row affected (0.01 sec)
   ```

6. 选择（使用）某个数据库

   格式：`use 数据库名;`

   ```sql
   mysql> use phptest;
   Database changed
   ```

## 4.数据表操作

“数据库“只是一个外壳，除了有个数据库名称和字符集设定，基本上就没有别的信息了

而数据表才是用来存储数据的载体

1. 创建数据表（初步）

   格式：`create table 数据表名(字段1，字段2，字段3，。。。)[charset=字符集] [engine=表类型];`

   - 字段的形式为：字段名 字段类型 [字段属性]
   - 字符集包括：utf,gbk等，默认是数据库的字符集，可以不写
   - 表类型包括：InnoDB,  MyIsam，BDB，等，默认是InnoDB，可以不写。

   ```sql
   mysql> create table test(id int,name varchar(10),sex char(1),age int);
   Query OK, 0 rows affected (0.03 sec)
   ```

2. 查看所有数据表

   格式：`show tables;`

   ```sql
   mysql> show tables;
   +-------------------+
   | Tables_in_phptest |
   +-------------------+
   | test              |
   +-------------------+
   1 row in set (0.00 sec)
   ```

3. 查看数据表结构

   格式：`desc 表名;`

   概念：数据表结构，其实就是一个表的每一个字段的具体信息

   ```sql
   mysql> desc test;
   +-------+-------------+------+-----+---------+-------+
   | Field | Type        | Null | Key | Default | Extra |
   +-------+-------------+------+-----+---------+-------+
   | id    | int(11)     | YES  |     | NULL    |       |
   | name  | varchar(10) | YES  |     | NULL    |       |
   | sex   | char(1)     | YES  |     | NULL    |       |
   | age   | int(11)     | YES  |     | NULL    |       |
   +-------+-------------+------+-----+---------+-------+
   4 rows in set (0.00 sec)
   
   ```

4. 查看数据表的创建

   格式：`show create table 表名;`

   ```
   mysql> show create table test;
   +-------+-------------------------------+
   | Table | Create Table                   |
   +-------+--------------------------------+
   | test  | CREATE TABLE `test` (
     `id` int(11) DEFAULT NULL,
     `name` varchar(10) DEFAULT NULL,
     `sex` char(1) DEFAULT NULL,
     `age` int(11) DEFAULT NULL
   ) ENGINE=InnoDB DEFAULT CHARSET=gbk |
   +-------+-----------------------------------+
   1 row in set (0.00 sec)
   ```

5. 删除数据表

   格式：`drop table 表名;`

   ```
   mysql> drop table test01;
   Query OK, 0 rows affected (0.02 sec)
   ```

   

## 5.修改数据表

修改数据表主要是修改表名，添加字段，修改字段，删除字段，修改表的字符集

1. 添加字段

   格式：`alter table 表名 add 字段名 字段类型 [字段属性。。] [after 某个字段名 或first];`

   - after 某字段名：新添加的字段放在现有字段的后面
   - first：表示新加的字段放在第一位

   ```sql
   mysql> alter table test add salary float;
   Query OK, 0 rows affected (0.02 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> desc test;
   +--------+-------------+------+-----+---------+-------+
   | Field  | Type        | Null | Key | Default | Extra |
   +--------+-------------+------+-----+---------+-------+
   | id     | int(11)     | YES  |     | NULL    |       |
   | name   | varchar(10) | YES  |     | NULL    |       |
   | sex    | char(1)     | YES  |     | NULL    |       |
   | age    | int(11)     | YES  |     | NULL    |       |
   | salary | float       | YES  |     | NULL    |       |
   +--------+-------------+------+-----+---------+-------+
   5 rows in set (0.00 sec)
   ```

   ```sql
   mysql> alter table test add edu varchar(10) after age;
   Query OK, 0 rows affected (0.04 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> desc test;
   +--------+-------------+------+-----+---------+-------+
   | Field  | Type        | Null | Key | Default | Extra |
   +--------+-------------+------+-----+---------+-------+
   | id     | int(11)     | YES  |     | NULL    |       |
   | name   | varchar(10) | YES  |     | NULL    |       |
   | sex    | char(1)     | YES  |     | NULL    |       |
   | age    | int(11)     | YES  |     | NULL    |       |
   | edu    | varchar(10) | YES  |     | NULL    |       |
   | salary | float       | YES  |     | NULL    |       |
   +--------+-------------+------+-----+---------+-------+
   6 rows in set (0.00 sec)
   
   ```

2. 修改字段

   格式：`alter table 表名 change 旧字段名 新字段名 字段类型 [字段属性。。。];`

   如果不修改字段名，只修改字段的其他信息，则可以使用：

   格式：`alter table 表名 modify 字段名 要修改的字段名 字段类型 [字段属性。。。];`

   ```sql
   mysql> alter table test change sex aaa varchar(4);
   Query OK, 0 rows affected (0.05 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> desc test;
   +--------+-------------+------+-----+---------+-------+
   | Field  | Type        | Null | Key | Default | Extra |
   +--------+-------------+------+-----+---------+-------+
   | id     | int(11)     | YES  |     | NULL    |       |
   | name   | varchar(10) | YES  |     | NULL    |       |
   | aaa    | varchar(4)  | YES  |     | NULL    |       |
   | age    | int(11)     | YES  |     | NULL    |       |
   | edu    | varchar(10) | YES  |     | NULL    |       |
   | salary | float       | YES  |     | NULL    |       |
   +--------+-------------+------+-----+---------+-------+
   6 rows in set (0.00 sec)
   ```

   ```sql
   mysql> alter table test modify age int(3);
   Query OK, 0 rows affected (0.01 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> desc test;
   +--------+-------------+------+-----+---------+-------+
   | Field  | Type        | Null | Key | Default | Extra |
   +--------+-------------+------+-----+---------+-------+
   | id     | int(11)     | YES  |     | NULL    |       |
   | name   | varchar(10) | YES  |     | NULL    |       |
   | aaa    | varchar(4)  | YES  |     | NULL    |       |
   | age    | int(3)      | YES  |     | NULL    |       |
   | edu    | varchar(10) | YES  |     | NULL    |       |
   | salary | float       | YES  |     | NULL    |       |
   +--------+-------------+------+-----+---------+-------+
   6 rows in set (0.00 sec)
   ```

3. 删除字段

   格式：`alter table 表名 drop 要删除的字段名;`

   ```sql
   mysql> alter table test drop aaa;
   Query OK, 0 rows affected (0.05 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> desc test;
   +--------+-------------+------+-----+---------+-------+
   | Field  | Type        | Null | Key | Default | Extra |
   +--------+-------------+------+-----+---------+-------+
   | id     | int(11)     | YES  |     | NULL    |       |
   | name   | varchar(10) | YES  |     | NULL    |       |
   | age    | int(3)      | YES  |     | NULL    |       |
   | edu    | varchar(10) | YES  |     | NULL    |       |
   | salary | float       | YES  |     | NULL    |       |
   +--------+-------------+------+-----+---------+-------+
   5 rows in set (0.00 sec)
   
   ```

4. 修改表名

   格式：`alter table 表名 rename 新的表名;`

   ```sql
   mysql> alter table test rename test1;
   Query OK, 0 rows affected (0.02 sec)
   
   mysql> show tables;
   +-------------------+
   | Tables_in_phptest |
   +-------------------+
   | test1             |
   +-------------------+
   1 row in set (0.00 sec)
   
   ```

5. 修改字符集

   格式：`alter table 表名 charset=新的字符集`

   ```sql
   mysql> alter table test1 charset=gbk;
   Query OK, 0 rows affected (0.02 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> show create table test1;
   +-------+------------------------------------------------------+
   | Table | Create Table             |
   +-------+-------------------------------------------------------+
   | test1 | CREATE TABLE `test1` (
     `id` int(11) DEFAULT NULL,
     `name` varchar(10) DEFAULT NULL,
     `age` int(3) DEFAULT NULL,
     `edu` varchar(10) DEFAULT NULL,
     `salary` float DEFAULT NULL
   ) ENGINE=InnoDB DEFAULT CHARSET=gbk |
   +-------+--------------------------------------------------------+
   1 row in set (0.00 sec)
   
   ```

## 6.数据的增删改查

1. 插入数据

   格式：`insert into 表名 (字段名1,字段名2,...) values(数据1,数据2,...);`

   - 字段名和数据都是一一对应的，包括：数量一致，顺序一致，类型匹配。

   - 对于要写入的数据，字符串和时间日期类型，要用单引号引起来

   - 可以省略"字段列表"部分，此时就需要给出跟字段数量一样多的数据，类似：

     `insert into 表名 values(数据1,数据2,。。。);`

   ```sql
   mysql> insert into test1(id,name,age,edu,salary) values(1,'song',21,'复旦',5.2);
   Query OK, 1 row affected (0.01 sec)
   mysql> select * from test1;
   +------+------+------+------+--------+
   | id   | name | age  | edu  | salary |
   +------+------+------+------+--------+
   |    1 | song |   21 | 复旦 |    5.2 |
   +------+------+------+------+--------+
   1 row in set (0.00 sec)
   ```

   ```sql
   mysql> insert into test1 values(2,'song2',22,'复旦',55.6);
   Query OK, 1 row affected (0.01 sec)
   
   mysql> select * from test1;
   +------+-------+------+------+--------+
   | id   | name  | age  | edu  | salary |
   +------+-------+------+------+--------+
   |    1 | song  |   21 | 复旦 |    5.2 |
   |    2 | song2 |   22 | 复旦 |   55.6 |
   +------+-------+------+------+--------+
   2 rows in set (0.00 sec)
   
   ```

2. 查询数据

   格式：`select 字段名1,字段名2,.... from 表名 [where 条件]`

   - select后的字段名用于设定要从表中去除那些字段的值
   - select后可以只使用UI个*来表示取出所有该表中所有字段的值
   - where条件表示取出的数据应该满足的条件，比如
     - where id < 10 表示取出字段id小于10的行
   - where可以不写，则表示取出所有数据

   ```sql
   mysql> select id,name,age from test1 where id <2;
   +------+------+------+
   | id   | name | age  |
   +------+------+------+
   |    1 | song |   21 |
   +------+------+------+
   1 row in set (0.00 sec)
   ```

3. 删除数据

   格式：`delete from 表名 [where 条件]`

   - 删除数据指的是删除表中的某些行
   - where条件表示删除数据应满足的条件，条件和selet中的一样
   - where条件可以不写，如果不写，则表示删除所有的数据

   ```sql
   mysql> delete from test1 where id >1;
   Query OK, 1 row affected (0.01 sec)
   
   mysql> select * from test1;
   +------+------+------+------+--------+
   | id   | name | age  | edu  | salary |
   +------+------+------+------+--------+
   |    1 | song |   21 | 复旦 |    5.2 |
   +------+------+------+------+--------+
   1 row in set (0.00 sec)
   ```

4. 修改数据

   格式：`update 表名 set 字段名1=新值,字段名2=新值2,... [where 条件]`

   - 修改数据值的是修改表哦的某些行的某些字段
   - where条件表示修改数据应该满足的条件，含义和select中一样
   - where条件可以不写，如果不写，则会修改所有数据

   ```sql
   mysql> update test1 set edu='北大',salary=63.5 where id =1;
   Query OK, 1 row affected (0.01 sec)
   Rows matched: 1  Changed: 1  Warnings: 0
   
   mysql> select * from test1;
   +------+------+------+------+--------+
   | id   | name | age  | edu  | salary |
   +------+------+------+------+--------+
   |    1 | song |   21 | 北大 |   63.5 |
   +------+------+------+------+--------+
   1 row in set (0.00 sec)
   ```

   