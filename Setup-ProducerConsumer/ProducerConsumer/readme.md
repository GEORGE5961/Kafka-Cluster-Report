## Readme


### 1. Introduction

My code is based on KafkaProducer API and ConsumerRecords API.

#### KafkaProducer API

The point of KafkaProducer API is Class KafkaProducer. 

* send()
	
	```
	producer.send(new ProducerRecord<byte[],byte[]>(topic, 
partition, key1, value1) , callback);
	```
* flush()
	* Make sure message sent before is finished.	
	
* close()  
	* Close the connection with producer pool.

#### ConsumerRecords API

* public ConsumerRecord(string topic,int partition, long offset,K key, V value)


### 2. Usage

* Step 1: Compile
	
	```
	javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*" *.java
	```

* Step 2: Producer produce

	```
	java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*":. Producer test
	```
	
	Here, **test** is the topic I created before. Producer is the name of the producer java file.
	
* Step 3: Consumer consume

	```
	java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*":. SimpleConsumer <topic-name>
	```
	
	Consumer is the name of the consumer java file.

	