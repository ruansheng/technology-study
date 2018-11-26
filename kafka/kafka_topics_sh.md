## kafka-topics.sh 工具
kafka-topics.sh 工具用来操作topic

### 创建topic
```
kafka-topics.sh --zookeeper host:port/path --create --topic test_topic --replication-factor 2 --partitions 8
example:
 kafka-topics.sh --zookeeper 127.0.0.1:2181 --create --topic test_topic --replication-factor 2 --partitions 8
```

### 删除topic
```
kafka-topics.sh --zookeeper host:port/path --delete --topic test_topic
example:
 kafka-topics.sh --zookeeper 127.0.0.1:2181 --delete --topic test_topic
```

### 增加topic分区数
```
kafka-topics.sh --zookeeper host:port/path --alert --topic test_topic --parttions 8
example:
 kafka-topics.sh --zookeeper 127.0.0.1:2181 --alert --topic test_topic --parttions 8
```

### 列出所有topic
```
kafka-topics.sh --zookeeper host:port/path --list
列出所有topic，每个topic占用一行，topic之间没有特定的顺序
example:
  kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
```

### 列出topic详细信息
```
// 所有topic
kafka-topics.sh --zookeeper host:port/path --describe
// 特定topic
kafka-topics.sh --zookeeper host:port/path --describe --topic test_topic

example:
  kafka-topics.sh --zookeeper 127.0.0.1:2181 --describe
```