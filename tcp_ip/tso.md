## 网卡特性TSO、UFO、GSO、LRO、GRO
```
TSO(TCP Segmentation Offload)，是利用网卡对TCP数据包分片，减轻CPU负荷的一种技术，也有人叫 LSO (Large segment offload) ，TSO是针对TCP的，UFO是针对UDP的。
如果硬件支持 TSO功能，同时也需要硬件支持的TCP校验计算和分散/聚集 (Scatter Gather) 功能。
如果网卡支持TSO/GSO，可以把最多64K大小的TCP payload直接往下传给协议栈，此时IP层也不会进行segmentation，网卡会生成TCP/IP包头和帧头，这样可以offload很多协议栈上的内存操作，节省CPU资源
当然如果都是小包，那么功能基本就没啥用了

新GSO ->  旧TSO、UFO    在发送方向上的
新GRO ->  旧LRO         在接收方向上的
```

### ethtool
```
在系统中可以通过ethtool命令来进行查看
#ethtool -k eth0
generic-segmentation-offload: on
generic-receive-offload: on
```