### accept_mutex
```
events {
    accept_mutex off;
}
```

### 作用
```
当一个新连接到达时，如果激活了accept_mutex，那么多个Worker将以串行方式来处理，其中有一个Worker会被唤醒，
其他的Worker继续保持休眠状态；如果没有激活accept_mutex，那么所有的Worker都会被唤醒，不过只有一个Worker能获取新连接，
其它的Worker会重新进入休眠状态，这就是「惊群问题」

Apache动辄就会启动成百上千的进程，如果发生惊群问题的话，影响相对较大；
但是对Nginx而言，一般来说，worker_processes会设置成CPU个数，所以最多也就几十个，即便发生惊群问题的话，影响相对也较小。
另：高版本的Linux中，accept不存在惊群问题，不过epoll_wait等操作还有

Nginx缺省激活了accept_mutex，是一种保守的选择。如果关闭了它，可能会引起一定程度的惊群问题，
表现为上下文切换增多（sar -w）或者负载上升，但是如果你的网站访问量比较大，为了系统的吞吐量，我还是建议大家关闭它。
```