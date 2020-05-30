---
title: 13_MySql面试题
date: 2019-01-14 18:26:20
tags:
categories: Java面试
---
#一、*面试点*

MySQL高性能索引

SQL语句

MySQL查询优化

MySQL高扩展高可用

MySQL安全性



####问题1：char、varchar的区别是什么？

varchar是变长而char的长度是固定的。如果你的内容是固定大小的，你会得到更好的性能。

####问题2: TRUNCATE和DELETE的区别是什么？

DELETE命令从一个表中删除某一行，或多行，TRUNCATE命令永久地从表中删除每一行。

####问题3：什么是触发器，MySQL中都有哪些触发器？

触发器是指一段代码，当触发某个事件时，自动执行这些代码。在MySQL数据库中有如下六种触发器：

1、Before Insert

2、After Insert

3、Before Update

4、After Update

5、Before Delete

6、After Delete

####问题4：FLOAT和DOUBLE的区别是什么？

FLOAT类型数据可以存储至多8位十进制数，并在内存中占4字节。

DOUBLE类型数据可以存储至多18位十进制数，并在内存中占8字节。

####问题5：如何在MySQL种获取当前日期？

SELECT CURRENT_DATE();
####问题6：如何查询第n高的工资？

SELECT DISTINCT(salary) from employee ORDER BY salary DESC LIMIT n-1,1
####问题7：请写出下面MySQL数据类型表达的意义（int(0)、char(16)、varchar(16)、datetime、text）

知识点分析
此题考察的是MySQL数据类型。MySQL数据类型属于MySQL数据库基础，由此延伸出的知识点还包括如下内容：

MySQL基础操作

MySQL存储引擎

MySQL锁机制

MySQL事务处理、存储过程、触发器

数据类型考点：

1、整数类型，包括TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT，分别表示1字节、2字节、3字节、4字节、8字节整数。
任何整数类型都可以加上UNSIGNED属性，表示数据是无符号的，即非负整数。 长度：整数类型可以被指定长度，
例如：INT(11)表示长度为11的INT类型。长度在大多数场景是没有意义的，它不会限制值的合法范围，只会影响显示字符的个数，
而且需要和UNSIGNED ZEROFILL属性配合使用才有意义。例子，假定类型设定为INT(5)，属性为UNSIGNED ZEROFILL，如果用户插入的数据为12的话，
那么数据库实际存储数据为00012。

2、实数类型，包括FLOAT、DOUBLE、DECIMAL。DECIMAL可以用于存储比BIGINT还大的整型，能存储精确的小数。而FLOAT和DOUBLE是有取值范围的，
并支持使用标准的浮点进行近似计算。计算时FLOAT和DOUBLE相比DECIMAL效率更高一些，DECIMAL你可以理解成是用字符串进行处理。

3、字符串类型，包括VARCHAR、CHAR、TEXT、BLOBVARCHAR用于存储可变长字符串，它比定长类型更节省空间。VARCHAR使用额外1或2个字节存储字符串长度
。列长度小于255字节时，使用1字节表示，否则使用2字节表示。VARCHAR存储的内容超出设置的长度时，内容会被截断。CHAR是定长的，
根据定义的字符串长度分配足够的空间。CHAR会根据需要使用空格进行填充方便比较。CHAR适合存储很短的字符串，或者所有值都接近同一个长度。
CHAR存储的内容超出设置的长度时，内容同样会被截断。

使用策略：对于经常变更的数据来说，CHAR比VARCHAR更好，因为CHAR不容易产生碎片。对于非常短的列，CHAR比VARCHAR在存储空间上更有效率。
使用时要注意只分配需要的空间，更长的列排序时会消耗更多内存。尽量避免使用TEXT/BLOB类型，查询时会使用临时表，导致严重的性能开销。

4、枚举类型（ENUM），把不重复的数据存储为一个预定义的集合。有时可以使用ENUM代替常用的字符串类型。ENUM存储非常紧凑，
会把列表值压缩到一个或两个字节。ENUM在内部存储时，其实存的是整数。尽量避免使用数字作为ENUM枚举的常量，因为容易混乱。排序是按照内部存储的整数

5、日期和时间类型，尽量使用timestamp，空间效率高于datetime，用整数保存时间戳通常不方便处理。如果需要存储微妙，
可以使用bigint存储。看到这里，这道真题是不是就比较容易回答了。

答：int(0)表示数据是INT类型，长度是0、char(16)表示固定长度字符串，长度为16、varchar(16)表示可变长度字符串，
长度为16、datetime表示时间类型、text表示字符串类型，能存储大字符串，最多存储65535字节数据）


​	

MySQL基础操作：
常见操作

MySQL的连接和关闭：mysql -u -p -h -P
-u：指定用户名-p：指定密码-h：主机-P：端口

进入MySQL命令行后：G、c、q、s、h、d
G：打印结果垂直显示c：取消当前MySQL命令q：退出MySQL连接s：显示服务器状态h：帮助信息d：改变执行符

MySQL存储引擎：
1、InnoDB存储引擎

默认事务型引擎，最重要最广泛的存储引擎，性能非常优秀。

数据存储在共享表空间，可以通过配置分开。也就是多个表和索引都存储在一个表空间中，可以通过配置文件改变此配置。

对主键查询的性能高于其他类型的存储引擎。

内部做了很多优化，从磁盘读取数据时会自动构建hash索引，插入数据时自动构建插入缓冲区。

通过一些机制和工具支持真正的热备份。

支持崩溃后的安全恢复。

支持行级锁。

支持外键。

2、MyISAM存储引擎

拥有全文索引、压缩、空间函数。

不支持事务和行级锁、不支持崩溃后的安全恢复。

表存储在两个文件，MYD和MYI。

设计简单，某些场景下性能很好，例如获取整个表有多少条数据，性能很高。

全文索引不是很常用，不如使用外部的ElasticSearch或Lucene。

3、其他表引擎Archive、Blackhole、CSV、Memory

使用策略在大多数场景下建议使用InnoDB存储引擎。

MySQL锁机制
表锁是日常开发中的常见问题，因此也是面试当中最常见的考察点，当多个查询同一时刻进行数据修改时，就会产生并发控制的问题。共享锁和排他锁，
就是读锁和写锁。

共享锁，不堵塞，多个用户可以同时读一个资源，互不干扰。

排他锁，一个写锁会阻塞其他的读锁和写锁，这样可以只允许一个用户进行写入，防止其他用户读取正在写入的资源。

锁的粒度
表锁，系统开销最小，会锁定整张表，MyIsam使用表锁。

行锁，最大程度的支持并发处理，但是也带来了最大的锁开销，InnoDB使用行锁。

MySQL事务处理
MySQL提供事务处理的表引擎，也就是InnoDB。

服务器层不管理事务，由下层的引擎实现，所以同一个事务中，使用多种引擎是不靠谱的。

需要注意，在非事务表上执行事务操作，MySQL不会发出提醒，也不会报错。

MySQL存储过程
为以后的使用保存的一条或多条MySQL语句的集合，因此也可以在存储过程中加入业务逻辑和流程。

可以在存储过程中创建表，更新数据，删除数据等等。

使用策略

可以通过把SQL语句封装在容易使用的单元中，简化复杂的操作

可以保证数据的一致性

可以简化对变动的管理

MySQL触发器
提供给程序员和数据分析员来保证数据完整性的一种方法，它是与表事件相关的特殊的存储过程。使用场景

可以通过数据库中的相关表实现级联更改。

实时监控某张表中的某个字段的更改而需要做出相应的处理。

例如可以生成某些业务的编号。

注意不要滥用，否则会造成数据库及应用程序的维护困难。

大家需要牢记以上基础知识点，重点是理解数据类型CHAR和VARCHAR的差异，表存储引擎InnoDB和MyISAM的区别。

####问题8：请说明InnoDB和MyISAM的区别

InnoDB支持事务，MyISAM不支持；

InnoDB数据存储在共享表空间，MyISAM数据存储在文件中；

InnoDB支持行级锁，MyISAM只支持表锁；

InnoDB支持崩溃后的恢复，MyISAM不支持；

InnoDB支持外键，MyISAM不支持；

InnoDB不支持全文索引，MyISAM支持全文索引；

####问题9：innodb引擎的特性

插入缓冲（insert buffer)

二次写(double write)

自适应哈希索引(ahi)

预读(read ahead)

####问题10：请列举3个以上表引擎

InnoDB、MyISAM、Memory

####问题11：请说明varchar和text的区别

varchar可指定字符数，text不能指定，内部存储varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，text是实际字符数+2个字节。

text类型不能有默认值。

varchar可直接创建索引，text创建索引要指定前多少个字符。varchar查询速度快于text,在都创建索引的情况下，text的索引几乎不起作用。

查询text需要创建临时表。

####问题12：varchar(50)中50的含义

最多存放50个字符，varchar(50)和(200)存储hello所占空间一样，但后者在排序时会消耗更多内存，因为order by col采用fixed_length
计算col长度(memory引擎也一样)。

####问题13：int(20)中20的含义

是指显示字符的长度，不影响内部存储，只是当定义了ZEROFILL时，前面补多少个 0

####问题14：简单描述MySQL中，索引，主键，唯一索引，联合索引的区别，对数据库的性能有什么影响？

知识点分析
此真题主要考察的是MySQL索引的基础和类型，由此延伸出的知识点还包括如下内容：

MySQL索引的创建原则

MySQL索引的注意事项

MySQL索引的原理

下面我们就来将这些知识一网打尽
索引的基础

索引类似于书籍的目录，要想找到一本数的某个特定主题，需要先查找书的目录，定位对应的页码

存储引擎使用类似的方式进行数据查询，先去索引当中找到对应的值，然后根据匹配的索引找到对应的数据行。

创建索引的语法：

首先创建一个表：create table t1 (id int primary key,username varchar(20),password varchar(20));

创建单个索引的语法：CREATE INDEX 索引名 on 表名（字段名）

索引名一般是：表名_字段名

给id创建索引：CREATE INDEX t1_id on t1(id);

创建联合索引的语法：CREATE INDEX 索引名 on 表名（字段名1，字段名2）

给username和password创建联合索引：CREATE index t1_username_password ON t1(username,password)

其中index还可以替换成unique，primary key，分别代表唯一索引和主键索引

删除索引：DROP INDEX t1_username_password ON t1

索引对性能的影响：

大大减少服务器需要扫描的数据量。

帮助服务器避免排序和临时表。

将随机I/O变顺序I/O。

大大提高查询速度。

降低写的速度（不良影响）。

磁盘占用（不良影响）。

索引的使用场景：

对于非常小的表，大部分情况下全表扫描效率更高。

中到大型表，索引非常有效。

特大型的表，建立和使用索引的代价会随之增大，可以使用分区技术来解决。

索引的类型：索引很多种类型，是在MySQL的存储引擎实现的。

普通索引：最基本的索引，没有任何约束限制。

唯一索引：和普通索引类似，但是具有唯一性约束。

主键索引：特殊的唯一索引，不允许有空值。

索引的区别：-一个表只能有一个主键索引，但是可以有多个唯一索引。

主键索引一定是唯一索引，唯一索引不是主键索引。

主键可以与外键构成参照完整性约束，防止数据不一致。

联合索引：将多个列组合在一起创建索引，可以覆盖多个列。（也叫复合索引，组合索引）

外键索引：只有InnoDB类型的表才可以使用外键索引，保证数据的一致性、完整性、和实现级联操作（基本不用）。

全文索引：MySQL自带的全文索引只能用于MyISAM，并且只能对英文进行全文检索 （基本不用）

MySQL索引的创建原则

最适合创建索引的列是出现在WHERE或ON子句中的列，或连接子句中的列而不是出现在SELECT关键字后的列。

索引列的基数越大，数据区分度越高，索引的效果越好。

对于字符串进行索引，应该制定一个前缀长度，可以节省大量的索引空间。

根据情况创建联合索引，联合索引可以提高查询效率。

避免创建过多的索引，索引会额外占用磁盘空间，降低写操作效率。

主键尽可能选择较短的数据类型，可以有效减少索引的磁盘占用提高查询效率。

MySQL索引的注意事项

1、联合索引遵循前缀原则

KEY(a,b,c)WHERE a = 1 AND b = 2 AND c = 3WHERE a = 1 AND b = 2WHERE a = 1#以上SQL语句可以用到索引
WHERE b = 2 AND c = 3WHERE a = 1 AND c = 3#以上SQL语句用不到索引
2、LIKE查询，%不能在前

WHERE name LIKE "%wang%"#以上语句用不到索引，可以用外部的ElasticSearch、Lucene等全文搜索引擎替代。
3、列值为空（NULL）时是可以使用索引的，但MySQL难以优化引用了可空列的查询,它会使索引、索引统计和值更加复杂。
可空列需要更多的储存空间，还需要在MySQL内部进行特殊处理。

4、如果MySQL估计使用索引比全表扫描更慢，会放弃使用索引，例如：表中只有100条数据左右。
对于SQL语句WHERE id > 1 AND id < 100，MySQL会优先考虑全表扫描。

5、如果关键词or前面的条件中的列有索引，后面的没有，所有列的索引都不会被用到。

6、列类型是字符串，查询时一定要给值加引号，否则索引失效，例如：列name varchar(16)，
存储了字符串"100"WHERE name = 100;以上SQL语句能搜到，但无法用到索引。

MySQL索引的原理

MySQL索引是用一种叫做聚簇索引的数据结构实现的，下面我们就来看一下什么是聚簇索引。

聚簇索引是一种数据存储方式，它实际上是在同一个结构中保存了B+树索引和数据行，InnoDB表是按照聚簇索引组织的（类似于Oracle的索引组织表）。

注：B+ 树是一种树数据结构，是一个n叉排序树，每个节点通常有多个孩子，一棵B+树包含根节点、内部节点和叶子节点。
根节点可能是一个叶子节点，也可能是一个包含两个或两个以上孩子节点的节点。B+ 树通常用于数据库和操作系统的文件系统中。
NTFS, ReiserFS, NSS, XFS, JFS, ReFS 和BFS等文件系统都在使用B+树作为元数据索引。B+ 树的特点是能够保持数据稳定有序，
其插入与修改拥有较稳定的对数时间复杂度。B+ 树元素自底向上插入。
InnoDB通过主键聚簇数据，如果没有定义主键，会选择一个唯一的非空索引代替，如果没有这样的索引，会隐式定义个主键作为聚簇索引。
下图形象说明了聚簇索引表(InnoDB)和普通的堆组织表(MyISAM)的区别：

最常问的MySQL面试题三——每个开发人员都应该知道对于普通的堆组织表来说（右图），表数据和索引是分别存储的，
主键索引和二级索引存储上没有任何区别。而对于聚簇索引表来说（左图），表数据是和主键一起存储的，主键索引的叶结点存储行数据，
二级索引的叶结点存储行的主键值。聚簇索引表最大限度地提高了I/O密集型应用的性能，但它也有以下几个限制：

1）插入速度严重依赖于插入顺序，按照主键的顺序插入是最快的方式，否则将会出现页分裂，严重影响性能。因此，对于InnoDB表，
我们一般都会定义一个自增的ID列为主键。

2）更新主键的代价很高，因为将会导致被更新的行移动。因此，对于InnoDB表，我们一般定义主键为不可更新。

3）二级索引访问需要两次索引查找，第一次找到主键值，第二次根据主键值找到行数据。

二级索引的叶节点存储的是主键值，而不是行指针，这是为了减少当出现行移动或数据页分裂时二级索引的维护工作，但会让二级索引占用更多的空间。

解题方法
在一些MySQL索引基础考题中，我们可以轻松的通过索引基础和类型来解决此类问题，对于一些索引创建注意事项方面的考点，
我们可以通过索引创建原则和注意事项来解决。

####问题15：创建MySQL联合索引应该注意什么？

需遵循前缀原则

####问题16：列值为NULL时，查询是否会用到索引？

在MySQL里NULL值的列也是走索引的。当然，如果计划对列进行索引，就要尽量避免把它设置为可空，MySQL难以优化引用了可空列的查询,
它会使索引、索引统计和值更加复杂。

####问题17：以下语句是否会应用索引：SELECT FROM users WHERE YEAR(adddate) < 2007;

不会，因为只要列涉及到运算，MySQL就不会使用索引。

####问题18：MyISAM索引实现？

MyISAM存储引擎使用B+Tree作为索引结构，叶节点的data域存放的是数据记录的地址。MyISAM的索引方式也叫做非聚簇索引的，
之所以这么称呼是为了与InnoDB的聚簇索引区分。

####问题19：MyISAM索引与InnoDB索引的区别？

InnoDB索引是聚簇索引，MyISAM索引是非聚簇索引。

InnoDB的主键索引的叶子节点存储着行数据，因此主键索引非常高效。

MyISAM索引的叶子节点存储的是行数据地址，需要再寻址一次才能得到数据。

InnoDB非主键索引的叶子节点存储的是主键和其他带索引的列数据，因此查询时做到覆盖索引会非常高效。

####问题20：以下三条sql 如何建索引，只建一条怎么建？

WHERE a=1 AND b=1WHERE b=1WHERE b=1 ORDER BY time DESC
以顺序b,a,time建立联合索引，CREATE INDEX table1_b_a_time ON index_test01(b,a,time)。
因为最新MySQL版本会优化WHERE子句后面的列顺序，以匹配联合索引顺序。

####问题21：有A(id,sex,par,c1,c2),B(id,age,c1,c2)两张表，其中A.id与B.id关联，现在要求写出一条SQL语句，
	将B中age>50的记录的c1,c2更新到A表中同一记录中的c1,c2字段中 考点分析

这道题主要考察的是MySQL的关联UPDATE语句延伸考点：

MySQL的关联查询语句

MySQL的关联UPDATE语句

针对刚才这道题，答案可以是如下两种形式的写法：
UPDATE A,B SET A.c1 = B.c1, A.c2 = B.c2 WHERE A.id = B.idUPDATE A INNER JOIN B ON A.id=B.id SET A.c1 = B.c1,
A.c2=B.c2再加上B中age>50的条件：UPDATE A,B set A.c1 = B.c1, A.c2 = B.c2 WHERE A.id = B.id and B.age > 50;
UPDATE A INNER JOIN B ON A.id = B.id set A.c1 = B.c1,A.c2 = B.c2 WHERE B.age > 50



----------

####1、MySQL的复制原理以及流程
基本原理流程，3个线程以及之间的关联；
2、MySQL中myisam与innodb的区别，至少5点

问5点不同；
innodb引擎的4大特性
####2者selectcount(*)哪个更快，为什么

####3、MySQL中varchar与char的区别以及varchar(50)中的50代表的涵义
(1)、varchar与char的区别
(2)、varchar(50)中50的涵义
(3)、int（20）中20的涵义
(4)、mysql为什么这么设计
####4、问了innodb的事务与日志的实现方式

有多少种日志；
事物的4种隔离级别
事务是如何通过日志来实现的，说得越深入越好。

####5、问了MySQL binlog的几种日志录入格式以及区别

binlog的日志格式的种类和分别
适用场景；
结合第一个问题，每一种日志格式在复制中的优劣。

####6、问了下MySQL数据库cpu飙升到500%的话他怎么处理？

没有经验的，可以不问；
有经验的，问他们的处理思路。

####7、sql优化

explain出来的各种item的意义；
profile的意义以及使用场景；

####8、备份计划，mysqldump以及xtranbackup的实现原理

备份计划；
备份恢复时间；
xtrabackup实现原理

####9、mysqldump中备份出来的sql，如果我想sql文件中，一行只有一个insert....value()的话，怎么办？如果备份需要带上master的复制点信息怎么办？
####10、500台db，在最快时间之内重启
####11、innodb的读写参数优化

读取参数
写入参数；
与IO相关的参数；
缓存参数以及缓存的适用场景。

####12、你是如何监控你们的数据库的？你们的慢日志都是怎么查询的？
####13、你是否做过主从一致性校验，如果有，怎么做的，如果没有，你打算怎么做？
####14、你们数据库是否支持emoji表情，如果不支持，如何操作？
####15、你是如何维护数据库的数据字典的?
####16、你们是否有开发规范，如果有，如何执行的
####17、表中有大字段X(例如：text类型)，且字段X不会经常更新，以读为为主，请问

您是选择拆成子表，还是继续放一起；
写出您这样选择的理由。

####18、MySQL中InnoDB引擎的行锁是通过加在什么上完成(或称实现)的？为什么是这样子的？
####19、如何从mysqldump产生的全库备份中只恢复某一个库、某一张表？
	开放性问题：据说是腾讯的

一个6亿的表a，一个3亿的表b，通过外间tid关联，你如何最快的查询出满足条件的第50000到第50200中的这200条数据记录。

答案
####1、MySQL的复制原理以及流程

基本原理流程，3个线程以及之间的关联；


​	

主：binlog线程——记录下所有改变了数据库数据的语句，放进master上的binlog中；
从：io线程——在使用start slave 之后，负责从master上拉取 binlog 内容，放进 自己的relay log中；
从：sql执行线程——执行relay log中的语句；

####2、MySQL中myisam与innodb的区别，至少5点

(1)、问5点不同；
1>.InnoDB支持事物，而MyISAM不支持事物

2>.InnoDB支持行级锁，而MyISAM支持表级锁
3>.InnoDB支持MVCC, 而MyISAM不支持
4>.InnoDB支持外键，而MyISAM不支持
5>.InnoDB不支持全文索引，而MyISAM支持。
(2)、innodb引擎的4大特性
插入缓冲（insert buffer),二次写(double write),自适应哈希索引(ahi),预读(read ahead)
(3)、2者selectcount(*)哪个更快，为什么 myisam更快，因为myisam内部维护了一个计数器，可以直接调取。

####3、MySQL中varchar与char的区别以及varchar(50)中的50代表的涵义

(1)、varchar与char的区别char是一种固定长度的类型，varchar则是一种可变长度的类型
(2)、varchar(50)中50的涵义最多存放50个字符，varchar(50)和(200)存储hello所占空间一样，但后者在排序时会消耗更多内存，
因为order by col采用fixed_length计算col长度(memory引擎也一样)
(3)、int（20）中20的涵义是指显示字符的长度但要加参数的，最大为255，比如它是记录行数的id,插入10笔资料，
它就显示00000000001 ~~~00000000010，当字符的位数超过11,它也只显示11位，如果你没有加那个让它未满11位就前面加0的参数，
它不会在前面加020表示最大显示宽度为20，但仍占4字节存储，存储范围不变；
(4)、mysql为什么这么设计对大多数应用没有意义，只是规定一些工具用来显示字符的个数；int(1)和int(20)存储和计算均一样；

####4、问了innodb的事务与日志的实现方式

(1)、有多少种日志；错误日志：记录出错信息，也记录一些警告信息或者正确的信息。查询日志：记录所有对数据库请求的信息，
不论这些请求是否得到了正确的执行。慢查询日志：设置一个阈值，将运行时间超过该值的所有SQL语句都记录到慢查询的日志文件中。
二进制日志：记录对数据库执行更改的所有操作。中继日志：事务日志：
(2)、事物的4种隔离级别隔离级别读未提交(RU)读已提交(RC)可重复读(RR)串行
(3)、事务是如何通过日志来实现的，说得越深入越好。事务日志是通过redo和innodb的存储引擎日志缓冲（Innodb log buffer）来实现的，
当开始一个事务的时候，会记录该事务的lsn(log sequence number)号; 当事务执行时，会往InnoDB存储引擎的日志的日志缓存里面插入事务日志；
当事务提交时，必须将存储引擎的日志缓冲写入磁盘（通过innodb_flush_log_at_trx_commit来控制），也就是写数据前，需要先写日志。
这种方式称为“预写日志方式”

####5、问了MySQL binlog的几种日志录入格式以及区别

(1)、binlog的日志格式的种类和分别
(2)、适用场景；
(3)、结合第一个问题，每一种日志格式在复制中的优劣。Statement：每一条会修改数据的sql都会记录在binlog中。
优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。(相比row能节约多少性能 与日志量，
这个取决于应用的SQL情况，正常同一条记录修改或者插入row格式所产生的日志量还小于Statement产生的日志量，
但是考虑到如果带条 件的update操作，以及整表删除，alter表等操作，ROW格式会产生大量日志，
因此在考虑是否使用ROW格式日志时应该跟据应用的实际情况，其所 产生的日志量会增加多少，以及带来的IO性能问题。)
缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的 一些相关信息，
以保证所有语句能在slave得到和在master端执行时候相同 的结果。另外mysql 的复制,像一些特定函数功能，
slave可与master上要保持一致会有很多相关问题(如sleep()函数， last_insert_id()，以及user-defined functions(udf)会出现问题).
使用以下函数的语句也无法被复制：


​	

LOAD_FILE()
UUID()
USER()
FOUND_ROWS()
SYSDATE() (除非启动时启用了 --sysdate-is-now 选项)
同时在INSERT ...SELECT 会产生比 RBR 更多的行级锁
2.Row:不记录sql语句上下文相关信息，仅保存哪条记录被修改。优点： binlog中可以不记录执行的sql语句的上下文相关的信息，
仅需要记录那一条记录被修改成什么了。所以rowlevel的日志内容会非常清楚的记录下 每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，
或function，以及trigger的调用和触发无法被正确复制的问题缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，
这样可能会产生大量的日志内容,比 如一条update语句，修改多条记录，则binlog中每一条修改都会有记录，这样造成binlog日志量会很大，
特别是当执行alter table之类的语句的时候，由于表结构修改，每条记录都发生改变，那么该表每一条记录都会记录到日志中。
	3.Mixedlevel: 是以上两种level的混合使用，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，
则 采用row格式保存binlog,MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择 一种.
新版本的MySQL中队row level模式也被做了优化，并不是所有的修改都会以row level来记录，像遇到表结构变更的时候就会以statement模式来记录。
至于update或者delete等修改数据的语句，还是会记录所有行的 变更。

####6、问了下MySQL数据库cpu飙升到500%的话他怎么处理？

(1)、没有经验的，可以不问；
(2)、有经验的，问他们的处理思路。列出所有进程  show processlist  观察所有进程  多秒没有状态变化的(干掉)查看超时日志或者错误日志 
(做了几年开发,一般会是查询以及大批量的插入会导致cpu与i/o上涨,,,,当然不排除网络状态突然断了,,导致一个请求服务器只接受到一半，
比如where子句或分页子句没有发送,,当然的一次被坑经历)

####7、sql优化

(1)、explain出来的各种item的意义；
select_type
表示查询中每个select子句的类型
type
表示MySQL在表中找到所需行的方式，又称“访问类型”
possible_keys
指出MySQL能使用哪个索引在表中找到行，查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询使用
key
显示MySQL在查询中实际使用的索引，若没有使用索引，显示为NULL
key_len
表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度
ref
表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值
Extra
包含不适合在其他列中显示但十分重要的额外信息
(2)、profile的意义以及使用场景；查询到 SQL 会执行多少时间, 并看出 CPU/Memory 使用量, 执行过程中 Systemlock, Table lock 花多少时间等等

####8、备份计划，mysqldump以及xtranbackup的实现原理

(1)、备份计划；这里每个公司都不一样，您别说那种1小时1全备什么的就行
(2)、备份恢复时间；这里跟机器，尤其是硬盘的速率有关系，以下列举几个仅供参考20G的2分钟（mysqldump）80G的30分钟(mysqldump)111G的30分钟
（mysqldump)288G的3小时（xtra)3T的4小时（xtra)逻辑导入时间一般是备份时间的5倍以上
(3)、xtrabackup实现原理在InnoDB内部会维护一个redo日志文件，我们也可以叫做事务日志文件。
事务日志会存储每一个InnoDB表数据的记录修改。当InnoDB启动时，InnoDB会检查数据文件和事务日志，
并执行两个步骤：它应用（前滚）已经提交的事务日志到数据文件，并将修改过但没有提交的数据进行回滚操作。

####9、mysqldump中备份出来的sql，如果我想sql文件中，一行只有一个insert....value()的话，怎么办？如果备份需要带上master的复制点信息怎么办？
	--skip-extended-insert
	[root@helei-zhuanshu ~]# mysqldump -uroot -p helei --skip-extended-insert
	Enter password:  
	  KEY `idx_c1` (`c1`),  
	  KEY `idx_c2` (`c2`)
	) ENGINE=InnoDB AUTO_INCREMENT=51 DEFAULT CHARSET=latin1;
	/*!40101 SET character_set_client = @saved_cs_client */;
	--
	-- Dumping data for table `helei`
	--
	
	LOCK TABLES `helei` WRITE;
	/*!40000 ALTER TABLE `helei` DISABLE KEYS */;
	INSERT INTO `helei` VALUES (1,32,37,38,'2016-10-18 06:19:24','susususususususususususu');
	INSERT INTO `helei` VALUES (2,37,46,21,'2016-10-18 06:19:24','susususususu');
	INSERT INTO `helei` VALUES (3,21,5,14,'2016-10-18 06:19:24','susu');

####10、500台db，在最快时间之内重启

puppet，dsh

####11、innodb的读写参数优化

(1)、读取参数global buffer pool以及 local buffer；
(2)、写入参数；innodb_flush_log_at_trx_commitinnodb_buffer_pool_size
(3)、与IO相关的参数；innodb_write_io_threads = 8innodb_read_io_threads = 8innodb_thread_concurrency = 0
(4)、缓存参数以及缓存的适用场景。query cache/query_cache_type并不是所有表都适合使用query cache。
造成query cache失效的原因主要是相应的table发生了变更
	第一个：读操作多的话看看比例，简单来说，如果是用户清单表，或者说是数据比例比较固定，比如说商品列表，是可以打开的，
前提是这些库比较集中，数据库中的实务比较小。
	第二个：我们“行骗”的时候，比如说我们竞标的时候压测，把query cache打开，还是能收到qps激增的效果，当然前提示前端的连接池什么的都配置一样。
大部分情况下如果写入的居多，访问量并不多，那么就不要打开，例如社交网站的，10%的人产生内容，其余的90%都在消费，打开还是效果很好的，
但是你如果是qq消息，或者聊天，那就很要命。
第三个：小网站或者没有高并发的无所谓，高并发下，会看到 很多 qcache 锁 等待，所以一般高并发下，不建议打开query cache

####12、你是如何监控你们的数据库的？你们的慢日志都是怎么查询的？

监控的工具有很多，例如zabbix，lepus，我这里用的是lepus

####13、你是否做过主从一致性校验，如果有，怎么做的，如果没有，你打算怎么做？

主从一致性校验有多种工具 例如checksum、mysqldiff、pt-table-checksum等

####14、你们数据库是否支持emoji表情，如果不支持，如何操作？

如果是utf8字符集的话，需要升级至utf8_mb4方可支持

####15、你是如何维护数据库的数据字典的？

这个大家维护的方法都不同，我一般是直接在生产库进行注释，利用工具导出成excel方便流通。16、你们是否有开发规范，

如果有，如何执行的有，开发规范网上有很多了，可以自己看看总结下

####17、表中有大字段X(例如：text类型)，且字段X不会经常更新，以读为为主，请问
	(1)、您是选择拆成子表，还是继续放一起；
	(2)、写出您这样选择的理由。

答：拆带来的问题：连接消耗 + 存储拆分空间；不拆可能带来的问题：查询性能；如果能容忍拆分带来的空间问题,拆的话最好和经常要查询的表的
主键在物理结构上放置在一起(分区) 顺序IO,减少连接消耗,最后这是一个文本列再加上一个全文索引来尽量抵消连接消耗如果能容忍不拆分
带来的查询性能损失的话:上面的方案在某个极致条件下肯定会出现问题,那么不拆就是最好的选择

####18、MySQL中InnoDB引擎的行锁是通过加在什么上完成(或称实现)的？为什么是这样子的？

答：InnoDB是基于索引来完成行锁例: select * from tab_with_index where id = 1 for update;for update 可以根据条件

来完成行锁锁定,并且 id 是有索引键的列,如果 id 不是索引键那么InnoDB将完成表锁,,并发将无从谈起
.

####19、如何从mysqldump产生的全库备份中只恢复某一个库、某一张表？

答案见：http://suifu.blog.51cto.com/9167728/1830651

开放性问题：据说是腾讯的

一个6亿的表a，一个3亿的表b，通过外间tid关联，你如何最快的查询出满足条件的第50000到第50200中的这200条数据记录。


​	

1、如果A表TID是自增长,并且是连续的,B表的ID为索引select * from a,b where a.tid = b.id and a.tid>500000 limit 200;
2、如果A表的TID不是连续的,那么就需要使用覆盖索引.TID要么是主键,要么是辅助索引,B表ID也需要有索引。
select * from b , (select tid from a limit 50000,200) a where b.id = a





#二、*参考链接地址*


[https://juejin.im/entry/5b57ec015188251aa8292a69](https://juejin.im/entry/5b57ec015188251aa8292a69)

[https://www.jianshu.com/p/977a9e7d80b3](https://www.jianshu.com/p/977a9e7d80b3)

[https://zhuanlan.zhihu.com/p/23713529](https://zhuanlan.zhihu.com/p/23713529)

[http://database.51cto.com/art/201807/578423.htm](http://database.51cto.com/art/201807/578423.htm)

[https://cloud.tencent.com/developer/article/1349819](https://cloud.tencent.com/developer/article/1349819)

[https://yq.aliyun.com/articles/656946](https://yq.aliyun.com/articles/656946)

[https://www.cnblogs.com/frankielf0921/p/5930743.html](https://www.cnblogs.com/frankielf0921/p/5930743.html)

[https://my.oschina.net/u/3869066/blog/2250885](https://my.oschina.net/u/3869066/blog/2250885)

[https://yq.aliyun.com/articles/283427](https://yq.aliyun.com/articles/283427)

[https://www.douban.com/note/345871485/](https://www.douban.com/note/345871485/)

[https://www.hhcycj.com/post/item/113.html#20](https://www.hhcycj.com/post/item/113.html#20)