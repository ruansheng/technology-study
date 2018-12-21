### systemd
```
systemd 是 Linux 下的一款系统和服务管理器，兼容 SysV 和 LSB 的启动脚本。
systemd 的特性有：支持并行化任务；同一时候採用 socket 式与 D-Bus 总线式激活服务；按需启动守护进程（daemon）。
利用 Linux 的 cgroups 监视进程；支持快照和系统恢复。维护挂载点和自己主动挂载点。各服务间基于依赖关系进行精密控制。
检视和控制systemd的主要命令是systemctl

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