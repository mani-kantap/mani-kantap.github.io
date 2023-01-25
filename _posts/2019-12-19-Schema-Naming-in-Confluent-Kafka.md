---
title: Schema naming strategies in Confluent Schema Registry
author: manikanta
date: 2021-12-19 14:10:00 +0800
categories: [Resume, Info]
tags: [writing]
render_with_liquid: false
---
Orginally Published @ https://maniii.medium.com/schema-naming-strategies-in-confluent-schema-registry-cb358a3aecb9


Kafka is a distributed streaming platform. Irrespective of the data type it is streaming; it just streams bytes. Due to this, it becomes a tedious task to manage the data contract between producer and consumer. Schema registry helps in maintaining the data structure contract.

A schema defines the structure of the data format. Confluent schema registry provides a RESTful API for managing schemas. It supports schema serialization in AVRO, JSON, and Protocol Buffers. It stores the schema under a scope which is called a Subject. The name of the subject depends on the configured subject name strategy; the default strategy is TopicNameStrategy.

Schema can be accessed at 
```
{schema_registry_host_url}/subjects/logs-value/version/1
```
As of now, Kafka supports TopicNameStrategy, RecordNameStrategy and TopicRecordName strategy.

### TopicNameStrategy :

This is the default subject naming strategy and it uses the topic name to determine the subject to be used for schema lookups.

Record Object: Customer

Topic: my_kafka_topic

Schema (subject name) : <topic name>-value, my_kafka_topic-value

This strategy enforces to follow only one schema per topic. As the subject name is derived from topic, if we are publishing the same event to different topics then it’ll have different schemas.

### RecordNameStrategy:

It names subjects independent of the topic, the name would be a fully qualified record name.

Record Object: com.example.customer

Topic: my_kafka_topic
```
Schema (subject name) : <fully-qualified record name >, com.example.customer
```

As this subject name is derived from the record name, even though it is published to multiple topics schema registry would get only one subject. As the schema is not binded to the topic, a single topic can contain multiple schema messages.

### TopicRecordNameStrategy:

It derives the subject name based on the topic and record name.

Record Object: com.example.customer

Topic: my_kafka_topic
```
Schema (subject name) : <topic name>-<fully-qualified record name >, my_kafka_topic-com.example.customer
```

As this subject name is derived from the topic and record name when a record is published it gets a different subject for every topic. Similar to record name strategy, a single topic can contain multiple schema records.

### References:

https://docs.confluent.io/platform/current/schema-registry/serdes-develop/index.html#schema-format-extensibility

https://www.confluent.io/blog/multiple-event-types-in-the-same-kafka-topic/


