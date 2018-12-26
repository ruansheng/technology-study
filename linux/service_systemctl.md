### service 和 systemctl 命令
```
在RHEL 6 使用service、chkconfig命令来管理系统服务
在RHEL 7 使用systemctl命令来管理系统服务
```
|System V init 命令           | systemctl 命令    | 作用    |
| :------| :------: |:------: |
| service foo start | systemctl start foo.service | 启动服务 |
| service foo restart | systemctl restart foo.service | 重启服务 |
| service foo stop | systemctl stop foo.service | 停止服务 |
| service foo reload | systemctl reload foo.service | 重新加载配置文件(不终止服务) |
| service foo status | systemctl status foo.service | 查看服务状态 |

### 开机启动设置
|System V init 命令           | systemctl 命令    | 作用    |
| :------| :------: |:------: |
| chkconfig foo on | systemctl enable foo.service | 开机自动启动 |
| chkconfig foo off | systemctl disable foo.service | 开机不自动启动 |
| chkconfig foo | systemctl is-enable foo.service | 查看特定服务是否为开机自动启动 |
| chkconfig --list | systemctl list-unit-files --type=service | 查看各个级别下服务的启动与禁用情况 |
