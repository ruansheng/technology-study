## access log rsyslog
rsyslog 是一个快速处理收集系统日志的程序，提供了高性能、安全功能和模块化设计。rsyslog 是syslog 的升级版，
它将多种来源输入输出转换结果到目的地，据官网介绍，现在可以处理100万条信息

### 安装 rsyslog
yum install rsyslog -y

### 查看rsyslogd进程
```
#ps -ef | grep rsyslogd | grep -v grep
root      5337     1  0 11:08 ?        00:00:00 /usr/sbin/rsyslogd -n
```

### 配置文件
/etc/rsyslog.conf
```
配置文件/etc/rsyslog.conf大概分为三个部分
#MODULES
这个部分是针对接收配置的，主要是指定接收日志的协议和端口。若要配置日志服务器，则需要将相应的配置项去掉注释。
#GLOBAL DIRECTIVES
这个部分主要用来配置模板，模板的作用是指定你希望在日志文件中保存的日志格式。
#begin forwarding rule
这个模块主要讲一下转发
根据这个实例可以看出，分为4个部分[模块.等级] [转发协议][日志服务器地址]:[日志服务器端口]，
其中转发协议的参数@@为TCP协议，对应的接收端也需要配置接受TCP协议。@为UDP协议。
注：使用TCP协议，若地址错误或不能连同的情况，转发的协议会写入缓存，但是不用担心会卡死服务器，
当到达一定限度后会自动转存到硬盘，这个不是我们应该关心的部分，使用就好了。
```

### local
local 1~7   –自定义的日志设备

### 日志级别
```
日志级别:
———————————————————————-
debug   0       –有调式信息的，日志信息最多
info    1       –一般信息的日志，最常用
notice  2      –最具有重要性的普通条件的信息
warning 3     –警告级别
err     4       –错误级别，阻止某个功能或者模块不能正常工作的信息
crit    5      –严重级别，阻止整个系统或者整个软件不能正常工作的信息
alert   6     –需要立刻修改的信息
emerg   7      –内核崩溃等严重信息
none    8      –什么都不记录
从上到下，级别从低到高，记录的信息越来越少
```

### 转发到远程
```
*.* @192.168.0.1          # 使用UDP协议转发到192.168.0.1的514(默认)端口
*.* @@192.168.0.1:514     # 使用TCP协议转发到192.168.0.1的514端口
```

### 示例
```
在/etc/rsyslog.conf配置文件中加入下面配置:
local3.*                                                /var/log/local3.log

重启syslog服务
#/etc/init.d/rsyslog restart   #centos 6
#systemctl restart rsyslog     #centos 7

写入日志
#logger 'test' -p local3.info 'KadeFor is testing the rsyslog and logger'

查看日志是否收集到
#cat /var/log/local3.log
Sep 12 14:06:07 pt-test01 root: test KadeFor is testing the rsyslog and logger
```
