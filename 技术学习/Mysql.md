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







