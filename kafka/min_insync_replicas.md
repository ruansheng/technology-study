## min.insync.replicas
用于指定消息最少同步的副本数量
假如min.insync.replicas=2，那么至少要存在2个同步副本才能向分区写入数据
当只剩下1个同步副本时，分区就变为只读