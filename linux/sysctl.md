### sysctl
```
sysctl命令用于运行时配置内核参数，这些参数位于/proc/sys目录下
sysctl配置与显示在/proc/sys目录中的内核参数．可以用sysctl来设置或重新设置联网功能，如IP转发、IP碎片去除以及源路由检查等。
用户只需要编辑/etc/sysctl.conf文件，即可手工或自动执行由sysctl控制的功能。
```

#### sysctl -a
```
显示所有系统参数

例如:
#sysctl -a
abi.vsyscall32 = 1
crypto.fips_enabled = 0
debug.exception-trace = 1
debug.kprobes-optimization = 1
debug.panic_on_rcu_stall = 0
dev.hpet.max-user-freq = 64
...
```

### sysctl -w
```
临时改变某个指定参数的值
sysctl -w net.ipv4.ip_forward=1

1) #echo 1 > /proc/sys/net/ipv4/ip_forward
2) #sysctl -w net.ipv4.ip_forward=1
以上两种方法都可能立即开启路由功能，但如果系统重启，或执行了service network restart命令，所设置的值即会丢失
如果想永久保留配置，可以修改/etc/sysctl.conf文件
```

### sysctl -p
```
从指定的文件加载系统参数，如不指定即从/etc/sysctl.conf中加载

sysctl -p
或者
sysctl -p /etc/sysctl.conf
```

### sysctl.conf 和 /proc/sys 的关系
```
RedHat提供了非常好的方法，使我们可以在系统运行时更改内核参数，而不需要重新引导系统。这是通过/proc虚拟文件系统实现的。
/proc/sys目录下存放着大多数的内核参数，并且设计成可以在系统运行的同时进行更改, 不过重新启动机器后会失效，
可以通过更改/proc/sys中内核参数对应的文件 /etc/sysctl.conf 的内核参数来永久更改

/proc/sys下内核文件与配置文件 sysctl.conf中变量的对应关系，
由于可以修改的内核参数都在/proc/sys目录下，所以sysctl.conf的变量名省略了目录的前面部分（/proc/sys）
将/proc/sys中的文件转换成sysctl中的变量依据下面两个简单的规则：
　　1．去掉前面部分/proc/sys
　　2．将文件名中的斜杠变为点
　　这两条规则可以将/proc/sys中的任一文件名转换成sysctl中的变量名。
```