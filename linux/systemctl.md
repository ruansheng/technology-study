### systemd
```
systemd 是 Linux 下的一款系统和服务管理器，兼容 SysV 和 LSB 的启动脚本。
systemd 的特性有：支持并行化任务；同一时候採用 socket 式与 D-Bus 总线式激活服务；按需启动守护进程（daemon）。
利用 Linux 的 cgroups 监视进程；支持快照和系统恢复。维护挂载点和自己主动挂载点。各服务间基于依赖关系进行精密控制。
检视和控制systemd的主要命令是systemctl

systemd是init系统的1号进程
# ps -ef | grep systemd
root  1  0  0 10月15 ?   00:00:25 /usr/lib/systemd/systemd --switched-root --system --deserialize 22

全部可用的单元文件存放在:
/usr/lib/systemd/system/ 和 /etc/systemd/system/ 文件夹（后者优先级更高）。
```

### systemctl --version
```
查看systemd版本
# systemctl --version
systemd 219
+PAM +AUDIT +SELINUX +IMA -APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 -SECCOMP +BLKID +ELFUTILS +KMOD +IDN
```

### systemctl status
```
显示 系统状态
# systemctl status
● a7120ea04fce
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: Wed 2018-12-19 17:13:55 CST; 24h ago
   CGroup: /docker/a7120ea04fced2756ea3ddcb1a3535132dfe2778a6301a728f2de33f697ab
b29
           ├─1 /usr/sbin/init
           └─system.slice
             ├─dbus.service
             │ └─55 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nop
idfile --systemd-activation
             ├─squid.service
             │ ├─215 /usr/sbin/squid -f /etc/squid/squid.conf
             │ ├─217 (squid-1) -f /etc/squid/squid.conf
             │ └─218 (logfile-daemon) /var/log/squid/access.log
             ├─system-getty.slice
             │ └─getty@tty1.service
             │   └─62 /sbin/agetty --noclear tty1 linux
             ├─systemd-logind.service
             │ └─56 /usr/lib/systemd/systemd-logind
             ├─systemd-udevd.service
             │ └─26 /usr/lib/systemd/systemd-udevd
             └─systemd-journald.service
               └─17 /usr/lib/systemd/systemd-journald
```

### systemctl list-unit-files
```
查看全部已安装服务
# systemctl list-unit-files
UNIT FILE                              STATE
proc-sys-fs-binfmt_misc.automount      static
dev-hugepages.mount                    static
dev-mqueue.mount                       static
proc-sys-fs-binfmt_misc.mount          static
sys-fs-fuse-connections.mount          static
sys-kernel-config.mount                static
sys-kernel-debug.mount                 static
tmp.mount                              disabled
systemd-ask-password-console.path      static
systemd-ask-password-wall.path         static
arp-ethers.service                     disabled
autovt@.service                        enabled
...
```

### systemctl list-units
```
输出激活的单元
#systemctl list-units
等效于
#systemctl
```

### systemctl --failed
```
输出执行失败的单元
```

### 单元
```
一个单元配置文件能够描写叙述例如以下内容之中的一个：
系统服务（.service）、挂载点（.mount）、sockets（.sockets） 、系统设备（.device）、交换分区（.swap）、文件路径（.path）、启动目标（.target）、由 systemd 管理的计时器（.timer）

使用 systemctl 控制单元时，通常须要使用单元文件的全名，包括扩展名（比如 sshd.service）。可是有些单元能够在systemctl中使用简写方式。
  1. 假设无扩展名，systemctl 默认把扩展名当作 .service。 比如 netcfg 和 netcfg.service 是等价的。
  2. 挂载点会自己主动转化为对应的 .mount 单元。比如 /home 等价于 home.mount。
  3. 设备会自己主动转化为对应的 .device 单元，所以 /dev/sda2 等价于 dev-sda2.device。
```

### 单元操作命令
```
激活单元:
systemctl start <单元>

停止单元:
systemctl stop <单元>

重新启动单元:
systemctl restart <单元>

重新载入配置:
systemctl reload <单元>

输出单元执行状态:
systemctl status <单元>

检查单元是否配置为自己主动启动:
systemctl is-enabled <单元>

开机自己主动激活单元:
systemctl enable <单元>

取消开机自己主动激活单元:
systemctl disable <单元>

禁用一个单元(禁用后，间接启动也是不可能的):
systemctl mask <单元>

取消禁用一个单元:
systemctl unmask <单元>

显示单元的手冊页（必须由单元文件提供）:
systemctl help <单元>

重新载入 systemd，扫描新的或有变动的单元
systemctl daemon-reload
```

### unit种类
```
systemd 单元文件的语法来源于 XDG桌面入口配置文件.desktop文件。最初的源头则是Microsoft Windows的.ini文件。

Red Hat Enterprise Linux 7（RHEL 7）已经将服务管理工具从SysVinit和Upstart迁移到了systemd上，相应的服务脚本也需要改变。
前面的版本里，所有的启动脚本都是放在/etc/rc.d/init.d/ 目录下。这些脚本都是bash脚本，可以让系统管理员控制这些服务的状态，
通常，这些脚本中包含了start，stop，restart这些方法，以提供系统自动调用这些方法。但是在RHEL 7中当中已经完全摒弃了这种方法，而采用了一种叫unit的配置文件来管理服务。

unit 类型如下：
service ：守护进程的启动、停止、重启和重载是此类 unit 中最为明显的几个类型。
socket ：此类 unit 封装系统和互联网中的一个socket。当下，systemd支持流式, 数据报和连续包的AF\_INET,AF\_INET6,AF\_UNIXsocket 。也支持传统的 FIFOs 传输模式。每一个 socket unit 都有一个相应的服务 unit 。相应的服务在第一个“连接”进入 socket 或 FIFO 时就会启动(例如：nscd.socket 在有新连接后便启动 nscd.service)。
device ：此类 unit 封装一个存在于 Linux设备树中的设备。每一个使用 udev 规则标记的设备都将会在 systemd 中作为一个设备 unit 出现。udev 的属性设置可以作为配置设备 unit 依赖关系的配置源。
mount ：此类 unit 封装系统结构层次中的一个挂载点。
automount ：此类 unit 封装系统结构层次中的一个自挂载点。每一个自挂载 unit 对应一个已挂载的挂载 unit (需要在自挂载目录可以存取的情况下尽早挂载)。
target ：此类 unit 为其他 unit 进行逻辑分组。它们本身实际上并不做什么，只是引用其他 unit 而已。这样便可以对 unit 做一个统一的控制。(例如：multi-user.target 相当于在传统使用 SysV 的系统中运行级别5)；bluetooth.target 只有在蓝牙适配器可用的情况下才调用与蓝牙相关的服务，如：bluetooth 守护进程、obex 守护进程等）
snapshot ：与 targetunit 相似，快照本身不做什么，唯一的目的就是引用其他 unit 。
```

### unit文件
```
单元文件能够从两个地方载入。优先级从低到高各自是：
   1. /usr/lib/systemd/system/: 软件包安装的单元
   2. /etc/systemd/system/: 系统管理员安装的单元
当systemd执行在用户模式下时，使用的载入路径是全然不同的。
systemd 单元名仅能包括 ASCII 字符, 下划线和点号. 其它字符须要用 C-style “\x2d” 替换

[Unit]
Description=Network Manager  # 对本service的描述
Wants=network.target  #推荐使用。本单元启动了，它“想要”的单元也会被启动。但是启动不成功，对本单元没有影响。
Before=network.target network.service # 定义启动顺序，Before=xxx.service，代表本服务在xxx.service启动之前启动。After=xxx.service,代表本服务在xxx之后启动。
# Conflicts：一个单元的启动会停止与它“冲突”的单元，反之亦然。
# Requires: 这个单元启动了，那么它“需要”的单元也会被启动; 它“需要”的单元被停止了，它自己也活不了。但是请注意，这个设定并不能控制某单元与它“需要”的单元的启动顺序（启动顺序是另外控制的），即 Systemd 不是先启动 Requires 再启动本单元，而是在本单元被激活时，并行启动两者。于是会产生争分夺秒的问题，如果 Requires 先启动成功，那么皆大欢喜; 如果 Requires 启动得慢，那本单元就会失败（Systemd 没有自动重试）。所以为了系统的健壮性，不建议使用这个标记，而建议使用 Wants 标记。可以使用多个 Requires。
# RequiresOverridable：跟 Requires 很像。但是如果这条服务是由用户手动启动的，那么 RequiresOverridable 后面的服务即使启动不成功也不报错。跟 Requires 比增加了一定容错性，但是你要确定你的服务是有等待功能的。另外，如果不由用户手动启动而是随系统开机启动，那么依然会有 Requires 面临的问题
# Requisite：强势版本的 Requires。要是这里需要的服务启动不成功，那本单元文件不管能不能检测等不能等待都立刻就会失败。
                                                                                                                                                                             
[Service]
Type=dbus  #service的种类，包含下列几种类型
BusName=org.freedesktop.NetworkManager
ExecStart=/usr/sbin/NetworkManager --no-daemon  # 服务启动时执行的命令，通常此命令就是服务的主体
# NM doesn't want systemd to kill its children for it
KillMode=process
# ExecStartPre, ExecStartPost：ExecStart执行前后所调用的命令。
# ExecStop：定义停止服务时所执行的命令，定义服务退出前所做的处理。如果没有指定，使用systemctl stop xxx命令时，服务将立即被终结而不做处理。
# Restart：定义服务何种情况下重启（启动失败，启动超时，进程被终结）。可选选项：no, on-success, on-failure,on-watchdog, on-abort
# SuccessExitStatus：参考ExecStart中返回值，定义何种情况算是启动成功  eg：SuccessExitStatus=1 2 8 SIGKILL


[Install]
WantedBy=multi-user.target  # 何种情况下，服务被启用 多用户环境下启用）
Alias=dbus-org.freedesktop.NetworkManager.service  # 别名
Also=NetworkManager-dispatcher.service

整个文件分三个部分，[Unit]·[Service]·[Install]
[Unit]：记录unit文件的通用信息。
[Service]：记录Service的信息
[Install]：安装信息。

编写自己定义的 service 文件时，能够选择几种不同的服务启动方式。启动方式可通过配置文件 [Service] 段中的 Type= 參数进行设置。

Type=simple（默认值）：systemd觉得该服务将马上启动。服务进程不会fork。
假设该服务要启动其它服务，不要使用此类型启动。除非该服务是socket激活型。
Type=forking：systemd觉得当该服务进程fork，且父进程退出后服务启动成功。对于常规的守护进程（daemon），除非你确定此启动方式无法满足需求。使用此类型启动就可以。使用此启动类型应同一时候指定 PIDFile=，以便systemd能够跟踪服务的主进程。
Type=oneshot：这一选项适用于仅仅执行一项任务、随后马上退出的服务。可能须要同一时候设置 RemainAfterExit=yes 使得 systemd 在服务进程退出之后仍然觉得服务处于激活状态。
Type=notify：与 Type=simple 相同，但约定服务会在就绪后向 systemd 发送一个信号。
这一通知的实现由 libsystemd-daemon.so 提供。
Type=dbus：若以此方式启动。当指定的 BusName 出如今DBus系统总线上时。systemd觉得服务就绪。
Type=idle: systemd会等待全部任务处理完毕后。才開始执行idle类型的单元。
其它行为和Type=simple 相似。
```

### service命令与systemd命令的映射
```
service name start    ---->  systemctl start name.service   ●Starts a service.
service name stop   ---->  systemctl stopname.service   ●Stops a service.
service name restart  ---->  systemctl restartname.service  ●Restarts a service.
service name condrestart ---->
systemctl try-restart name.service ●Restarts a service only if it is running.
service name reload  ---->  systemctl reloadname.serviceReloads configuration.
service name status ---->  systemctl status name.service
systemctl is-active name.service
●Checks if a service isrunning.
service --status-all  ----> systemctl list-units –type service --all
●Displays the status of all services.chkconfig systemctl
chkconfig name on  ---->   systemctl enablename.service ●Enables a service.
chkconfig name off   ---->   systemctl disablename.service ●Disables a service.
chkconfig --list name  ---->  systemctl statusname.service
system ctl is-enabled name.service
●Checks if a service is enabled.
chkconfig --list  ----> systemctl list-unit-files –type service
●Lists all services and checks if they are enabled.
```

### 创建自己的systemd服务
```
Step1：编写属于自己的unit文件，命令为my-demo.service，整个文件如下:
[Unit]
Description=My-demo Service                                                                                                                             

[Service]
Type=oneshot
ExecStart=/bin/bash /root/test.sh
StandardOutput=syslog
StandardError=inherit 

[Install]
WantedBy=multi-user.target

Step2：将上述的文件拷贝到RHEL 7系统中/usr/lib/systemd/system/*目录下

Step3：编写unit文件中ExecStart=/bin/bash /root/test.sh所定义的test.sh文件，将其放在定义的目录当中，此文件是服务的执行主体。文件内容如下：
#!/bin/bash
date >> /tmp/date

Step4：将my-demo.service注册到系统当中执行命令：
# systemctl enable my-demo.service
输出：ln -s'/usr/lib/systemd/system/my-demo.service' '/etc/systemd/system/multi-user.target.wants/my-demo.service'
输出表明，注册的过程实际上就是将服务链接到/etc/systemd/system/目录下。
```