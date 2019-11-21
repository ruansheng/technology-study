##Linux用户态与内核态通信的几种方式
```
1.系统调用函数
2.procfs(/proc)
3.sysctl(/proc/sys)
4.sysfs(/sys)
5.netlink 套接口
```

### 1.系统调用函数
```
在用户空间，通过系统调用函数来访问内核空间，比如:malloc()
```

### 2.procfs(/proc)
```
procfs 是 进程文件系统 的缩写，它本质上是一个伪文件系统
因为它不占用外部存储空间，只是占用少量的内存，通常是挂载在 /proc 目录下

我们在该目录下看到的一个文件，实际上是一个内核变量。内核就是通过这个目录，
以文件的形式展现自己的内部信息，相当于 /proc 目录为用户态和内核态之间的交互搭建了一个桥梁，
用户态读写 /proc 下的文件，就是读写内核相关的配置参数。

比如常见的 /proc/cpuinfo、/proc/meminfo 和 /proc/net 就分别提供了 CPU、内存、网络的相关参数。
除此之外，还有很多的参数，如下所示：
# ls /proc/
1     1143  1345  1447  2     2292  29   331   393  44    63    70    76
acpi       diskstats    irq          locks         sched_debug    sysvipc            zoneinfo
10    1145  1357  148   20    23    290  332   396  442   64

可以看到，这里面有很多的数字表示的文件，这些其实是当前系统运行的进程文件，数字表示进程号（PID），
每个文件包含该进程所有的配置信息，包括进程状态、文件描述符、内存映射等等，我们可以看下：
# ls /proc/1/
attr/            cmdline          environ          io               mem              ns/
pagemap          schedstat        stat             timers
autogroup        comm             exe              limits           mountinfo        numa_maps
personality      sessionid        statm            uid_map
auxv             coredump_filter  fd/              loginuid         mounts           oom_adj
projid_map       setgroups        status           wchan
cgroup           cpuset           fdinfo/          map_files/       mountstats       oom_score
root/            smaps            syscall
clear_refs       cwd/             gid_map          maps             net/             oom_score_adj
sched            stack            task/
```

### 3.sysctl(/proc/sys)
```
我们熟悉的 sysctl 是一个 Linux 命令，它主要是被用来修改内核的运行时参数，换句话说，它可以在内核运行过程中，动态修改内核参数。

它本质上还是用到了文件的读写操作，来完成用户态和内核态的通信。它使用的是 /proc 的一个子目录 /proc/sys。和 procfs 的区别在于：
procfs 主要是输出只读数据，而 sysctl 输出的大部分信息是可写的。

例如，我们比较常见的是通过 cat /proc/sys/net/ipv4/ip_forward 来获取内核网络层是否允许转发 IP 数据包，
通过 echo 1 > /proc/sys/net/ipv4/ip_forward 或者 sysctl -w net.ipv4.ip_forward=1 来设置内核网络层允许转发 IP 数据包。
同样的操作，Linux 也提供了文件 /etc/sysctl.conf 来让你进行批量修改。
```

### 4.sysfs(/sys)
```
sysfs 是 Linux 2.6 才引入的一种虚拟文件系统，它的做法也是通过文件 /sys 来完成用户态和内核的通信。
和 procfs 不同的是，sysfs 是将一些原本在 procfs 中的，关于设备和驱动的部分，独立出来，以 “设备树” 的形式呈现给用户。

sysfs 不仅可以从内核空间读取设备和驱动程序的信息，也可以对设备和驱动进行配置。

# ls /sys
block  bus  class  dev  devices  firmware  fs  hypervisor  kernel  module  power
```

### 5.netlink 套接口
```
netlink 是 Linux 用户态与内核态通信最常用的一种方式。Linux kernel 2.6.14 版本才开始支持。
它本质上是一种 socket，常规 socket 使用的标准 API，在它身上同样适用。比如创建一个 netlink socket，可以调用如下的 socket 函数：
#include <asm/types.h>
#include <sys/socket.h>
#include <linux/netlink.h>

netlink_socket = socket(AF_NETLINK, socket_type, netlink_family);

netlink 这种灵活的方式，使得它可以用于内核与多种用户进程之间的消息传递系统，比如路由子系统，防火墙（Netfilter），ipsec 安全策略等等。
```