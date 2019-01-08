### login shell、non-login shell
```
login shell : 取得bash时需要完整的登录流程
non-login shell : 取得bash接口不需要登录的举动，比如在原本bash下再次执行bash这个命令，那么第二个bash子进程也就是non-login shell
```

### login shell读取的配置文件
```
/etc/profile   // 全局login shell配置文件
    /etc/inputrc
    /etc/profile.d/*.sh
        /etc/sysconfig/i18n
~/.bash_profile  // 个人login shell配置文件
    ~/.bashrc
         /etc/bashrc
             /etc/profile.d/*.sh
                 /etc/sysconfig/i18n

个人偏好会顺序都去3个配置文件:
~/.bash_profile
~/.bash_login
~/.profile
bash的login shell只会读取三个中的一个，而读取的顺序是依照上面的顺序，也就是 ~/.bash_profile存在则不会读后面2个，依次类推
```

### non-login shell读取的配置文件
```
non-login shell 只会读取 ~/.bashrc
```
### ~/.bashrc 模板
```
如果没有 ~/.bashrc 文件，可以copy /etc/skel/.bashrc 文件内容来创建 ~/.bashrc
```

### source 和 .
```
当修改了配置文件，不会直接生效，需要注销再登录后才会生效
# source ~/.bashrc
# . ~/.bashrc
上面2中方式是等效的
```

### ~/.bash_history
```
bash的历史命令记录在这里，这个文件记录的命令条数与HISTSIZE有关
每次登录都会先读取这个文件，将所有历史命令读入内存
```

### ~/.bash_logout
```
这个文件记录了当注销bash后系统再帮我完成上面操作后才离开
```