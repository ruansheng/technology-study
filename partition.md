## partition 表分区
MySQL 5.1之后的版本支持表分区
```
Range（范围） – 这种模式允许将数据划分不同范围。例如可以将一个表通过年份划分成三个分区，80年代（1980's）的数据，90年代（1990's）的数据以及任何在2000年（包括2000年）后的数据

List（预定义列表） – 这种模式允许系统通过DBA定义的列表的值所对应的行数据进行分割。例如：DBA建立了一个横跨三个分区的表，分别根据2004年2005年和2006年值所对应的数据

Hash（哈希）  – 这中模式允许通过对表的一个或多个列的Hash Key进行计算，最后通过这个Hash码不同数值对应的数据区域进行分区，。例如DBA可以建立一个对表主键进行分区的表

Key（键值）   – 上面Hash模式的一种延伸，这里的Hash Key是MySQL系统产生的

Composite（复合模式） - 很神秘吧，哈哈，其实是以上模式的组合使用而已，就不解释了。举例：在初始化已经进行了Range范围分区的表上，我们可以对其中一个分区再进行hash哈希分区
```

### Range分区
基于属于一个给定连续区间的列值，把多行分配给分区
```
create table range_test(
	id int not null,
	age int not null
)
partition by range(age)(
	partition p0 values less than (6),
	partition p1 values less than (11),
	partition p2 values less than (16),
	partition p3 values less than (20)
);
// 0 - 5  在p0分区上
// 6 - 10 在p1分区上
// 11 -15 在p2分区上
// 16 -19 在p3分区上
// 当插入21时，将会导致错误 可以用下面的方式来解决

create table range_test(
	id int not null,
	age int not null
)
partition by range(age)(
	partition p0 values less than (6),
	partition p1 values less than (11),
	partition p2 values less than (16),
	partition p3 values less than (maxvalue)
);

// 测试
insert into range_test value(1, 4);
insert into range_test value(2, 5);
insert into range_test value(3, 6);
insert into range_test value(4, 10);
insert into range_test value(5, 13);
insert into range_test value(6, 16);
insert into range_test value(7, 20);

mysql> select * from range_test;
+----+-----+
| id | age |
+----+-----+
|  1 |   4 |
|  2 |   5 |
|  3 |   6 |
|  4 |  10 |
|  5 |  13 |
|  6 |  16 |
|  7 |  20 |
+----+-----+

mysql> explain select * from range_test where age > 10;
+----+-------------+------------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table      | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+------------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | range_test | p2,p3      | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+------------+------------+------+---------------+------+---------+------+------+----------+-------------+
// 由上可见，在这个表没有任何索引的情况下，这个查询扫描的数据行数只有3行，只查询了p2,p3分区

// 删除一个分区 可以删除这个分区的数据
ALTER TABLE range_test DROP PARTITION p0;

mysql> alter table range_test drop partition p0;
Query OK, 0 rows affected (0.21 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> select * from range_test;
+----+-----+
| id | age |
+----+-----+
|  3 |   6 |
|  4 |  10 |
|  5 |  13 |
|  6 |  16 |
|  7 |  20 |
+----+-----+
```

### List分区
类似于按RANGE分区，区别在于LIST分区是基于列值匹配一个离散值集合中的某个值来进行选择
```
create table list_test(
	id int not null,
	age int not null
)
partition by list(age)(
	partition p0 values in (1,2,3),
	partition p1 values in (4,5,6),
	partition p2 values in (7,8,9),
	partition p3 values in (10,11,12)
);

// 测试
insert into list_test value(1, 1);
insert into list_test value(2, 2);
insert into list_test value(3, 3);
insert into list_test value(4, 4);
insert into list_test value(5, 5);
insert into list_test value(6, 6);
insert into list_test value(7, 7);
insert into list_test value(8, 8);
insert into list_test value(9, 9);
insert into list_test value(10, 10);
insert into list_test value(11, 11);
insert into list_test value(12, 12);

mysql> select * from list_test;
+----+-----+
| id | age |
+----+-----+
|  1 |   1 |
|  2 |   2 |
|  3 |   3 |
|  4 |   4 |
|  5 |   5 |
|  6 |   6 |
|  7 |   7 |
|  8 |   8 |
|  9 |   9 |
| 10 |  10 |
| 11 |  11 |
| 12 |  12 |
+----+-----+

// 插入没在离散值集合中的值将失败
mysql> insert into list_test value(13, 13);
ERROR 1526 (HY000): Table has no partition for value 13
如果试图插入列值（或分区表达式的返回值）不在分区值列表中的一行时，那么“INSERT”将失败并报错

mysql> explain select * from list_test where age > 10;
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | list_test | p3         | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
// 由上可见，在这个表没有任何索引的情况下，这个查询扫描的数据行数只有3行，只查询了p3分区

// 删除一个分区 可以删除这个分区的数据
ALTER TABLE list_test DROP PARTITION p0;

mysql> ALTER TABLE list_test DROP PARTITION p0;
Query OK, 0 rows affected (0.15 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> select * from list_test;
+----+-----+
| id | age |
+----+-----+
|  4 |   4 |
|  5 |   5 |
|  6 |   6 |
|  7 |   7 |
|  8 |   8 |
|  9 |   9 |
| 10 |  10 |
| 11 |  11 |
| 12 |  12 |
+----+-----+
```

### Hash分区
基于用户定义的表达式的返回值来进行选择的分区，该表达式使用将要插入到表中的这些行的列值进行计算。这个函数可以包含MySQL 中有效的、产生非负整数值的任何表达式
```
需要在后面再添加一个“PARTITIONS num”子句，其中num是一个非负的整数，它表示表将要被分割成分区的数量，如果没有包括一个PARTITIONS子句，那么分区的数量将默认为1

create table hash_test(
	id int not null,
	age int not null
)
partition by hash(age)
partitions 4;

// 测试
insert into hash_test value(1, 1);
insert into hash_test value(2, 2);
insert into hash_test value(3, 3);
insert into hash_test value(4, 4);

mysql> select * from hash_test;
+----+-----+
| id | age |
+----+-----+
|  4 |   4 |
|  1 |   1 |
|  2 |   2 |
|  3 |   3 |
+----+-----+

mysql> explain select * from hash_test where age > 2;
+----+-------------+-----------+-------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions  | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+-------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | hash_test | p0,p1,p2,p3 | ALL  | NULL          | NULL | NULL    | NULL |    4 |    33.33 | Using where |
+----+-------------+-----------+-------------+------+---------------+------+---------+------+------+----------+-------------+

mysql> explain select * from hash_test where age = 2;
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | hash_test | p2         | ALL  | NULL          | NULL | NULL    | NULL |    1 |   100.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
由上可见，范围查询会扫描所有分区，精确的值查询能定位到某个人特定分区

// 特别注意: 删除分区仅能用在RANGE/LIST分区
mysql> ALTER TABLE hash_test DROP PARTITION p0;
ERROR 1512 (HY000): DROP PARTITION can only be used on RANGE/LIST partitions
```
