## Broker配置
有一些配置选项，在单机安装时可以直接使用默认值，但是在部署到其它环境时要格外小心，
有些参数在经过修改后才能用在集群环境

### broker.id
每个broker都需要有一个唯一标识符，使用broker.id表示，默认值是0，也可以设置成其它任意整数
这个值在集群中必须是唯一的

### port
kafka默认会监听port 9092来启动，可以设置成任意的端口，注意:如果设置1024以下的端口，需要以root权限启动

### zookeeper.connect
```
用于保存broker元数据的zookeeper地址，多个zookeeper地址用逗号来分隔，例如:
zookeeper.connect=hostname:port/path,hostname:port/path,hostname:port/path
path是可选的zookeeper路径，作为kafka集群的chroot，如果不指定，默认使用根路径，如果指定的chroot不存在，broker在启动的时候会创建
ZooKeeper连接字符串的格式为：hostname:port，此处hostname和port分别是ZooKeeper集群中某个节点的host和port；为了当某个host宕掉之后你能通过其他ZooKeeper节点进行连接，你可以按照一下方式制定多个hosts：
hostname1:port1, hostname2:port2, hostname3:port3.

ZooKeeper 允许你增加一个“chroot”路径，将集群中所有kafka数据存放在特定的路径下。当多个Kafka集群或者其他应用使用相同ZooKeeper集群时，可以使用这个方式设置数据存放路径。这种方式的实现可以通过这样设置连接字符串格式，如下所示：
hostname1：port1，hostname2：port2，hostname3：port3/chroot/path
这样设置就将所有kafka集群数据存放在/chroot/path路径下。注意，在你启动broker之前，你必须创建这个路径，并且consumers必须使用相同的连接格式。
```

### log.dirs
Kafka把所有消息都保存在磁盘上，存放这些日志片段的目录是通过log.dirs指定的，用逗号分隔的多个本地文件系统路径，
如果指定多个路径，broker会根据"最少使用"原则，把同一个分区的日志片段保存在同一个路径下，broker会往拥有最少数目分区的路径新增分区

### num.recovery.threads.per.data.dir
Kafak会使用可配置的线程池来处理日志片段:
1.服务器正常启动，用于打开每个分区的日志片段
2.服务器崩溃后重启，用于检查和截断每个分区的日志片段
3.服务器正常关闭，用于关闭日志片段
如果log.dir=3，num.recovery.threads.per.data.dir=8，那么总共需要24个线程

### auto.create.topics.enable
默认情况下，Kafka会在如下几种情况下自动创建topic:
1.当一个生产者开始往主题写入消息时
2.当一个消费者开始从主题读取消时
3.当任意一个客户端向主题发送元数据请求时
很多时候，这些行为都是非预期的。而且，如果一个topic不先被创建，根本无法知道它是否存在，如果显式创建topic，
不管是手动创建还是通过其他配置系统来创建，都可以把auto.create.topics.enable设置为false