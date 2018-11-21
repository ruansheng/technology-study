## myisam && innodb

### myisam
```
不支持事务，回滚将造成不完全回滚，不具有原子性
不支持外键
不支持外键
支持全文搜索
保存表的具体行数,不带where时，直接返回保存的行数
DELETE 表时，先drop表，然后重建表
MyISAM 表被存放在三个文件 。frm 文件存放表格定义。 数据文件是MYD (MYData) 。 索引文件是MYI (MYIndex)引伸
跨平台很难直接拷贝
MyISAM中可以使AUTO_INCREMENT类型字段建立联合索引
表格可以被压缩
```

### innodb
```
支持事务处理等
不加锁读取
支持外键
支持行锁
不支持FULLTEXT类型的索引
不保存表的具体行数，扫描表来计算有多少行
DELETE 表时，是一行一行的删除
InnoDB 把数据和索引存放在表空间里面
跨平台可直接拷贝使用
InnoDB中必须包含AUTO_INCREMENT类型字段的索引
表格很难被压缩
```

### 选择myisam还是innodb
```
因为MyISAM相对简单所以在效率上要优于InnoDB.如果系统读多，写少。对原子性要求低。那么MyISAM最好的选择。且MyISAM恢复速度快。可直接用备份覆盖恢复。
如果系统读少，写多的时候，尤其是并发写入高的时候。InnoDB就是首选了
```
