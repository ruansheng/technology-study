## Mysql Innodb的AUTO_INCREMENT
```
1.较早版本的 MySQL 将 AUTO_INCREMENT 存储在内存中，实例重启后会根据表中的数据重新设置该值；
2.获取 AUTO_INCREMENT 时不会使用事务锁，并发的插入事务可能出现部分字段冲突导致插入失败；

重启了 MySQL 的实例，所以内存中的 AUTO_INCREMENT 计数器会被重置成表中的最大值
```

### AUTO_INCREMENT 的模式
```
在获取 AUTO_INCREMENT 时会加锁，但是该锁是语句锁，它的目的是保证 AUTO_INCREMENT 的获取不会导致线程竞争，而不是保证 MySQL 中主键的连续

由 InnoDB 存储引擎提供的 innodb_autoinc_lock_mode 配置控制的，该配置决定了获取 AUTO_INCREMENT 计时器时需要先得到的锁，该配置存在三种不同的模式，分别是
传统模式（Traditional）
连续模式（Consecutive）
交叉模式（Interleaved）

其中 MySQL 使用连续模式作为默认的锁模式：
a.传统模式 innodb_autoinc_lock_mode = 0
  在包含 AUTO_INCREMENT 属性的表中插入数据时，所有的 INSERT 语句都会获取表级别的 AUTO_INCREMENT 锁，该锁会在当前语句执行后释放
b.连续模式 innodb_autoinc_lock_mode = 1
  INSERT ... SELECT、REPLACE ... SELECT 以及 LOAD DATA 等批量的插入操作需要获取表级别的 AUTO_INCREMENT 锁，该锁会在当前语句执行后释放；
  简单的插入语句（预先知道插入多少条记录的语句）只需要获取获取 AUTO_INCREMENT 计数器的互斥锁并在获取主键后直接释放，不需要等待当前语句执行完成；
c.交叉模式 innodb_autoinc_lock_mode = 2
  所有的插入语句都不需要获取表级别的 AUTO_INCREMENT 锁，但是当多个语句插入的数据行数不确定时，可能存在分配相同主键的风险；

这三种模式都不能解决 MySQL 自增主键不连续的问题，想要解决这个问题的终极方案是串行执行所有包含插入操作的事务，也就是使用数据库的最高隔离级别 —— 可串行化（Serialiable）。
当然直接修改数据库的隔离级别相对来说有些简单粗暴，基于 MySQL 或者其他存储系统实现完全串行的插入也可以保证主键在插入时的连续，但是仍然不能避免删除数据导致的不连续。
```

