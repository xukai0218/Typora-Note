Mysql



# 1、索引

唯一索引

```
alter table bpos_order.order_sale add unique key (order_number);

Alter table  bpos_order.order_sale drop index order_number_2;

show index from  bpos_order.order_sale
```



```
查看索引  show index from 数据库表名
alter table 数据库add index 索引名称(数据库字段名称)
PRIMARY KEY（主键索引）
ALTER TABLE `table_name` ADD PRIMARY KEY ( `column` )
UNIQUE(唯一索引)
ALTER TABLE `table_name` ADD UNIQUE (`column`)
INDEX(普通索引)
mysql>ALTER TABLE `table_name` ADD INDEX index_name ( `column` )
FULLTEXT(全文索引)
ALTER TABLE `table_name` ADD FULLTEXT ( `col


表创建完了之后添加
Alter table 表名 add primary key(id)
删除主键索引:
Alter table 表名 drop primary key;

删除
Alter table s1 drop index u_name;


多列索引
ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3` )
1.普通索引。
这是最基本的索引，它没有任何限制。它有以下几种创建方式：
（1）创建索引：CREATE INDEX indexName ON tableName(tableColumns(length));如果是CHAR,VARCHAR类型，length可以小于字段实际长度;如果是BLOB 和 TEXT 类型，必须指定length，下同。
（2）修改表结构：ALTER tableName ADD INDEX [indexName] ON (tableColumns(length)) 
（3）创建表的时候直接指定：CREATE TABLE tableName ( [...], INDEX [indexName] (tableColumns(length)) ;


2.唯一索引。
它与前面的"普通索引"类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。它有以下几种创建方式：
（1）创建索引：CREATE UNIQUE INDEX indexName ON tableName(tableColumns(length))
（2）修改表结构：ALTER tableName ADD UNIQUE [indexName] ON (tableColumns(length))
（3）创建表的时候直接指定：CREATE TABLE tableName ( [...], UNIQUE [indexName] (tableColumns(lengt

查看索引
show index from tb_wz_all;
```

# 失效索引

```
1、如果条件中有or，即使其中有条件带索引也不会使用(这也是为什么尽量少用or的原因)
	注意：要想使用or，又想让索引生效，只能将or条件中的每个列都加上索引

2、对于多列索引，不是使用的第一部分，则不会使用索引

3、like查询是以%开头，索引失效；以%结尾，索引有效

4、如果列类型是字符串，那一定要在条件中将数据使用引号引用起来,否则不使用索引

5、如果mysql估计使用全表扫描要比使用索引快,则不使用索引

查看索引的使用情况：

show status like ‘Handler_read%’;

handler_read_key:这个值越高越好，越高表示使用索引查询到的次数

handler_read_rnd_next:这个值越高，说明查询低效

```

# 问题异常

```
Expression #2 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'loser.tank_admin.login_ip' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
违背了 去掉 sql_mode=only_full_group_by 

select @@sql_mode;

set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';

https://www.cnblogs.com/kenshinobiy/p/9580701.html
```



kill 

SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;

SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;

SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS;







# 什么是事务?

事务是数据库管理系统(DBMS)执行过程中的一个逻辑单位,由一个有限的数据库操作序列构成。

DML (Data Manipulation Language)

## 事务特性

原子性( Atomicity)

> 原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，这和前面两篇博客介绍事务的功能是一样的概念，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。

一致性( consistency)

> 一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。
>
> 　　拿转账来说，假设用户A和用户B两者的钱加起来一共是5000，那么不管A和B之间如何转账，转几次账，事务结束后两个用户的钱相加起来应该还得是5000，这就是事务的一致性

隔离性( Isolation)

> 隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。
>
> 　　即要达到这么一种效果：对于任意两个并发的事务T1和T2，在事务T1看来，T2要么在T1开始之前就已经结束，要么在T1结束之后才开始，这样每个事务都感觉不到有其他事务在并发地执行。
>
> 　　关于事务的隔离性数据库提供了多种隔离级别，稍后会介绍到。

持久性( Durability)

> 持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。
>
> 　　例如我们在使用JDBC操作数据库时，在提交事务方法后，提示用户事务操作完成，当我们程序执行完成直到看到提示后，就可以认定事务以及正确提交，即使这时候数据库出现了问题，也必须要将我们的事务完全执行完成，否则就会造成我们看到提示事务处理完毕，但是数据库因为故障而没有执行事务的重大错误。
>
> 以上介绍完事务的四大特性(简称ACID)，现在重点来说明下事务的隔离性，当多个线程都开启事务操作数据库中的数据时，数据库系统要能进行隔离操作，以保证各个线程获取数据的准确性，在介绍数据库提供的各种隔离级别之前，我们先看看如果不考虑事务的隔离性，会发生的几种问题：

# 隔离级别

## **① 脏读:**

>  脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据

## **② 不可重复读**:

> 是指在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读

#### 注：不可重复读和脏读的区别，脏读是某一个事务读取另一个事务未提交的脏数据; 不可重复读则是读取已提交事务  

## **④ 幻读**

> :第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样。

只有插入 读不一致的情况 

不可重复读 修改删除  幻读 insert



·未提交读(Read Uncommitted)：允许脏读，也就是可能读取到其他会话中未提交事务修改的数据

·提交读(Read Committed)：只能读取到已经提交的数据。Oracle等多数数据库默认都是该级别 (不重复读)

·可重复读(Repeated Read)：可重复读。在同一个事务内的查询都是事务开始时刻一致的，InnoDB默认级别。在SQL标准中，该隔离级别消除了不可重复读，但是还存在幻象读

·串行读(Serializable)：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞



![image-20200717214501460](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\事务隔离级别.png)

```
未提交读（Read uncommitted）        可能                            可能                      可能

已提交读（Read committed）          不可能                          可能                       可能

可重复读（Repeatable read）         不可能                          不可能                     可能

可串行化（Serializable ）           不可能                          不可能                     不可能
```



# 解决读一性的问题

保证一个事务中前后两次(无论多少次)读取数据结果
致,实现事务隔离

## 第一种(当前读 LBCC)

- [ ] > 在读取数据前,对其加锁,阻止其他事务对数据进行
  > 修改—— -Lock Based Concurrency Control(LBCC)

## 第二种(快照读-不加锁的读 MVCC)

> 生成一个数据请求时间点的—致性数据快照( Snapshot),
>
> 并用这个快照来提供一定级别(语句级或事务级)的一致性读取 Multi Version Concurrency
> Control(MVCC)。

![image-20200717220018322](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\数据库锁.png)

# 数据库锁

1. 行锁

   

2. 表锁



![image-20200717220412249](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\表锁行锁的区别.png)

MyISAM 支持 表锁 并发下降

InnoDB 都支持

![image-20200717220847367](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\数据库锁2.png)



![image-20200717221024570](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\锁的模式png)



![image-20200717221157081](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\共享锁.png)



![image-20200717221428053](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\排它锁.png)



区间定义

![image-20200717224459229](E:\Users\kai.xu\Desktop\My\Typora-Note\image\mysql\区间定义.png)





间隙锁阻塞的就是插入
最后的范围:
最后一个 record的下一个左开右闭的区间  