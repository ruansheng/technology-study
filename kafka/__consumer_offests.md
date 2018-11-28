## __consumer_offests Topic
__consumer__offsets是一个内部topic，对用户而言是透明的，除了它的数据文件以及偶尔在日志中出现这两点之外，用户一般是感觉不到这个topic的。不过我们的确知道它保存的是Kafka新版本consumer的位移信息

### __consumer_offests 何时被创建
```
__consumer_offsets创建的时机有很多种，主要包括：

broker响应FindCoordinatorRequest请求时
broker响应MetadataRequest显式请求__consumer_offsets元数据时
其中以第一种最为常见，而第一种时机的表现形式可能有很多，比如用户启动了一个消费者组(下称consumer group)进行消费或调用kafka-consumer-groups --describe等
```

### __consumer_offests 的副本因子
```
__consumer_offest不受server.properties中num.partitions和default.replication.factor参数的制约。
相反地，它的分区数和备份因子分别由offsets.topic.num.partitions和offsets.topic.replication.factor参数决定。
这两个参数的默认值分别是50和1，表示该topic有50个分区，副本因子是1。鉴于位移和group元数据等信息都保存在该topic中，
实际使用过程中很多用户都会将offsets.topic.replication.factor设置成大于1的数以增加可靠性，这是推荐的做法。
不过在0.11.0.0之前，这个设置是有缺陷的：假设你设置了offsets.topic.replication.factor = 3，只要Kafka创建该topic时可用broker数<3，
那么创建出来的__consumer_offsets的备份因子就是2。也就是说Kafka并没有尊重我们设置的offsets.topic.replication.factor参数。
好在这个问题在0.11.0.0版本得到了解决，现在用户在使用时，一旦需要创建__consumer_offsets了Kafka一定会保证凑齐足量的broker才会开始创建，否则就抛出异常给用户。
```