## show processlist
显示哪些线程正在运行

如果有root权限，可以看到所有线程。否则，您只能看到登录的用户自己的线程，通常只会显示100条如果想看跟多的可以使用full修饰（show full processlist）

```
mysql> show processlist;
+----+------+-----------+-------+---------+------+----------+------------------+
| Id | User | Host      | db    | Command | Time | State    | Info             |
+----+------+-----------+-------+---------+------+----------+------------------+
|  9 | root | localhost | study | Query   |    0 | starting | show processlist |
+----+------+-----------+-------+---------+------+----------+------------------+

参数
 id      #ID标识，要kill一个语句的时候很有用
use      #当前连接用户
host     #显示这个连接从哪个ip的哪个端口上发出
db       #数据库名
command  #连接状态，一般是休眠（sleep），查询（query），连接（connect）
time     #连接持续时间，单位是秒
state    #显示当前sql语句的状态
info     #显示这个sql语句

其中state的状态十分关键，下表列出state主要状态和描述：
状态 	            描述
Checking table	 正在检查数据表（这是自动的）。
Closing tables	 正在将表中修改的数据刷新到磁盘中，同时正在关闭已经用完的表。这是一个很快的操作，如果不是这样的话，就应该确认磁盘空间是否已经满了或者磁盘是否正处于重负中。
Connect Out	     复制从服务器正在连接主服务器。
Copying to tmp table on disk	由于临时结果集大于tmp_table_size，正在将临时表从内存存储转为磁盘存储以此节省内存。
Creating tmp table	正在创建临时表以存放部分查询结果。
deleting from main table	服务器正在执行多表删除中的第一部分，刚删除第一个表。
deleting from reference tables	服务器正在执行多表删除中的第二部分，正在删除其他表的记录。
Flushing tables	正在执行FLUSH TABLES，等待其他线程关闭数据表。
Killed	发送了一个kill请求给某线程，那么这个线程将会检查kill标志位，同时会放弃下一个kill请求。MySQL会在每次的主循环中检查kill标志位，不过有些情况下该线程可能会过一小段才能死掉。如果该线程程被其他线程锁住了，那么kill请求会在锁释放时马上生效。
 	 
Locked	被其他查询锁住了。
 	 
Sending data	正在处理SELECT查询的记录，同时正在把结果发送给客户端。
Sorting for group	正在为GROUP BY做排序。
Sorting for order	正在为ORDER BY做排序。
Opening tables	这个过程应该会很快，除非受到其他因素的干扰。例如，在执ALTER TABLE或LOCK TABLE语句行完以前，数据表无法被其他线程打开。正尝试打开一个表。
Removing duplicates	正在执行一个SELECT DISTINCT方式的查询，但是MySQL无法在前一个阶段优化掉那些重复的记录。因此，MySQL需要再次去掉重复的记录，然后再把结果发送给客户端。
 	 
Reopen table	获得了对一个表的锁，但是必须在表结构修改之后才能获得这个锁。已经释放锁，关闭数据表，正尝试重新打开数据表。
Repair by sorting	修复指令正在排序以创建索引。
Repair with keycache	修复指令正在利用索引缓存一个一个地创建新索引。它会比Repair by sorting慢些。
Searching rows for update	正在讲符合条件的记录找出来以备更新。它必须在UPDATE要修改相关的记录之前就完成了。
Sleeping	正在等待客户端发送新请求.
System lock	正在等待取得一个外部的系统锁。如果当前没有运行多个mysqld服务器同时请求同一个表，那么可以通过增加--skip-external-locking参数来禁止外部系统锁。
Upgrading lock	INSERT DELAYED正在尝试取得一个锁表以插入新记录。
Updating	正在搜索匹配的记录，并且修改它们。
User Lock	正在等待GET_LOCK()。
Waiting for tables	该线程得到通知，数据表结构已经被修改了，需要重新打开数据表以取得新的结构。然后，为了能的重新打开数据表，必须等到所有其他线程关闭这个表。以下几种情况下会产生这个通知：FLUSH TABLES tbl_name, ALTER TABLE, RENAME TABLE, REPAIR TABLE, ANALYZE TABLE,或OPTIMIZE TABLE。
waiting for handler insert	INSERT DELAYED已经处理完了所有待处理的插入操作，正在等待新的请求。
```
