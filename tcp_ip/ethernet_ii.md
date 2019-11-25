## 以太网帧格式
![TCP Option](https://github.com/ruansheng/technology-study/blob/master/images/ethernet_ii.jpg)
```
以太网数据帧的长度在64-1518字节之间

Ethernet_II的帧中各字段说明如下：

DMAC（DestinationMAC）是目的MAC地址。DMAC字段长度为6个字节，标识帧的接收者。
SMAC（SourceMAC）是源MAC地址。SMAC字段长度为6个字节，标识帧的发送者。
类型字段（Type）用于标识数据字段中包含的高层协议，该字段长度为2个字节。类型字段取值为0x0800的帧代表IP协议帧；类型字段取值为0806的帧代表ARP协议帧。
数据字段(Data)是网络层数据，最小长度必须为46字节以保证帧长至少为64字节，数据字段的最大长度为1500字节。
循环冗余校验字段（FCS）提供了一种错误检测机制。该字段长度为4个字节。
```
