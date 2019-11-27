## 丢包重传次数
```
TCP丢包重传有次数限制，在Centos上由以下文件内的值决定
#cat /proc/sys/net/ipv4/tcp_syn_retries
6

6次重试(65s=1s+2s+4s+8s+16s+32s)
```