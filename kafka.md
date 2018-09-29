
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
