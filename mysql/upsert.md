### INSERT ... ON DUPLICATE KEY UPDATE
```
ON DUPLICATE KEY UPDATE能保证操作的原子性和数据的完整性

若该数据的主键值/ UNIQUE KEY 已经在表中存在,则执行更新操作, 即UPDATE 后面的操作，否则插入一条新的记录。
```