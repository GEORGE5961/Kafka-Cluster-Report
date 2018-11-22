# **Kafka Stream Processing**
###### lijiahao - November 22, 2018
---
## **一、Setup method**
1. **first start a ZooKeeper server** 
   > bin/zookeeper-server-start.sh config/zookeeper.properties
2. **start the Kafka server in another terminal** 
   > bin/kafka-server-start.sh config/server.properties
3. **Next, we create the input topic named streams-plaintext-input and the output topic named streams-wordcount-output:**
    > bin/kafka-topics.sh --create \
    --zookeeper localhost:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic streams-plaintext-input
Created topic "streams-plaintext-input".
4. **we create the output topic with compaction enabled because the output stream is a changelog stream**
    > bin/kafka-topics.sh --create \
    --zookeeper localhost:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic streams-wordcount-output \
    --config cleanup.policy=compact
Created topic "streams-wordcount-output".
5. **The created topic can be described with the same kafka-topics tool:**
    > bin/kafka-topics.sh --zookeeper localhost:2181 --describe
6. **starts the WordCount(the name is the same with the packet name declare at the head of the .java file)**
    > bin/kafka-run-class.sh org.apache.kafka.streams.examples.wordcount.WordCountDemo
7. **start the console producer in a separate terminal**
    > bin/kafka-console-producer.sh --broker-list localhost:9092 --topic streams-plaintext-input
8. **inspect the output of the WordCount demo application by reading from its output topic with the console consumer in a separate terminal**
    > bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
    --topic streams-wordcount-output \
    --from-beginning \
    --formatter kafka.tools.DefaultMessageFormatter \
    --property print.key=true \
    --property print.value=true \
    --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer \
    --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
9. **input message in the termianl created in the step-7, then the result of the application will be shown in the termianl created in the step-8**
10. **You can exit the application by press ctrl+C**

## **二、Implementation**
1. Declare the packet name(org.apache.kafka.streams.examples.wordcount)
2. Add packet provided by the kafka
    >import org.apache.kafka.clients.consumer.ConsumerConfig;
    >import org.apache.kafka.common.serialization.Serdes;
    >import org.apache.kafka.streams.KafkaStreams;
    >import org.apache.kafka.streams.StreamsBuilder;
    >import org.apache.kafka.streams.StreamsConfig;
    >import org.apache.kafka.streams.kstream.KStream;
    >import org.apache.kafka.streams.kstream.KTable;
    >import org.apache.kafka.streams.kstream.Produced;
3. declare the Name/ID of the application
    >props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-wordcount");
4. declare the server config(the port of server)
    >props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
5. using Kstream to create data stream,your input will be inserted into the Kstream created by you(KStream负责抽象的，就是数据流。与Kafka自身topic中的数据一样，类似日志，每一次操作都是向其中插入（insert）新数据。)
    >final KStream<String, String> input_source = builder.stream("streams-plaintext-input");
6. create a KTable to calculate the word counts(KTable负责抽象的，就是表状数据。每一次操作，都是更新插入（upsert）。)
    >final KTable<String, Long> counter = input_source(input_source is the KStream created previously)
    1. split the input by detecting space
        >input_source.flatMapValues(value -> Arrays.asList(value.toLowerCase(Locale.getDefault()).split(" ")))
    2. keep the same word into one pair
        >input_source.groupBy((key, value) -> value)
    3. count the appearing time of the perticular word by counting the key-value pair
        >input_source.count()

