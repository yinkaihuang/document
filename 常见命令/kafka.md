# kafka常见命令

**基本命令**

```python
发送kafka消息：
./kafka-console-producer.sh --broker-list node1:9092 --topic my-kafka-topic

消费kafka消息命令：
./kafka-console-consumer.sh --bootstrap-server node1:9092 --topic my-kafka-topic

从头消费命令：
./kafka-console-consumer.sh --bootstrap-server node01:9092 --from-beginning --topic my-kafka-topic
```

**topic相关命令**

```python
创建topic命令：
./kafka-topics.sh --create --zookeeper 192.168.119.131:2181 --replication-factor 1 --partitions 4 --topic test_demo

修改topic命令：
 ./kafka-topics.sh --alter --zookeeper 192.168.119.131:2181 --topic test_demo --partitions 12

列出所有的topic：
./kafka-topics.sh --list --zookeeper localhost:2181

查看下topic的详细信息
/kafka-topics.sh --describe --zookeeper 192.168.119.131:2181 --topic test_demo 

```


 **消费组相关**

```python

查看所有的消费组们：
./kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list

查看某个消费组的详细信息：
./kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group test_demo

```

 

**调优命令**

```python

生产端压测命令：
kafka-producer-perf-test.bat --topic test_demo --num-records 100 --record-size 1 --throughput 100  --producer-props bootstrap.servers=localhost:9092

参数讲解：
--topic topic名称
--num-records 总共需要发送的消息数量
--record-size 每个记录的字节数
--throughput  每秒发送的记录数
--producer-props bootstrap.servers=localhost:9092 发送端配置


消费端压测命令：
kafka-consumer-perf-test.bat --broker-list localhost:9092 --topic test_demo --fetch-size 1048576 --messages 10000000 --threads 1

参数讲解：
--broker-list kafka链接信息
--topic  指定topic的名称
--fetch-size 指定每次fetch的数据大小（单位字节）
--messages 总共要消费的消息个数

```


**常见问题**

```python

查看kafka文件句柄使用情况是否合理:
 ps aux|grep kafka //获取kafka的进程id
 ls /proc/pid/fd |wc -l //获取kafka中句柄使用情况
 ulimit -Sn //查看软限制
 ulimit -Hn //查看硬限制
 如果kafka使用文件句柄过多可以使用 ulimit -n 数量  （但是要小于硬限制）

```