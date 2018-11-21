## Topic配置
Kafka为Topic提供了很多默认配置参数，可以通过管理工具为每个topic单独配置一部分参数，比如分区个数和数据保留策略
服务器提供的默认配置可以作为基准，他们适用于大部分topic

### num.partitions
指定新创建的topic包含多少个分区，默认值是1

### log.retention.ms,log.retention.minutes,log.retention.hour
Kafka通常根据时间来决定数据可以被保留多久。默认使用log.retention.hour参数来配置时间，默认值是168小时，也就是一周
这三个参数都是决定消息多久以后被删除，推荐使用log.retention.ms。如果指定了不止一个参数，Kafka会优先使用具有最小值得参数
原理:通过文件最后一条消息的时间戳来作为依据的，如果使用管理工具在不同服务器间移动分区，这个时间就可能出现误差

### log.retention.bytes
另一种方式是通过保留消息字节数来判断消息是否过期的，通过log.retention.bytes来指定作用在每个分区上，如果一个topic的
分区增多时，整个topic可以保存的数据是随之增加的
如果同时设置了log.retention.ms和log.retention.bytes两种策略，那么只要满足其中一种，数据就会被删除

### log.segment.bytes
当消息到达broker时，消息会被追加到分区的当前日志片段上，当日志片段大小达到log.segment.bytes指定的上限(默认是1G)时，
当前日志片段就会关闭，等待过期，然后往新的日志片段文件写入

### log.segment.ms
另一个可有控制日志片段关闭时间的参数是log.segment.ms，指定了日志片段多长时间之后会被关闭
log.retention.bytes和log.segment.ms参数之间存存在互斥问题，看哪个条件满足就执行哪个条件

### message.max.bytes
broker通过message.max.bytes来限制单个消息的大小，默认值是1000000，也就是1M，如果超过这个大小，broker不会接收消息，反而会返回错误信息
这个参数值是指压缩后的消息大小