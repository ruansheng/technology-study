### OOM
```
OOM关键文件oom_kill.c，里面介绍了当内存不够时，系统如何选择最应该被杀死的进程；
选择因素有挺多的，除了进程占用的内存外，还有进程运行的时间，进程的优先级，
是否为root用户进程，子进程个数和占用内存以及用户控制参数oom_adj都相关；

当产生oom之后，函数select_bad_process会遍历所有进程，通过之前提到的那些因素，
每个进程都会得到一个oom_score分数，分数最高，则被选为杀死的进程；

我们可以通过设置/proc/<pid>/oom_adj分数来干预系统选择杀死的进程；
/*
 * /proc/<pid>/oom_adj set to -17 protects from the oom killer for legacy
 * purposes.
 */
#define OOM_DISABLE (-17)
/* inclusive */
#define OOM_ADJUST_MIN (-16)
#define OOM_ADJUST_MAX 15


这是内核关于这个oom_adj调整值的定义；最大可以调整为15,最小为-16，如果为-17，
则该进程就像买了vip会员一样，不会被系统驱逐杀死了；
因此，如果在一台机器上有跑很多服务器，且你不希望自己的服务被杀死的话，就可以设置自己服务的oom_adj为-17；
```
