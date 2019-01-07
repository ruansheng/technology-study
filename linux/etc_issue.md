### /etc/issue
```
/etc/issue 是显示在TTY控制台登录前（非图形界面）

\r 显示KERNEL内核版本号；
\l 显示虚拟控制台号；
\d 显示当前日期；
\n 显示主机名；
\m 显示机器类型，即CPU架构，如i386等；
```

### /etc/issue.net
```
/etc/issue.net 是显示在 Telnet (SSH默认不开启)远程登录前

在/etc/ssh/sshd_config添加“Banner /etc/issue.net”
重启sshd服务，再次登录就能生效
```