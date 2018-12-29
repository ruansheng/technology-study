### job control
```
job control 是用在bash环境下的:登录系统取得bash shell之后，在单一终端下进行多个工作任务的行为管理
```

### jobs
```
列出当前终端下的后台的工作任务，在后台的工作任务状态有"暂停(stop)"与"运行中(running)"
#jobs
[1]+  已停止               vi .bashrc

#jobs -l
[1]+  已停止               vi .bashrc
[2]-  已停止               vi .bashrc

这里的+、- 号有特殊含义
+ 表示最后一个加入的任务
- 表示倒数第二个加入的任务，倒数第三个就没有这种表示了
```

### 任务加入jobs
```
1. control + Z
2. 在命令后面加入 &，例如: ./run.sh &
```

### fg 后台的任务提到前台
```
# fg      把最后一个加入的任务提到前台
# fg+      把最后一个加入的任务提到前台
# fg-      把倒数第二个加入的任务提到前台
# fg %jobnumber    把jobnumber这个号码的任务提到前台 例如: fg %1
```

### bg 后台的任务状态变为运行
```
# bg %jobnumber    把jobnumber这个号码的任务状态变为运行 例如: bg %1
```

### kill 管理后台中任务
```
kill -signal %jobnumber
```