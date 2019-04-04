### 平均负载
```
单位时间内，系统处于可运行状态和不可中断状态的进程数，可简单理解为系统平均活跃进程数

可运行状态：top命令状态为R，包括Runnable和Running
不可中断状态：top命令状态为D，正处于内核态关键流程中的进程，并且这些流程是不可打断的，比如等待硬件设备的I/O响应，这往往是对数据一致性的一种保护机制
```

### 如何查看平均负载
```
$ uptime
...,  load average: 0.20, 0.15, 0.19
$ top
...,  load average: 0.20, 0.15, 0.19
$ w
...,  load average: 0.20, 0.15, 0.19
$  watch -d uptime // 监控平均负载，默认2s刷新一次
有uptime、top、w等命令查看系统平均负载，后面三个数据分别系统过去1分钟、5分钟、15分钟的平均负载
```

### 平均负载与cpu使用率关系
```
前面提到，计算平均负载的进程包括R和D两种状态进程，所有平均负载高可能有Cpu使用率高或者IO wait两种情况引起
简单理解如下：

CPU密集型：程序大量使用cpu，此时两种指标效果一直
IO密集型：程序I/O等待导致平均负载升高，此时cpu使用率不一定高
大量等待 CPU 的进程调度也会导致平均负载升高，此时的 cpu使用率一般也会比较高
```