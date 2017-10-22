# kafka相关命令

1) 启动kafka

```
bin/kafka-server-start.sh -daemon config/server.properties
```
2) 查询kafka分片

```
bin/kafka-topics.sh --describe --zookeeper 10.211.55.15:2182,10.211.55.16:2182,10.211.55.17:2182 --topic test
```

3) 创建分区

```
bin/kafka-topics.sh --create --zookeeper 10.211.55.15:2182,10.211.55.16:2182,10.211.55.17:2182 --replication-factor 2 --partitions 2 --topic test2
```

4) 手动触发选举

```
bin/kafka-preferred-replica-election.sh --zookeeper 10.211.55.15:2182,10.211.55.16:2182,10.211.55.17:2182
 ```

5) 监控启动命令

```
java -cp KafkaOffsetMonitor-assembly-0.2.0.jar com.quantifind.kafka.offsetapp.OffsetGetterWeb --zk 10.4.46.215:2181,10.4.46.216:2181,10.4.46.217:2181 --port 9090 --refresh 10.seconds --retain 1.days &
 ```
