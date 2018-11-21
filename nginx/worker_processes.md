## Nginx 进程
在Nginx运行的时候，会启动两种进程，一种是主进程master process；一种是工作进程worker process。例如我在配置文件中将worker_processes设置为4，
这时会启动1个master process + 4个worker process

## 查看Linux CPU数
```
查看Linux可查看逻辑CPU个数及总核数
grep processor /proc/cpuinfo|wc -l

查看物理CPU总颗数
grep 'physical id' /proc/cpuinfo|sort|uniq|wc -l

每个物理CPU中Core的个数：
cat /proc/cpuinfo | grep "cpu cores" | wc -l

通过执行top命令，然后按数字1，即可显示所有的CPU核数
%Cpu0  :  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu1  :  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu2  :  0.0 us,  0.3 sy,  0.0 ni, 99.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu3  :  0.0 us,  0.3 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

## worker_processes
worker_processes用来指定操作系统启动多少个Nginx工作进程运行，据官方说法，一般开一个就够了，多开几个，可以减少机器io带来的影响
```
// 手动配置nginx worker个数，默认不配置是1
worker_processes 4;

// 可以配置为默认，这样启动的时候就会自动获取CPU内核数，启动相应的nginx worker数，这个auto是在nginx 1.2.x版本之后才支持的
worker_processes auto;
```

## worker_cpu_affinity
用来绑定nginx进程到特定CPU，默认情况下，nginx的进程跑在某一个CPU或CPU的某一个核上，导致nginx进程使用硬件的资源不均，worker_cpu_affinity绑定可以充分有效的利用有效的硬件资源
worker_processes    4;
worker_cpu_affinity 0001 0010 0100 1000;
