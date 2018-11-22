## Set up Kafka

### Step 1: Download Java, Zookeeper, and Kafka

#### Download them on the official websites

* Java: http://www.oracle.com/technetwork/java/javase/downloads/index.html
* Zookeeper: http://zookeeper.apache.org/releases.html
* Kafka: https://www.apache.org/dyn/closer.cgi?path=/kafka/0.9.0.0/kafka_2.11-2.0.0.tgz

### Step 2: Start the server

#### Start Zookeeper server
1. Enter the Zookeeper directory you download before (not Kafka directory).
2. Enter the command:
	
	```
	> ./bin/zkServer.sh start
	```
3. You should get the following response if that's OK:
	
	```
	> JMX enabled by default
	> Using config: /Users/../zookeeper-3.4.6/bin/../conf/zoo.cfg
	> Starting zookeeper ... STARTED
	```
	
#### Start Kafka server
1. Enter the kafka directory you download before (not Zookeeper directory).
2. Enter the command:
	
	```
	> ./bin/kafka-server-start.sh config/server.properties
	```
3. You should get the following response if that's OK:
	
	```
	> ./bin/kafka-server-start.sh config/server.properties
	
	……………………………………………
	……………………………………………
	[2016-01-02 15:37:30,410] INFO KafkaConfig values:
	request.timeout.ms = 30000
	log.roll.hours = 168
	inter.broker.protocol.version = 0.9.0.X
	log.preallocate = false
	security.inter.broker.protocol = PLAINTEXT
	……………………………………………
	……………………………………………
	```
	
### Step 3: Create a topic
1. Create a topic, say, test
	
	```
	> ./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
	```
	
2. List topics
	
	```
	> ./bin/kafka-topics.sh --list --zookeeper localhost:2181
	test	
	```
	
### Step 4: Test with a producer and a consumer

1. A producer sends a message.

	```
	> ./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
	Message from WZY.
	```
2. A consumer consumes a message.

	```
	> ./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
	Message from WZY.
	```
	
### Step 5: Set up a multi-broker cluster

Until now, we have set up a basic Kafka environment. But we haven't felt the power of Kafka, right? Let's build a Kafka cluster.

1. Make a config file for each of the brokers

	```
	> cp config/server.properties config/server-1.properties
	> cp config/server.properties config/server-2.properties
	```
	
2. Edit these new files and set the following properties:

	* config/server-1.properties:
	
	```
	broker.id=1
	listeners=PLAINTEXT://:9093
	log.dirs=/tmp/kafka-logs-1
 	```
 	
	* config/server-2.properties:
    
    ```
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dirs=/tmp/kafka-logs-2
    ```
 
3. Start both of them

 	```
	> ./bin/kafka-server-start.sh config/server-1.properties &
	...
	> ./bin/kafka-server-start.sh config/server-2.properties &
	...
	```
	
	
### Reference:

1. [https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart)
2. [https://www.w3cschool.cn/apache_kafka/apache_kafka_basic_operations.html](https://www.w3cschool.cn/apache_kafka/apache_kafka_basic_operations.html)




