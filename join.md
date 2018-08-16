## sql 5种连接
```
分别创建table a and  table b
create table `a` (
	`id` int default 0,
	`name` char(10) default ''
)engine=innodb;

create table `b` (
	`id` int default 0,
	`name` char(10) default ''
)engine=innodb;

分别给 a、b 两个表插入数据
insert into a value(1, "a");
insert into a value(2, "b");
insert into a value(3, "c");
insert into a value(4, "d");

insert into b value(3, "c");
insert into b value(4, "d");
insert into b value(5, "e");
insert into b value(6, "f");

内连接
select a.id as aid,a.name as aname,b.id as bid,b.name as bname from a inner join b on a.id = b.id;
+------+-------+------+-------+
| aid  | aname | bid  | bname |
+------+-------+------+-------+
|    3 | c     |    3 | c     |
|    4 | d     |    4 | d     |
+------+-------+------+-------+

左外连接
select a.id as aid,a.name as aname,b.id as bid,b.name as bname from a left join b on a.id = b.id;
+------+-------+------+-------+
| aid  | aname | bid  | bname |
+------+-------+------+-------+
|    3 | c     |    3 | c     |
|    4 | d     |    4 | d     |
|    1 | a     | NULL | NULL  |
|    2 | b     | NULL | NULL  |
+------+-------+------+-------+

左连接
select a.id as aid,a.name as aname,b.id as bid,b.name as bname from a left join b on a.id = b.id where b.id is null;
+------+-------+------+-------+
| aid  | aname | bid  | bname |
+------+-------+------+-------+
|    1 | a     | NULL | NULL  |
|    2 | b     | NULL | NULL  |
+------+-------+------+-------+

右外连接
select a.id as aid,a.name as aname,b.id as bid,b.name as bname from a right join b on a.id = b.id;
+------+-------+------+-------+
| aid  | aname | bid  | bname |
+------+-------+------+-------+
|    3 | c     |    3 | c     |
|    4 | d     |    4 | d     |
| NULL | NULL  |    5 | e     |
| NULL | NULL  |    6 | f     |
+------+-------+------+-------+

右连接
select a.id as aid,a.name as aname,b.id as bid,b.name as bname from a right join b on a.id = b.id where a.id is null;
+------+-------+------+-------+
| aid  | aname | bid  | bname |
+------+-------+------+-------+
| NULL | NULL  |    5 | e     |
| NULL | NULL  |    6 | f     |
+------+-------+------+-------+
```
