## MySQL里边还有几种常见的log
```
undo log
  主要作用:undo log主要有两个作用：回滚和多版本控制(MVCC)
  在数据修改的时候，不仅记录了redo log，还记录undo log，如果因为某些原因导致事务失败或回滚了，可以用undo log进行回滚
  undo log主要存储的也是逻辑日志，比如我们要insert一条数据了，那undo log会记录的一条对应的delete日志。我们要update一条记录时，它会记录一条对应相反的update记录。


binlog
  存储着每条变更的SQL语句(不止SQL，还有XID「事务Id」等等),比如update/delete/insert/truncate/create
  主要有两个作用：复制和恢复数据

redo log
  如果每个请求都需要将数据立马落磁盘之后，那速度会很慢，MySQL可能也顶不住
  MySQL引入了redo log，内存写完了，然后会写一份redo log，这份redo log记载着这次在某个页上做了什么修改
  写redo log的时候，也会有buffer，是先写buffer，再真正落到磁盘中的。至于从buffer什么时候落磁盘，会有配置供我们配置
  写redo log也是需要写磁盘的，但它的好处就是顺序IO（我们都知道顺序IO比随机IO快非常多）
  redo log的存在为了：当我们修改的时候，写完内存了，但数据还没真正写到磁盘的时候。此时我们的数据库挂了，我们可以根据redo log来对数据进行恢复。因为redo log是顺序IO，所以写入的速度很快，并且redo log记载的是物理变化（xxxx页做了xxx修改），文件的体积很小，恢复速度很快。
```

### binlog和redo log的区别
![TCP Option](https://github.com/ruansheng/technology-study/blob/master/images/binlog_redolog.jpg)
```
binlog记载的是update/delete/insert这样的SQL语句
redo log记载的是物理修改的内容（xxxx页修改了xxx）

redo log 记录的是数据的物理变化
binlog 记录的是数据的逻辑变化

redo log的作用是为持久化而生的:写完内存，如果数据库挂了，那我们可以通过redo log来恢复内存还没来得及刷到磁盘的数据，将redo log加载到内存里边，那内存就能恢复到挂掉之前的数据了。
binlog的作用是复制和恢复而生的:(主从服务器需要保持数据的一致性，通过binlog来同步数据)(如果整个数据库的数据都被删除了，binlog存储着所有的数据变更情况，那么可以通过binlog来对数据进行恢复。)

redo log是MySQL的InnoDB引擎所产生的。
binlog无论MySQL用什么引擎，都会有的。

InnoDB是有事务的，事务的四大特性之一：持久性就是靠redo log来实现的（如果写入内存成功，但数据还没真正刷到磁盘，如果此时的数据库挂了，我们可以靠redo log来恢复内存的数据，这就实现了持久性）。

redo log事务开始的时候，就开始记录每次的变更信息
binlog是在事务提交的时候才记录。

MySQL需要保证redo log和binlog的数据是一致的，如果不一致，那就乱套了
如果写redo log失败了，那我们就认为这次事务有问题，回滚，不再写binlog。
如果写redo log成功了，写binlog，写binlog写一半了，但失败了怎么办？我们还是会对这次的事务回滚，将无效的binlog给删除（因为binlog会影响从库的数据，所以需要做删除操作）
如果写redo log和binlog都成功了，那这次算是事务才会真正成功。

MySQL通过两阶段提交来保证redo log和binlog的数据是一致的。

阶段1：InnoDBredo log 写盘，InnoDB 事务进入 prepare 状态
阶段2：binlog 写盘，InooDB 事务进入 commit 状态
每个事务binlog的末尾，会记录一个 XID event，标志着事务是否提交成功，也就是说，恢复过程中，binlog 最后一个 XID event 之后的内容都应该被 purge。
```
