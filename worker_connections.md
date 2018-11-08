## worker_connections
worker_connections用来配置单个nginx worker进程允许的客户端最大连接数
官方解释如下，个人认为是每一个worker进程能并发处理（发起）的最大连接数（包含所有连接数）。
不能超过最大文件打开数：在linux终端中输入ulimit -a进行查看

## ulimit -n
```
# ulimit -n
65535
```

## 计算方式
```
nginx作为http服务器的时候：
    max_clients = worker_processes * worker_connections
nginx作为反向代理服务器的时候：
    max_clients = worker_processes * worker_connections/2
nginx做反向代理时，和客户端之间保持一个连接，和后端服务器保持一个连接。
```

## 配置worker_connections
```
events { 
    worker_connections 65535; 
}
```

## 查看配置是否生效
注意下面的2291是一个nginx worker的pid
```
# cat /proc/2291/limits
Limit                     Soft Limit           Hard Limit           Units
Max cpu time              unlimited            unlimited            seconds
Max file size             unlimited            unlimited            bytes
Max data size             unlimited            unlimited            bytes
Max stack size            8388608              unlimited            bytes
Max core file size        0                    unlimited            bytes
Max resident set          unlimited            unlimited            bytes
Max processes             3829                 3829                 processes
Max open files            1024                 4096                 files
Max locked memory         65536                65536                bytes
Max address space         unlimited            unlimited            bytes
Max file locks            unlimited            unlimited            locks
Max pending signals       3829                 3829                 signals
Max msgqueue size         819200               819200               bytes
Max nice priority         0                    0
Max realtime priority     0                    0
Max realtime timeout      unlimited            unlimited            us
```
