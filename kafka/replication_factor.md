## replication.factor
kafka的默认复制系数是3，意味着每个分区会有3个副本，1个leader副本+2个同步副本，保证高可用

## 动态指定replication.factor
```
bin/kafka-topics.sh --create --zookeeper 192.168.128.129:2181 --replication-factor 3 -partitions 1 --topic 3test
```