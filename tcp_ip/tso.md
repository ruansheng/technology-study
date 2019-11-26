## 网卡特性TSO、UFO、GSO、LRO、GRO
```
TSO(TCP Segmentation Offload)，是利用网卡对TCP数据包分片，减轻CPU负荷的一种技术，也有人叫 LSO (Large segment offload) ，TSO是针对TCP的，UFO是针对UDP的。
如果硬件支持 TSO功能，同时也需要硬件支持的TCP校验计算和分散/聚集 (Scatter Gather) 功能。
如果网卡支持TSO/GSO，可以把最多64K大小的TCP payload直接往下传给协议栈，此时IP层也不会进行segmentation，网卡会生成TCP/IP包头和帧头，这样可以offload很多协议栈上的内存操作，节省CPU资源
当然如果都是小包，那么功能基本就没啥用了

新GSO ->  旧TSO、UFO    在发送方向上的
新GRO ->  旧LRO         在接收方向上的

在不支持TSO的网卡上，TCP层向IP层发送数据会考虑mss，使得TCP向下发送的数据可以包含在一个IP分组中而不会造成分片

网卡支持TSO时，TCP层会逐渐增大mss（总是整数倍数增加），当TCP层向下发送大块数据时，仅仅计算TCP头，
网卡接到到了IP层传下的大数 据包后自己重新分成若干个IP数据包，添加IP头，复制TCP头并且重新计算校验和等相关数据，这样就把一部分CPU相关的处理工作转移到由网卡来处理。
```

### ethtool
```
在系统中可以通过ethtool命令来进行查看
#ethtool -k eth0
generic-segmentation-offload: on
generic-receive-offload: on
```