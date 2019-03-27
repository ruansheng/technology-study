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

### 客户端读写
```
Zookeeper 客户端会随机的连接到 zookeeper 集群中的一个节点:
读请求: 就直接从当前节点中读取数据；
写请求: 那么节点就会向 Leader 提交事务，Leader 接收到事务提交，会广播该事务，只要超过半数节点写入成功，该事务就会被提交
```

### ZAB协议的特性
```
1）Zab 协议需要确保那些已经在 Leader 服务器上提交（Commit）的事务最终被所有的服务器提交。
2）Zab 协议需要确保丢弃那些只在 Leader 上被提出而没有被提交的事务。
```

### ZAB协议原理
```
Zab协议要求每个 Leader 都要经历三个阶段：发现，同步，广播:
发现：要求zookeeper集群必须选举出一个 Leader 进程，同时 Leader 会维护一个 Follower 可用客户端列表。将来客户端可以和这些 Follower节点进行通信。
同步：Leader 要负责将本身的数据与 Follower 完成同步，做到多副本存储。这样也是提现了CAP中的高可用和分区容错。Follower将队列中未处理完的请求消费完成后，写入本地事务日志中。
广播：Leader 可以接受客户端新的事务Proposal请求，将新的Proposal请求广播给所有的 Follower。
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



