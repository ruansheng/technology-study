## redis hash
redis hash结构的内存存储模型
对于 Redis的常用 5 种数据类型（String、Hash、List、Set、sorted set），每种数据类型都提供了 最少两种 内部的编码格式，而且每个数据类型内部编码方式的选择 对用户是完全透明的，Redis会根据数据量自适应地选择较优化的内部编码格式。

### 查看key使用的编码方式
```
可以用 OBJECT ENCODING keyname 来查看某个key的内部编码类型
127.0.0.1:6379> set name rs
OK
127.0.0.1:6379> object encoding name
"embstr"

看一下测试例子:
127.0.0.1:6379> hset htest name rs
(integer) 1
```

### hash结构的内部编码方法
```
对于Hash类型，其内部编码方式可能有两种：
OBJ_ENCODING_ZIPLIST（压缩列表）
OBJ_ENCODING_HT（哈希表）

Redis 会根据数据量的情况来自适应地选择这两种编码方式中 较优 的一种，而这一切对用户完全透明。
在 数据条目较少，数据值较小 的时候 Redis会采用 压缩列表（OBJ_ENCODING_ZIPLIST）编码方式进行存储。这里成员"较少"，
成员值"较小"的标准可以通过如下配置项进行配置：
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
Redis 默认给出了默认值，也可根据实际情况自行修改配置
以上配置的意思是:
当 Hash类型键的字段个数 < hash-max-ziplist-entries 并且 每个字段名和字段值的长度 < hash-max-ziplist-value 时，
Redis 会使用 OBJ_ENCODING_ZIPLIST来存储该键，反之则会转换为 OBJ_ENCODING_HT的编码方式。

127.0.0.1:6379> object encoding htest
"ziplist"
127.0.0.1:6379> hset htest name 1234567890123456789012345678901234567890123456789012345678901234   // 设置64个字符
(integer) 0
127.0.0.1:6379> object encoding htest
"ziplist"
127.0.0.1:6379> hset htest name 12345678901234567890123456789012345678901234567890123456789012345  // 设置65个字符
(integer) 0
127.0.0.1:6379> object encoding htest
"hashtable"
由上可见，超过64个字符之后编码格式会由 ZIPLIST方式切换为 Hashtable方式

```

### OBJ_ENCODING_ZIPLIST 编码格式的内部存储模型
```
Ziplist 压缩列表是一种紧凑编码格式，总体思想是时间换空间，即以部分读写性能为代价，来换取极高的内存空间利用率，因此只会用于 字段个数少，且字段值也较小的场景，压缩列表内存利用率极高的原因与其连续内存的特性是分不开的，ziplist并没有定义明确的结构体, 根据存储结构我们可以定义ziplist如下, 只是进行演示用.其中content字段存储实际的实体内容, 实体
typedef struct ziplist{
     /*ziplist分配的内存大小*/
     uint32_t bytes;
     /*达到尾部的偏移量*/
     uint32_t tail_offset;
     /*存储元素实体个数*/
     uint16_t length;
     /*存储内容实体元素*/
     unsigned char* content[];
     /*尾部标识*/
     unsigned char end;
}ziplist;

/*元素实体所有信息, 仅仅是描述使用, 内存中并非如此存储*/
typedef struct zlentry {
     /*前一个元素长度需要空间和前一个元素长度*/
    unsigned int prevrawlensize, prevrawlen;
     /*元素长度需要空间和元素长度*/
    unsigned int lensize, len;
     /*头部长度即prevrawlensize + lensize*/
    unsigned int headersize;
     /*元素内容编码*/
    unsigned char encoding;
     /*元素实际内容*/
    unsigned char *p;
}zlentry;

                                  ziplist内存布局
|-----------|-----------|----------|---------------------------------------------------|---|
    bytes      offset      length  content         {zlentry, zlentry ... ...}           end
    
```

### OBJ_ENCODING_HT 编码格式的内部存储模型
```
OBJ_ENCODING_HT 这种编码方式内部才是真正的哈希表结构，或称为字典结构，其可以实现O(1)复杂度的读写操作，因此效率很高。
```
