## 安装 Kafka Broker
下载kafka_2.12-1.1.0.tar.gz
解压

## 配置
```
修改配置文件 /usr/local/kafka_2.12-1.1.0/config/server.properties
修改zookeeper地址
zookeeper.connect=localhost:2181
```

## 启动
```
#sh kafka-server-start.sh -daemon /usr/local/kafka_2.12-1.1.0/config/server.properties
```