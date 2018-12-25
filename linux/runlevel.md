### runlevel 运行级别
```
RHEL 6 使用的初始化进程  System V init   仅仅做了引导
RHEL 7 使用的初始化进程  systemd  引导、syslogd、udev、cgroup等

RHEL 6 和 RHEL 7 在运行级别上使用了不同的程序，使用起来也不尽相同
```

### RHEL 6 和 RHEL 7 运行级别
|System V init 运行级别           | Systemd目标名称    | 作用    |
| :------| :------: |:------: |
| 0 | runlevel0.target, poweroff.target  | 关机 |
| 1 | runlevel1.target, rescue.target  |单用户模式 |
| 2 | runlevel2.target, multi-user.target  |等同于级别3 |
| 3 | runlevel3.target, multi-user.target  | 多用户的文本界面 |
| 4 | runlevel4.target, multi-user.target  |等同于级别3 |
| 5 | runlevel5.target, graphical.target  |多用户的图形界面 |
| 6 | runlevel6.target, reboot.target  |重启 |
| emergency | emergency.target |紧急Shell |

### RHEL 6 运行级别
```
vim /etc/inittab
将末行 id:3:initdefault: 中的3改成想要设置的就可以
```

### RHEL 7 运行级别
```
查看当前的运行级别:
# systemctl get-default
multi-user.target

# runlevel
N 3

修改运行级别:
systemctl set-default multi-user.target
或者
ln -sf /lib/systemd/system/runlevel5.target /etc/systemd/system/default.target
```