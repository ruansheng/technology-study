### su
```
su是最简单的身份切换命令

su [-lmp] [-c 命令] username
-  : 使用"su -"，表示使用login-shell的变量文件方式登录系统
-l : 和 "su -"类似，后面需要加上欲切换的用户账户，也是login-shell方式登录
-m : 表示使用目前的环境设置，而不读取新用户的配置文件
-p : 表示使用目前的环境设置，而不读取新用户的配置文件
-c : 仅执行一次命令

注意:
 单纯使用"su"切换，是已non-login shell方式登录root账户
 使用"su -username" 和 "su -l username" 效果是一样的

缺点:
 如果一个系统多个账号都切换root，那就需要多个账号都知道root密码
```

### sudo
```
sudo是使用root身份来执行命令

配置文件 /etc/sudoers，该文件只有root能修改，建议用visudo命令来修改，因为visudo在保存是会进行语法校验

sudo [-b] [-u 新用户账号]
-b : 将后续的命令让系统自动执行，不和目前的shell产生影响
-u : 接要切换的用户，如果没有此项则表示切换身份为root

#给账号添加sudo权限
root  ALL=(ALL)    ALL
说明:
root 表示可以使用sudo的账号名称
ALL  表示登录者来源的主机，ALL表示可以来自任何一台网络主机
ALL  表示root可以切换成什么身份来执行命令，默认root可以切换成任何人
ALL  表示可执行的命令，这个命令必须使用全路径编写

#给用户组添加sudo权限
%root  ALL=(ALL)    ALL
说明:
%root 表示可以使用sudo的用户组名称
ALL  表示登录者来源的主机，ALL表示可以来自任何一台网络主机
ALL  表示root可以切换成什么身份来执行命令，默认root可以切换成任何人
ALL  表示可执行的命令，这个命令必须使用全路径编写

#给sudo设置免密码
root  ALL=(ALL)    NOPASSWD: ALL
不再需要输入密码

#给sudo这种禁止命令
root  ALL=(ALL)    !/usr/bin/passwd !/usr/bin/passwd root

#别名设置sudo
别名可分为 账号别名User_Alias、命令别名Cmnd_Alias、主机别名Host_Alias等
User_Alias ADMPW = pro1,pro2,pro3
Cmnd_Alias ADMPWCOM = !/usr/bin/passwd, !/usr/bin/passwd root
ADMPW  ALL=(ALL) ADMPWCOM

#sudo的时间间隔
sudo的时间间隔为5分钟，也就是说5分钟内是不需要重复sudo输入密码

#sudo 搭配 su 使用
首先该用户有sudo权限，从该用户切换到root权限，如下命令:
sudo su -
```