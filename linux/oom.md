### OOM
```
OOM就是out of memory的缩写

Linux内核有个机制叫OOM killer（Out-Of-Memory killer），该机制会监控那些占用内存过大，
尤其是瞬间很快消耗大量内存的进程，为了防止内存耗尽内核会把该进程杀掉。
```

```
当内存严重不足时，内核有两种选择：
1.直接panic
2.杀掉部分进程，释放一些内存；

通过/proc/sys/vm/panic_on_oom可以控制，当panic_on_oom为1时，直接panic，
当panic_on_oom为0时内核将通过oom killer杀掉部分进程。（默认是为0的）

# sysctl -w vm.panic_on_oom=1
vm.panic_on_oom = 1 //1表示关闭，默认为0表示开启OOM killer
# sysctl –p

我们可以通过一些内核参数来调整 OOM killer 的行为，避免系统在那里不停的杀进程。比如我们可以在触发 OOM 后立刻触发 kernel panic，kernel panic 10秒后自动重启系统：
# sysctl -w vm.panic_on_oom=1
vm.panic_on_oom = 1
# sysctl -w kernel.panic=10
kernel.panic = 10
或者：
# echo "vm.panic_on_oom=1" >> /etc/sysctl.conf
# echo "kernel.panic=10" >> /etc/sysctl.conf
```

```
内核检测到系统内存不足、挑选并杀掉某个进程的过程可以参考内核源代码 linux/mm/oom_kill.c，
当系统内存不足的时候，out_of_memory()被触发，然后调用 select_bad_process() 选择一个“bad”进程杀掉，
判断和选择一个“bad”进程的过程由 oom_badness()决定，最 bad 的那个进程就是那个最占用内存的进程。
```

```
每个进程的分值有2部分组成，系统打分和用户打分
/proc/PID/oom_score     查看每个进程的oom分数
/proc/PID/oom_score_adj 进程的用户打分，新接口
/proc/PID/oom_adj       老接口，兼容oom_score_adj
```

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
