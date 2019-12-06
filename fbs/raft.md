## raft协议
raft是一个共识算法（consensus algorithm），所谓共识，就是多个节点对某个事情达成一致的看法。

所有节点启动时都是follower状态；在一段时间内如果没有收到来自leader的心跳，从follower切换到candidate，发起选举；
如果收到的造成票（含自己的一票）则切换到leader状态；如果发现其他节点比自己更新，则主动切换到follower。

### 节点角色
```
Leader：主节点，负责接收客户端的请求，将日志复制到其他节点并告知其他节点何时应用这些日志是安全的
Candidate：候选者，用于选举Leader的一种角色
Follower：从节点，负责响应来自Leader或者Candidate的请求
```

### 角色流转
```
1.所有节点初始状态都是Follower角色
2.超时时间内没有收到Leader的请求则转换为Candidate进行选举
3.Candidate收到大多数节点的选票则转换为Leader；发现Leader或者收到更高任期的请求则转换为Follower
4.Leader在收到更高任期的请求后转换为Follower
```

### 任期
```
Raft把时间切割为任意长度的任期，每个任期都有一个任期号，采用连续的整数。
```