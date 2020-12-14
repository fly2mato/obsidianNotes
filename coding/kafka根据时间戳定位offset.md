- [参考java代码](https://github.com/YHYR/Kafka-Utils/blob/master/Kafka-Utils-Java/src/main/java/com/yhyr/comsumer/GetOffsetWithTimestamp.java)
- 需要kafka client 0.10.1.0 及以后版本

- pom依赖
```xml
<properties>
	<kafka.client.version>0.10.1.0</kafka.client.version>
</properties>
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>${kafka.client.version}</version>
</dependency>
```

- 实现代码
```scala
//依赖包
import com.google.gson._
import java.util.Properties
import org.apache.kafka.clients.consumer.KafkaConsumer
import org.apache.kafka.clients.consumer.OffsetAndTimestamp
import org.apache.kafka.common.TopicPartition
import scala.collection.JavaConverters._


def getOffsetWithTimestamp(startTimestamp: Long, endTimestamp: Long, servers: String, topic: String) = {
val props = new Properties()
props.put("bootstrap.servers", servers)
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer")
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer")

val kafkaConsumer = new KafkaConsumer(props)

val startTopicPartitions = kafkaConsumer.partitionsFor(topic)
  .asScala
  .map(partitionInfo => (new TopicPartition(partitionInfo.topic(), partitionInfo.partition()), startTimestamp.asInstanceOf[java.lang.Long]))
  .toMap
val endTopicPartitions = kafkaConsumer.partitionsFor(topic)
  .asScala
  .map(partitionInfo => (new TopicPartition(partitionInfo.topic(), partitionInfo.partition()), endTimestamp.asInstanceOf[java.lang.Long]))
  .toMap

val earliestOffSet = kafkaConsumer.beginningOffsets(startTopicPartitions.map(_._1).toList.asJava)
  .asScala
  .map( row => (row._1.partition(), row._2.toLong))
  .toMap
val latestOffSet = kafkaConsumer.endOffsets(endTopicPartitions.map(_._1).toList.asJava)
  .asScala
  .map( row => (row._1.partition(), row._2.toLong))
  .toMap

val startOffSet = earliestOffSet ++ kafkaConsumer.offsetsForTimes(startTopicPartitions.asJava)
  .asScala
  .map( row => (row._1.partition(), row._2.offset()))

val endOffSet = latestOffSet ++ kafkaConsumer.offsetsForTimes(endTopicPartitions.asJava)
  .asScala
  .filter(_._2 != null)
  .map( row => (row._1.partition(), row._2.offset()))


val gson = new Gson()
(gson.toJson(Map(topic -> startOffSet.asJava).asJava),
  gson.toJson(Map(topic -> endOffSet.asJava).asJava)
)
}


def main(args: Array[String]): Unit = {
Logger.getLogger("org.apache.spark").setLevel(Level.WARN)

val spark = SparkSession
  .builder()
  .master("local[4]")
  .getOrCreate()
spark.conf.set("spark.sql.broadcastTimeout", 36000)
spark.sparkContext.setLogLevel("WARN")

// For implicit conversions like converting RDDs to DataFrames
import spark.implicits._

val (startOffsetJsonString, endOffsetJsonString) = getOffsetWithTimestamp(1598284800000L, 2598326620000L, "localhost:9092", "test2")

val data = spark.read
  .format("kafka")
  .option("kafka.bootstrap.servers", "localhost:9092")
  .option("subscribe", "test2")
  .option("startingOffsets", startOffsetJsonString)
  .option("endingOffsets", endOffsetJsonString)
  .load()
  .selectExpr("CAST(value AS STRING)")

data.show(100, false)
}

```

- <mark>传入的时间戳是单位是ms</mark>





byte
```python
# -*- coding: UTF-8 -*-
from pykafkaclient.kafka_proxy2.kafka_proxy import KafkaProxy
import json
import time

cluster_name = 'kafka_risc_lf'  # cluster名称
topic = 'security_verification'  # topic名称
consumer_group = 'rc_alg_test'  # consumer group名称
search_time = '2020-09-29 18:25:00'  # 指定的时间，字符串类型
# search_time = 1562564700000        # 指定的时间，linux时间戳，数字类型

client = KafkaProxy(cluster_name=cluster_name, topic=topic, consumer_group=consumer_group,
                    psm='toutiao.webarch.whale_add_rules', owner="zhangbo.39", team='rc_alg_test')

result = client.offsets_for_times(search_time)
consumer = client.get_kafka_consumer()

for topic_partition, offset_and_timestamp in result.items():
    consumer.seek(topic_partition, offset_and_timestamp.offset)
consumer.update_positions()
consumer.commit()

offsets = {}
for partition, _ in result.items():
    offset = consumer.committed(partition)
    offsets[partition.partition] = offset

print('New Offset: ')
print(offsets)

while True:
    kafka_msg_list = client.fetch_msgs(count=100)

    if not kafka_msg_list:
        time.sleep(30)
        print(int(time.time()))
        continue

    for msg_str in kafka_msg_list:
        pass
```