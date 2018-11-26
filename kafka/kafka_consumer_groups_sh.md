## kafka-consumer-groups.sh
在kafka里，有2个地方保存着消费者群组的信息
旧版本:保存在zk里
新版本:保存在broker里

### 旧版本
```
kafka-consumer-groups.sh --zookeeper host:port/path --list groupid
```

### 新版本
```
kafka-consumer-groups.sh --new-consumer --bootstrap-server host:port/path --list groupid
```

