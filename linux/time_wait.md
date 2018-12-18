### time_wait
```
客户端与服务器端建立TCP/IP连接后关闭SOCKET后，服务器端连接的端口状态为TIME_WAIT.主动关闭的一方在发送最后一个 ack 后
就会进入 TIME_WAIT 状态 停留2MSL（max segment lifetime）时间

1、防止上一次连接中的包，迷路后重新出现，影响新连接（经过2MSL，上一次连接中所有的重复包都会消失）

2、可靠的关闭TCP连接
在主动关闭方发送的最后一个 ack(fin) ，有可能丢失，这时被动方会重新发fin, 如果这时主动方处于 CLOSED 状态 ，就会响应 rst 而不是 ack。所以主动方要处于 TIME_WAIT 状态，而不能是 CLOSED
```

#### 系统本地可用端口极限值
```
# cat /proc/sys/net/ipv4/ip_local_port_range
32768 61000

说明这台机器本地能向外连接61000-32768=28232个连接，注意是本地向外连接，
不是这台机器的所有连接，不会影响这台机器的80端口的对外连接数。
但这个数字会影响到代理服务器（nginx）对app服务器的最大连接数，
因为nginx对app是用的异步传输，所以这个环节的连接速度很快，所以堆积的连接就很少。
假如nginx对app服务器之间的带宽出了问题或是app服务器有问题，那么可能使连接堆积起来，
这时可以通过设定nginx的代理超时时间，来使连接尽快释放掉，一般来说极少能用到28232个连接。
```

#### 解决办法
```
目前看来最好的办法是让每个TIME_WAIT早点过期

#表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭
net.ipv4.tcp_tw_reuse = 0

#表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭
net.ipv4.tcp_tw_recycle = 0

#表示如果套接字由本端要求关闭，这个参数决定了它保持在FIN-WAIT-2状态的时间（可改为30，一般来说FIN-WAIT-2的连接也极少）
net.ipv4.tcp_fin_timeout = 60

修改方式一:
vim /etc/sysctl.conf
/sbin/sysctl -p

修改方式二:
echo "1" > /proc/sys/net/ipv4/tcp_tw_reuse
echo "1" > /proc/sys/net/ipv4/tcp_tw_recycle
echo "12" > /proc/sys/net/ipv4/tcp_fin_timeout
```