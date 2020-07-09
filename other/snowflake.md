## SnowFlake算法
![TCP Option](https://github.com/ruansheng/technology-study/blob/master/images/snowflake.png)
```
Snowflake 算法是Twitter的分布式ID自增算法,用于生成可以跨数据中心的全局唯一ID(不连续)。
SnowFlake算法生成id的结果是一个64bit大小的整数。
```

### bit说明
```
第一个bit位是标识部分，在Java中由于long的最高位是符号位，正数是0，负数是1，一般生成的ID为正数，所以固定为0。
时间戳部分占41bit，这个是毫秒级的时间，一般实现上不会存储当前的时间戳，而是时间戳的差值（当前时间-固定的开始时间），这样可以使产生的ID从更小值开始；41位的时间戳可以使用69年；
工作机器id占10bit，这里比较灵活，比如，可以使用前5位作为数据中心机房标识，后5位作为单机房机器标识，可以部署1024个节点。
序列号部分占12bit，支持同一毫秒内同一个节点可以生成4096个ID。
```

### 优点
```
毫秒数在高位，自增序列在低位，ID趋势递增。
以服务方式部署，可以做高可用。
根据业务分配bit位，灵活。
```

### 缺点
```
每台机器的时钟不同，当时钟回拨可能会发生重复ID。
当数据量大时，需要对ID取模分库分表，在跨毫秒时，序列号总是归0，会发生取模后分布不均衡。
```