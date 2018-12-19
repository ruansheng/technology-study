### squid

### Install
```
yum -y install squid

启动:
systemctl enable squid
systemctl start squid

注意:
docker 启动容器的时候需要加一些参数，否则systemctl不可用
docker run --privileged --name test docker-image /usr/sbin/init

查看端口是否监听:
# netstat -luntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
...
tcp6       0      0 :::3128                 :::*                    LISTEN      206/(squid-1)
...
```

### 正向代理
```
1. 添加squid配置文件
#vim /etc/squid/squid.conf
acl badsite dstdomain www.baidu.com
http_access deny badsite

2. 重启squid
systemctl restart squid
# squid -k reconfig 该命令重新加载配置文件

3. 设置浏览器http代理
设置http https 代理 127.0.0.1 3128

4. 请求
https://www.baidu.com
结果请求不通
```
