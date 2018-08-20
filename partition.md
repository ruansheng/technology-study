## partition 表分区
MySQL 5.1之后的版本支持表分区
```
Range（范围） – 这种模式允许将数据划分不同范围。例如可以将一个表通过年份划分成三个分区，80年代（1980's）的数据，90年代（1990's）的数据以及任何在2000年（包括2000年）后的数据

Hash（哈希）  – 这中模式允许通过对表的一个或多个列的Hash Key进行计算，最后通过这个Hash码不同数值对应的数据区域进行分区，。例如DBA可以建立一个对表主键进行分区的表

Key（键值）   – 上面Hash模式的一种延伸，这里的Hash Key是MySQL系统产生的

List（预定义列表） – 这种模式允许系统通过DBA定义的列表的值所对应的行数据进行分割。例如：DBA建立了一个横跨三个分区的表，分别根据2004年2005年和2006年值所对应的数据

Composite（复合模式） - 很神秘吧，哈哈，其实是以上模式的组合使用而已，就不解释了。举例：在初始化已经进行了Range范围分区的表上，我们可以对其中一个分区再进行hash哈希分区
```

### Range分区
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
