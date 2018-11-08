## worker_rlimit_nofile
worker_rlimit_nofile为nginx工作进程改变打开最多文件描述符数目的限制。用来在不重启主进程的情况下增加限制。
理论上这个值是最多打开文件数（ulimit -n）与nginx工作进程相除

## 配置worker_rlimit_nofile
```
worker_rlimit_nofile 100000;
默认值是 ulimit -n
```

## 查看是否生效
从下面可以看出worker_rlimit_nofile的值默认是ulimit -n
```
# ulimit -n
1048576

#cat /proc/90/limits
Limit                     Soft Limit           Hard Limit           Units
Max cpu time              unlimited            unlimited            seconds
Max file size             unlimited            unlimited            bytes
Max data size             unlimited            unlimited            bytes
Max stack size            8388608              unlimited            bytes
Max core file size        unlimited            unlimited            bytes
Max resident set          unlimited            unlimited            bytes
Max processes             1048576              1048576              processes
Max open files            1048576              1048576              files
Max locked memory         65536                65536                bytes
Max address space         unlimited            unlimited            bytes
Max file locks            unlimited            unlimited            locks
Max pending signals       15088                15088                signals
Max msgqueue size         819200               819200               bytes
Max nice priority         0                    0
Max realtime priority     0                    0
Max realtime timeout      unlimited            unlimited            us
```
