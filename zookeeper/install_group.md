## 安装zookeeper
下载zookeeper.tar.gz包

## 配置
在zookeeper/conf目录中copy zoo_sample.cfg，按照需要进行修改
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/tmp/zookeeper
clientPort=2181
# server.X = hostname:peerPort:leaderPort格式
  # X 服务器ID，它必须是一个整数,不过不一定要从0开始,也不要求是连续的
  # hostname 服务器的机器名或IP地址
  # peerPort 用于节点间通信的TCP端口
  # leaderPort 用于首领选举的TCP端口
server.1=zook1.example.com:2888:3888
server.2=zook2.example.com:2888:3888
server.3=zook3.example.com:2888:3888
#maxClientCnxns=60
#autopurge.snapRetainCount=3
#autopurge.purgeInterval=1
```
在dataDir下面创建myid文件
```
文件中包含服务器ID，这个ID要与配置文件中的ID保持一致
```
zookeeper端口说明:
```
2181：对cline端提供服务
3888：选举leader使用
2888：集群内机器通讯使用（Leader监听此端口）
```

## 启动zk
```
# cd bin
# sh zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /Users/ruansheng/Downloads/zookeeper-3.4.13/bin/../conf/zoo.cfg
-n Starting zookeeper ...
STARTED
```

## 验证启动成功
```
# telnet localhost 2181
Trying ::1...
Connected to localhost.
Escape character is '^]'.
srvr
Zookeeper version: 3.4.13-2d71af4dbe22557fda74f9a9b4309b15a7487f03, built on 06/29/2018 04:05 GMT
Latency min/avg/max: 0/0/0
Received: 1
Sent: 0
Connections: 1
Outstanding: 0
Zxid: 0x0
Mode: standalone
Node count: 4
Connection closed by foreign host.
```

## 查看状态
```
# sh zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /Users/ruansheng/Downloads/zookeeper/zookeeper-3.4.13-3/bin/../conf/zoo.cfg
Mode: follower
```

## 停止
```
# cd bin
# sh zkServer.sh stop
ZooKeeper JMX enabled by default
Using config: /Users/ruansheng/Downloads/zookeeper-3.4.13/bin/../conf/zoo.cfg
-n Stopping zookeeper ...
STOPPED
```
