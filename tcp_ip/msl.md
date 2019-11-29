## MSL (Maximum Segment Lifetime 报文最大生存时间)

### 查看linux默认MSL
```
默认是60s
cat /proc/sys/net/ipv4/tcp_fin_timeout
```

### 修改linux的MSL
```
echo 120 > /proc/sys/net/ipv4/tcp_fin_timeout

重新加载配置文件
sysctl -p /etc/sysctl.conf

查看是否已经生效
sysctl -a | grep fin
```

### TIME_WAIT
```
主动关闭连接的peer会进入TIME_WAIT状态。TIME_WAIT通常又叫做2MSL
```

### TIME_WAIT存在的意义
```
1.防止上一次连接的报文出现在新连接中。经过2MSL的等待时间，这些报文都已失效（超过生存周期）。
2.如果没有TIME_WAIT，主动关闭方将直接进入CLOSED状态，如果对端没有收到最后我们最后发出的ACK报文，将重发FIN，此时我们已进入CLOSED，就会响应RST而非ACK，这将导致对端非正常关闭连接。
```
