## kafka basic concept
https://blog.csdn.net/shangmingtao/article/details/79567921  
- Broker : Kafka集群包含一个或多个服务器，这种服务器被称为broker
- Topic : 每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）
- Partition : Partition是物理上的概念，每个Topic包含一个或多个Partition.
- Producer : 负责发布消息到Kafka broker
- Consumer : 消息消费者，向Kafka broker读取消息的客户端。
- Consumer Group : 每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。

## consumer and partitions
一个Consumer(消费者)的一个线程在某个时刻只能接收一个partition(分区)的数据,一个partition(分区)某个时刻也只会把消息发给一个Consumer(消费者).我们设计出来几种场景:

场景一: topic-1 下有partition-1和partition-2 
group-1 下有consumer-1和consumer-2和consumer-3 
所有consumer只有一个线程,且都消费topic-1的消息. 
消费情况 : consumer-1只消费partition-1的数据 
consumer-2只消费partition-2的数据 
consumer-3不会消费到任何数据 
原因 : 只能接受一个partition(分区)的数据

场景二: topic-1 下有partition-1和partition-2 
group-1 下有consumer-1 
consumer只有一个线程,且消费topic-1的消息. 
消费情况 : consumer-1先消费partition-1的数据 
consumer-1消费完partition-1数据后开始消费partition-2的数据 
原因 : 这里是kafka检测到当前consumer-1消费完partition-1处于空闲状态,自动帮我做了负载.所以大家看到这里在看一下上边那句话的”某个时刻” 
特例: consumer在消费消息时必须指定topic,可以不指定partition,场景二的情况就是发生在不指定partition的情况下,如果consumer-1指定了partition-1,那么consumer-1消费完partition-1后哪怕处于空闲状态了也是不会消费partition-2的消息的.  

进而我们总结出了一条经验,同组内的消费者(单线程消费)数量不应多于topic下的partition(分区)数量,不然就会出有消费者空闲的状态,此时并发线程数=partition(分区)数量.反之消费者数量少于topic下的partition(分区)数量也是不理想的,原因是此时并发线程数=消费者数量,并不能完全发挥kafka并发效率.

## specify partition in ProducerRecord
https://kafka.apache.org/10/javadoc/org/apache/kafka/clients/producer/ProducerRecord.html
```
ProducerRecord(java.lang.String topic, java.lang.Integer partition, K key, V value)  
Creates a record to be sent to a specified topic and partition
```
For the consumer, to specify partitions
https://kafka.apache.org/10/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html
```
consumer.assign(Arrays.asList(partition0, partition1));
```

## consumer read record from specific offset


## kafka consumer is offline for a while, when it comes back, does it continue from the previous offset? Or it will miss thhe messages when it is offline? 
TODO  
https://stackoverflow.com/questions/42572795/kafka-does-not-retrieve-messages-which-are-sent-when-it-is-offline  

## confluent jdbc source configuratin options
https://docs.confluent.io/current/connect/connect-jdbc/docs/source-connector/source_config_options.html#jdbc-source-configs  

## confluent jdbc sink configuration options
https://docs.confluent.io/current/connect/connect-jdbc/docs/sink-connector/sink_config_options.html  

## confluent connect
once the connector is started, the default port is 8083. The Url below can list all connectors loaded.  
http://localhost:8083/connectors  
Let's sya there is one connector which name is "my-file-sink". The Url below can view the detail of the connector.  
http://localhost:8083/connectors/my-file-sink
this url can view the status
http://localhost:28083/connectors/my-jdbc-source/status

To load one connect, 
```
curl -X POST   -H "Content-Type: application/json"   --data '{ "name": "my-jdbc-source", "config": { "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector", "tasks.max": 1, "connection.url": "jdbc:mysql://127.0.0.1:3306/connect_test?user=root&password=password", "mode": "incrementing", "incrementing.column.name": "id", "timestamp.column.name": "modified", "topic.prefix": "my-jdbc-", "poll.interval.ms": 1000, "table.whitelist": "test" } }'   http://localhost:8083/connectors
```
or 
```
curl -X POST   -H "Content-Type: application/json"   --data '{ "name": "my-file-sink", "config": { "connector.class": "FileStreamSink", "tasks.max": 1, "file": "/tmp/test.sink.txt", "topics": "my-jdbc-test" } }'   http://localhost:8083/connectors
```
To delete one connect, 
```
curl -X DELETE http://localhost:28083/connectors/my-jdbc-source
```

## kafka commands
### list groups/consumers 
kafka-consumer-groups --bootstrap-server localhost:19092,localhost:29092,localhost:39092 --list
### view groups/consumers offset
kafka-consumer-groups --bootstrap-server localhost:19092,localhost:29092,localhost:39092 --describe --group console-consumer-34641

## kafka connector sink tutorial
I followed this tutorial online and found there were some errors. 
https://docs.confluent.io/3.1.2/cp-docker-images/docs/tutorials/connect-avro-jdbc.html

Here is the command to create the connector
```
docker run -d \
  --name=kafka-connect-avro-cluster \
  --net=host \
  -e CONNECT_BOOTSTRAP_SERVERS=localhost:19092,localhost:29092,localhost:39092 \
  -e CONNECT_REST_PORT=28083 \
  -e CONNECT_GROUP_ID="quickstart-avro" \
  -e CONNECT_CONFIG_STORAGE_TOPIC="quickstart-avro-config" \
  -e CONNECT_OFFSET_STORAGE_TOPIC="quickstart-avro-offsets" \
  -e CONNECT_STATUS_STORAGE_TOPIC="quickstart-avro-status" \
  -e CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_OFFSET_FLUSH_INTERVAL_MS=10000 \
  -e CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_KEY_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_VALUE_CONVERTER="io.confluent.connect.avro.AvroConverter" \
  -e CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL="http://localhost:8081" \
  -e CONNECT_INTERNAL_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_INTERNAL_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" \
  -e CONNECT_REST_ADVERTISED_HOST_NAME="localhost" \
  -e CONNECT_LOG4J_ROOT_LOGLEVEL=DEBUG \
  -e CONNECT_PLUGIN_PATH="/usr/share/java" \
  -v /tmp/quickstart/file:/tmp/quickstart \
  -v /tmp/quickstart/jars:/etc/kafka-connect/jars \
  confluentinc/cp-kafka-connect:latest
```
Three environment arguments are mandatory. 
```
  -e CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1 \
  -e CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1 \
```
here is the command to create the sink in cluster env. 
```
curl -X POST -H "Content-Type: application/json" \
  --data '{"name": "quickstart-avro-file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"3", "topics":"quickstart-jdbc-test", "file": "/tmp/quickstart/jdbc-output.txt"}}' \
  http://localhost:28083/connectors
```

## confluent Docker configuration parameters
https://docs.confluent.io/current/installation/docker/docs/config-reference.html#c3-configuration  

## run confluent on  (windows subsystem linux)
download the latest version and extract it  
https://nielsberglund.com/2018/07/10/install-confluent-platform-kafka-on-windows/  

Now the confluent CLI need be installed separately  
https://docs.confluent.io/current/cli/installing.html  
```
curl -L https://cnfl.io/cli | sh -s -- -b /<path-to-directory>/bin
```
Then you can run 
```
bin/confluent local start
```
gerenate data example, https://www.confluent.io/blog/easy-ways-generate-test-data-kafka

## running confluent 
### port listening
zookeeper: 2181
kafka: 9092
schema-registry: 8081
connect: 9021

### start
cd confluent-5.3.0
rm -f zookeeper.out && nohup sudo bin/zookeeper-server-start etc/kafka/zookeeper.properties > zookeeper.out &
rm -f kafka.out && nohup sudo bin/kafka-server-start etc/kafka/server.properties > kafka.out &
rm -f schema.out && nohup sudo bin/schema-registry-start etc/schema-registry/schema-registry.properties > schema.out &
rm -f connect.out && nohup sudo bin/control-center-start etc/confluent-control-center/control-center-dev.properties > connect.out &
rm -f avro.out && nohup sudo bin/connect-distributed etc/schema-registry/connect-avro-distributed.properties > avro.out &
rm -f kafkaRest.out && nohup sudo bin/kafka-rest-start etc/kafka-rest/kafka-rest.properties > kafkaRest.out &
rm -f ksql.out && nohup sudo bin/ksql-server-start etc/ksql/ksql-server.properties > ksql.out &

### stop 
sudo bin/zookeeper-server-stop
sudo bin/kafka-server-stop
sudo bin/schema-registry-stop
sudo bin/control-center-stop

### feed data
nohup bin/ksql-datagen quickstart=users format=avro topic=users maxInterval=100 &


## ksql
prepare the environment, extract confluent platform, install confluent CLI
```
confluent local start
```
For small testing data, you can feed in through input
```
confluent local produce pageview
> input your data here...
```

create datagen-pageviews.json file, this is the config file to load data 
```
{
  "name": "datagen-pageviews",
  "config": {
    "connector.class": "io.confluent.kafka.connect.datagen.DatagenConnector",
    "kafka.topic": "pageviews",
    "quickstart": "pageviews",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "max.interval": 1000,
    "iterations": 1000,
    "tasks.max": "1"
  }
}
```
The command below starts pushing data to the topic
```
confluent local config datagen-pageviews -- -d datagen-pageviews.json
```
the data is loading now, to have a look, add a console consumer
```
confluent local consume pageviews
OR
confluent local consume pageviews -- --from-beginning
```

