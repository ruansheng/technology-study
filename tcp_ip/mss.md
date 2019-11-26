## MSS (Max Segment Size TCP最大段大小)
![TCP Option](https://github.com/ruansheng/technology-study/blob/master/images/mtu_mss.jpg)
```

MSS是在TCP初始建立连接时由网卡MTU确定并和对端协商的

TCP为了避免被发送方分片，会主动把数据分割成小段再交给网络层，最大的分段大小称之为MSS
MSS = MTU - IP header头的大小 - TCP 头的大小
这样一个MSS的数据就恰好能装进一个MTU而不用分片了

在以太网中TCP的 MSS = 1500(MTU) - 20 (IP头大小) - 20 (TCP头大小) = 1460
```
