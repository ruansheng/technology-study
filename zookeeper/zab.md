### ZAB 协议
```
Zookeeper Atomic Broadcast（Zookeeper 原子广播协议）
ZAB 协议是为分布式协调服务 Zookeeper 专门设计的一种支持崩溃恢复和原子广播协议。
```

### 数据一致性原理
```
Zookeeper 如何处理集群中的数据。所有客户端写入数据都是写入到 主进程（称为 Leader）中，
然后，由 Leader 复制到备份进程（称为 Follower）中。从而保证数据一致性。从设计上看，和 Raft 类似。
```

### 消息广播和崩溃恢复
```
整个 Zookeeper 就是在这两个模式之间切换。 简而言之，当 Leader 服务可以正常使用，就进入消息广播模式，
当 Leader 不可用时，则进入崩溃恢复模式。
```

### 消息广播
```
ZAB 协议的消息广播过程使用的是一个原子广播协议，类似一个 二阶段提交过程。
对于客户端发送的写请求，全部由 Leader 接收，Leader 将请求封装成一个事务 Proposal，将其发送给所有 Follwer ，
然后，根据所有 Follwer 的反馈，如果超过半数成功响应，则执行 commit 操作（先提交自己，再发送 commit 给所有 Follwer）。
```

### ZXID
```
在 ZAB 协议的事务编号 ZXID 设计中，ZXID 是一个 64 位的数字，其中低 32 位可以看作是一个简单的递增的计数器，
针对客户端的每一个事务请求，Leader 都会产生一个新的事务 Proposal 并对该计数器进行 + 1 操作。

而高 32 位则代表了 Leader 服务器上取出本地日志中最大事务 Proposal 的 ZXID，并从该 ZXID 中解析出对应的 epoch 值，然后再对这个值加一。
```



