### env
Linux系统中的环境变量是用来定义系统运行环境的一些参数的，比如每个用户不同的家目录
```
# env
XDG_SESSION_ID=2102
HOSTNAME=localhost.localdomain
SELINUX_ROLE_REQUESTED=
TERM=xterm-256color
SHELL=/bin/bash
HISTSIZE=1000
SSH_CLIENT=172.16.34.161 61700 22
SELINUX_USE_CURRENT_RANGE=
...
```

### 输入一个命令的执行过程
```
1. 判断用户是否以绝对路径或相对路径的方式输入命令(比如/bin/ls)，如果是的话就直接执行
2. 检查命令是否是"别名命令"，通过alias定义的命令名称
3. 解释权判断用户输入的是内部命令还是外部命令，内部命令是解释器内部的命令，会被直接指向；
   而用户绝大部分时间输入的是外部命令，可以用"type 命令"来判断是内部命令还是外部命令
4. 系统在对个路径中查找用户输入的命令文件，定义这些路径的变量名叫PATH，PATH可以定义多个路径在其中，
   各路径直接用":"分隔，解释器将逐个查找
```

### 常见的env
|变量名称            | 作用    |
| :------| :------: |
| HOME | 用户的住目录(家目录)  |
| SHELL | 用户在使用的Shell解释器名称  |
| HISTSIZE | 输出的历史命令记录条数 |
| HISTFILESIZE | 保存的历史命令记录条数 |
| MAIL | 邮件保存路径 |
| LANG | 系统语言、语言名称 |
| RANDOM | 生成一个随机数字 |
| PS1 | Bash解释器的提示符 |
| PATH | 搜索用户执行命令的路径 |

### export
```
export作用在提升变量为全局变量
```

### source
```
使设置的变量生效
例如:
    1. 在当前session中添加一个变量a=test在.bashrc文件中
    2. echo $a  输出空字符串，说明未生效
    3. source .bashrc
    4. echo $a  输出test，说明生效了
```
