## history
```
记录曾经执行过的命令
记录在 ~/.bash_history 中

# history 10    列出最近10条命令
# history -c    清除目前shell中的所有history内容，注意:并不会影响~/.bash_history中的内容
# history -a histfiles   将目前新增的命令写入到histfiles中，如果没有写histfiles，则默认写入到~/.bash_history
# history -r    将histfiles的内容读到目前这个shell的hisotry记忆中
# history -w    将目前的hisotry记忆内容写入到histfiles中

history记录的条数由环境变量HISTSIZE决定

!number   执行history中的第number条命令
!command  由最近的命令向前搜索命令串以command开头的命令，并执行
!!        执行上一条命令
```