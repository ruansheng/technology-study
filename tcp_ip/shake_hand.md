## 三次握手
![TCP Option](https://github.com/ruansheng/technology-study/blob/master/images/shake_hand.jpg)
```
三次握手最重要的是交换彼此的ISN(初始序列号)
除了交换彼此的初始序列号，三次握手的另一个重要作用是交换一下辅助信息:
  比如最大段大小(MSS)
  窗口大小(Win)
  窗口缩放因子(WS)
  是否支持选择确认(SACK_PERM)
```

### 握手过程
```
第一次握手: 建立连接时，客户端发送syn包（syn=x）到服务器，并进入SYN_SENT状态，等待服务器确认；SYN：同步序列编号（Synchronize Sequence Numbers）。
          SYN报文不携带数据，但是占用一个序列号
第二次握手: 服务器收到syn包，必须确认客户的SYN（ack=x+1），同时自己也发送一个SYN包（syn=y），即SYN+ACK包，此时服务器进入SYN_RECV状态；
第三次握手: 客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK(ack=y+1），此包发送完毕，客户端和服务器进入ESTABLISHED（TCP连接成功）状态，完成三次握手。

注意: 凡是消耗序列号的TCP报文段，一定需要对端确认，如果这个段没收到确认，会一直重传直到达到指定的次数为止
```
