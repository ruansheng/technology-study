### runlevel 运行级别

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