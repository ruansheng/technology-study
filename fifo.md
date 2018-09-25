### FIFO 命名管道
```
FIFO可以使没有亲缘关系的进程通信，和无名管道(PIPE)相比，FIFO仅仅是披了一件马甲，其核心与PIPE是一模一样的
内核fs/fifo.c文件很简短，只说明了两件事: 
1. 从外表看，我是一个FIFO文件，任何进程通过文件名都可以打开我
2. 我的内心与PIPE是一样的，支持的文件操作与PIPE也是一样的
```

### linux创建FIFO
```
mkfifo [-m mode] pathname
或者
mknod [-m mode] pathname p

通过ls -l查看文件，第一个字符是p，表示是命名管道文件
```
