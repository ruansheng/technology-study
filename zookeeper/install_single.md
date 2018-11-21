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
#maxClientCnxns=60
#autopurge.snapRetainCount=3
#autopurge.purgeInterval=1
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

## 停止
```
# cd bin
# sh zkServer.sh stop
ZooKeeper JMX enabled by default
Using config: /Users/ruansheng/Downloads/zookeeper-3.4.13/bin/../conf/zoo.cfg
-n Stopping zookeeper ...
STOPPED
```