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

